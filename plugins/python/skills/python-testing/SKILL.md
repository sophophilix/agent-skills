---
name: python-testing
description: "Designs, writes, and evaluates Python tests with pytest, coverage, contract tests, property tests, and optional adapter integration. Use for behavior changes, test infrastructure, flaky tests, coverage policy, or test quality assessment. Requires python-standards and python-style-guide. Uses technical-writing for saved test plans and reports."
metadata:
  version: "2.0.0"
---

# Python testing

Read `python-standards` and `python-style-guide` first. Load `technical-writing` when saving a test plan, benchmark report, or quality report.

A change is tested when the suite proves its public behavior, important failures, and boundary conditions at the cheapest reliable level.

## Test levels

| Level | Purpose | Normal dependencies |
|---|---|---|
| Unit | Pure decisions, validation, state changes | Memory and simple fakes |
| Contract | A port and every adapter obey the same behavior | Shared contract suite, adapter fixture |
| Integration | Real database, parser, filesystem, queue, or service sandbox | Local or containerized dependency |
| End to end | A critical workflow through deployed entry points | Production-like system |

Keep most tests at unit and contract level. Add integration and end-to-end coverage where a boundary can fail in ways a fake cannot represent.

## Layout and discovery

Prefer a `src/` package layout and tests outside production packages:

```text
src/package_name/
tests/
  unit/
  contract/
  integration/
  e2e/
```

Mirror source concepts where that helps discovery. Use `--import-mode=importlib` for new projects. Name tests for observable behavior, such as `test_expired_token_returns_401`.

Register markers in `pyproject.toml`:

```toml
[tool.pytest.ini_options]
addopts = "-ra --strict-markers --tb=short --import-mode=importlib"
markers = [
  "integration: requires a real local or sandbox dependency",
  "e2e: requires a deployed system",
  "heavy: requires a model, accelerator, or native runtime",
]
```

## Test design

Use Arrange, Act, Assert as a reading aid, not a requirement for comments.

Test:

- public contracts and user-visible output
- error types, messages when contractual, and exit status
- empty, missing, zero, negative, maximum, malformed, and oversized inputs as relevant
- authorization and security-sensitive decisions
- state transitions and idempotency
- timeout, cancellation, retry, and cleanup behavior at external boundaries
- regressions with the smallest example that failed

Do not test framework internals, trivial accessors, or private methods directly. Reach private behavior through a public contract unless the design needs a smaller public unit.

One test should explain one behavior. Multiple assertions are appropriate when they describe the same result.

## Fixtures and test data

- Scope fixtures as narrowly as practical.
- Use `yield` fixtures or context managers for deterministic cleanup.
- Name a fixture for what it provides.
- Keep shared fixtures near the tests that share them.
- Build the smallest valid object, then override the field relevant to the test.
- Keep committed examples synthetic or explicitly licensed and free of secrets and personal data.

## Doubles and mocks

Prefer a small fake that implements a port. Mock at a process boundary, such as HTTP, time, randomness, a cloud SDK, or a subprocess.

- Use a spec or autospec where the framework supports it.
- Assert important arguments and calls, not every incidental interaction.
- Do not patch an internal function merely to make the test easier.
- More than two unrelated mocks in one test is a design signal, not an automatic failure.
- Simulate protocol-level failures, including timeout, malformed response, rate limit, and unavailable dependency.

## Optional and heavy adapters

A plugin may require a large model, GPU, native library, paid sandbox, or unavailable runtime. Its absence does not remove the testing obligation.

Minimum merge gate:

1. Run the shared adapter contract against a lightweight fake or deterministic fixture.
2. Test orchestration with the model or native boundary replaced, including request shape and result conversion.
3. Test the unavailable-dependency path and its actionable error.
4. Test parsing and validation with representative saved outputs, subject to licensing and privacy rules.
5. Mark the real integration `integration` and `heavy`. Run it in a capable CI job, scheduled job, or documented pre-release environment.

Record real integration status as `pass`, `fail`, or `unavailable`. Never report a mocked test as proof that the external runtime works.

## Property and differential tests

Use Hypothesis for stable invariants such as parser round trips, normalization idempotency, numeric properties, schema constraints, and path handling.

Use differential tests when replacing an implementation and the previous behavior is the intended contract. Remove or revise the comparison when the old implementation is no longer authoritative.

## Determinism and time

- Do not depend on test order or shared mutable state.
- Seed randomized examples when reproducibility matters. Let Hypothesis manage and report its own examples.
- Inject clocks and randomness for domain tests.
- Do not use `sleep()` to coordinate a test. Use events, fake clocks, polling with a strict deadline, or framework synchronization.
- Give every network or process wait a timeout.
- Treat a flaky test as a defect. Quarantine only with an owner when known, a tracking item, and a removal condition.

## Coverage policy

Coverage is evidence of exercised code, not evidence of correct assertions.

- Require branch coverage.
- Require at least 80 percent line coverage for each publishable package, not only an aggregate `src/` number.
- Set a higher threshold for security-sensitive or business-critical packages when the repository records it.
- Measure every package in a monorepo or plugin tree in its own CI job or explicit coverage target.
- Do not omit a hard package from measurement to preserve the aggregate percentage.
- Do not add assertion-free tests to raise coverage.

Configure import package names explicitly:

```toml
[tool.coverage.run]
branch = true
source_pkgs = ["core_package", "plugin_package"]

[tool.coverage.report]
fail_under = 80
show_missing = true
```

If packages have separate test suites, enforce each threshold separately:

```bash
uv run --frozen pytest tests/core --cov=core_package --cov-branch --cov-fail-under=80
uv run --frozen pytest plugins/example/tests --cov=example_plugin --cov-branch --cov-fail-under=80
```

Use repository paths and import package names. Do not copy the placeholders.

## Required checks for a change

1. Run the focused tests while developing.
2. Run the complete affected package suite.
3. Run all repository tests when shared contracts, configuration, or public APIs changed.
4. Run the package coverage target.
5. Run the locked lint, format, type, and security commands from `python-standards`.
6. Report skipped, unavailable, and failed checks exactly.

## Review checklist

- Does each changed behavior have a test that would fail without the change?
- Are meaningful failure and boundary paths covered?
- Are doubles placed at real boundaries?
- Are cleanup and timeouts deterministic?
- Does every publishable package have an enforced coverage target?
- Do optional adapters meet the minimum contract gate?
- Are heavy integration results distinguished from mocked results?
- Can a failed test tell the next developer what contract broke?
