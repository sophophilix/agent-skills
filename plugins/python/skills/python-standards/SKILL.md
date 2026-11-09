---
name: python-standards
description: "Defines the shared Python toolchain, dependency policy, quality gates, security floor, logging schema, and configuration ownership. Use for any Python code task or when another Python skill requires a central rule. Use technical-writing for any documentation produced during the task."
metadata:
  version: "2.0.0"
---

# Python standards

This skill is the source of truth for Python tooling and cross-project rules. The architecture, style, testing, and review skills should reference it instead of repeating it.

For project prose, also load `technical-writing`. It invokes `humanizer` and owns document location, timestamps, review metadata, accessibility, and writing quality.

## Rule precedence

Apply rules in this order:

1. Explicit user or organization requirements
2. Repository configuration and accepted architecture decisions
3. This skill and the other Python skills
4. PEP 8 and tool defaults

Do not weaken the security floor silently. Record a required exception, its scope, risk, owner when known, and removal condition in the project's decision record.

## Runtime and layout

- Target Python 3.11 or newer. Respect a repository that targets a newer version.
- Use `from __future__ import annotations` when it improves forward compatibility or resolves annotation ordering. Do not add it mechanically to files that do not need it.
- Prefer a `src/` layout for new distributable packages.
- Keep project metadata and supported tool configuration in `pyproject.toml`.
- Use `uv` for environments, dependency resolution, locking, and command execution.

## Supported toolchain

| Purpose | Default | Required use |
|---|---|---|
| Format and lint | Ruff | All maintained projects |
| Type checking | mypy strict | Production packages and public APIs |
| Tests and coverage | pytest, pytest-cov, coverage.py | All behavior changes |
| Security lint | Ruff S rules and Bandit | Production packages |
| SAST | Semgrep with repository rules | CI for maintained products |
| Dependency audit | pip-audit | CI and release checks |
| Structured logging | structlog | Applications and services |
| Tracing | OpenTelemetry | Networked services and traced pipelines |
| Package management | uv | All projects |
| Git hooks | pre-commit | Maintained repositories |
| Secret delivery | Infisical by organization default | Workloads that use secrets |

An accepted architecture decision may select an equivalent vendor or omit an operational tool that does not fit a small local library or script. Ruff, typing, tests, and secure dependency handling remain the baseline.

Experimental tools, including model-based vulnerability scanners and AI reviewers, are advisory. They must not be mandatory gates until the team defines a supported version, command, failure policy, and owner.

## Dependency and version policy

- Declare compatible runtime ranges in `pyproject.toml`.
- Commit `uv.lock` for applications and reproducible development environments. The lockfile owns exact resolved versions.
- For published libraries, declare honest lower bounds, test supported Python versions and important lower bounds, and do not rely on the lockfile as the consumer contract.
- Put quality and test tools in named dependency groups.
- Run quality commands through the locked uv environment. Do not maintain a second set of Python tool versions in pre-commit.
- Upgrade intentionally with `uv lock --upgrade-package <name>` or a reviewed full lock refresh.

Canonical dependency audit for a uv project:

```bash
requirements_file="$(mktemp)"
trap 'rm -f "$requirements_file"' EXIT
uv export \
  --frozen \
  --no-dev \
  --no-emit-project \
  --no-hashes \
  --format requirements.txt \
  --output-file "$requirements_file"
uv run --frozen pip-audit \
  --strict \
  --no-deps \
  --requirement "$requirements_file"
```

The export audits exact locked runtime dependencies without asking pip-audit to resolve a second graph. Current pip-audit `--locked` project discovery supports `pyproject.toml` and `pylock.*.toml`, not `uv.lock`, so do not use `pip-audit --locked .` as a uv audit.

`pip-audit` must be present in the quality dependency group. A vulnerability waiver must name the advisory, affected scope, compensating control, owner when known, and expiry or review date.

## Canonical quality commands

Adapt package paths to the repository, but keep one command source for local use and CI:

```bash
uv sync --frozen
uv run --frozen ruff check .
uv run --frozen ruff format --check .
uv run --frozen mypy <package-roots>
uv run --frozen bandit -c pyproject.toml -r <package-roots>
uv run --frozen semgrep scan --config .semgrep.yml <package-roots>
uv run --frozen pytest
# Run the canonical exported-lock dependency audit above.
```

CI records the command and result. A missing tool or configuration is `unavailable`, not a pass.

## Pre-commit ownership

Pin non-Python hygiene hooks by release. Invoke Python quality tools as local hooks through `uv run --frozen`, so local hooks and CI use the exact versions in `uv.lock`.

```yaml
repos:
  - repo: local
    hooks:
      - id: ruff-check
        name: ruff check
        entry: uv run --frozen ruff check --fix
        language: system
        types_or: [python, pyi]
      - id: ruff-format
        name: ruff format
        entry: uv run --frozen ruff format
        language: system
        types_or: [python, pyi]
      - id: mypy
        name: mypy
        entry: uv run --frozen mypy
        language: system
        pass_filenames: false
        args: [<package-roots>]
```

Run Bandit, Semgrep, tests, and dependency audit in CI. Add them to pre-commit only when their latency and dependencies fit the repository.

## Minimum `pyproject.toml` decisions

Use repository package names and roots rather than copying placeholders.

```toml
[tool.ruff]
line-length = 88
target-version = "py311"

[tool.ruff.lint]
select = ["E", "F", "I", "N", "W", "B", "S", "UP", "C4", "SIM", "RUF", "D"]
ignore = ["E501"]

[tool.ruff.lint.pydocstyle]
convention = "google"

[tool.mypy]
python_version = "3.11"
strict = true
warn_return_any = true
warn_unused_configs = true
ignore_missing_imports = false

[tool.pytest.ini_options]
testpaths = ["tests"]
addopts = "-ra --strict-markers --tb=short --import-mode=importlib"

[tool.coverage.run]
branch = true
source_pkgs = ["your_package"]

[tool.coverage.report]
fail_under = 80
show_missing = true

[tool.bandit]
exclude_dirs = ["tests"]
skips = ["B101"]
```

Document every ignore with a narrow scope and reason. Prefer per-file ignores over global ignores.

## Structured log contract

Applications emit these fields. Libraries may use the host application's logging facade and must not configure global handlers on import.

| Field | Requirement | Meaning |
|---|---|---|
| `timestamp` | Always | ISO 8601 UTC |
| `level` | Always | `DEBUG`, `INFO`, `WARNING`, `ERROR`, or `CRITICAL` |
| `event` | Always | Stable snake-case event name |
| `service` | Always for applications | Service or application name |
| `trace_id` | When a trace exists | OpenTelemetry trace ID |
| `span_id` | When a span exists | OpenTelemetry span ID |
| `error` | On failures | Safe exception type and message |
| `duration_ms` | On measured operations | Elapsed milliseconds |

Structlog level processors commonly receive lowercase method names. Normalize after adding the level:

```python
from typing import Any

import structlog


def uppercase_log_level(
    _logger: Any,
    _method_name: str,
    event_dict: dict[str, Any],
) -> dict[str, Any]:
    """Normalize the structured log level to the shared schema."""
    level = event_dict.get("level")
    if isinstance(level, str):
        event_dict["level"] = level.upper()
    return event_dict


processors = [
    structlog.processors.add_log_level,
    uppercase_log_level,
    structlog.processors.TimeStamper(fmt="iso", utc=True),
]
```

Test the final rendered event, not only the processor in isolation.

## Security floor

Never:

- commit or hardcode credentials, tokens, private keys, or real secret values
- store real secrets in `.env` or TOML files
- log secrets, access tokens, sensitive personal data, or unrestricted request bodies
- interpolate external values into SQL
- use `shell=True` with external or variable input
- deserialize untrusted pickle data
- execute untrusted input with `eval`, `exec`, or `compile`
- use mutable default arguments
- use wildcard imports except a documented public re-export
- silently discard a security-sensitive return value or exception
- use a bare `except:`

Catch `Exception` only at a process, worker, task, or framework boundary where the handler logs safely and then re-raises, maps the failure to an exit status, or returns a defined protocol error. Catch specific exceptions inside application logic.

Store ordinary settings and secret references in config files. Deliver secret values at startup through Infisical injection or an approved equivalent. Validate required configuration before accepting work.
