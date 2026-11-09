---
name: python-style-guide
description: "Guides production Python authoring and refactoring across API design, typing, docstrings, errors, boundaries, security, observability, resources, and proportional SRE controls. Use when writing or changing Python. Requires python-standards. Use technical-writing for project prose."
metadata:
  version: "2.0.0"
---

# Python style guide

Read `python-standards` first. It owns tools, commands, dependency policy, security floors, and the log schema. Load `technical-writing` for any documentation produced during the task.

PEP 8, PEP 257, the typing specification, and repository conventions apply where this guide is silent. Preserve a consistent local style during legacy work unless a focused refactor is part of the task.

## Design for readers

- Keep modules focused around one responsibility.
- Prefer small functions with explicit inputs and return values.
- Separate pure decisions from I/O and framework concerns.
- Use guard clauses to keep the main path visible.
- Avoid abstractions until at least two real callers need the same contract.
- Preserve public behavior unless the task includes a migration.

## Formatting and names

Let Ruff own formatting and import order.

- Use four spaces and a line length of 88.
- Use double quotes unless an automated formatter preserves another valid form.
- Add trailing commas to multiline structures.
- Use `snake_case` for functions, methods, variables, and modules.
- Use `PascalCase` for classes and exceptions.
- Use `UPPER_SNAKE_CASE` for constants.
- Use a leading underscore for internal names.
- Choose a short domain name over a type or history name. Prefer `scores` to `scores_df_cleaned_v2`.
- Keep one-letter names to conventional indexes, coordinates, and very small expressions.

## Public APIs and typing

Type every public function and method, including `-> None`.

- Use native generics such as `list[str]` and unions such as `str | None`.
- Use `Protocol` for behavioral contracts when inheritance is not required.
- Use `TypedDict` only for dictionary-shaped interchange data. Prefer dataclasses or Pydantic models for objects with behavior or validation.
- Avoid `Any`. Contain unavoidable untyped third-party values at an adapter and convert them to a known type.
- Return one stable type. Do not use `None`, empty containers, and exceptions to mean the same condition.
- Treat exported names, exceptions, CLI output, serialized fields, and entry points as compatibility surfaces.

## Docstrings and comments

Document public modules, classes, functions, and methods in Google style unless the repository states otherwise.

```python
def calculate_retry_delay(attempt: int, base: float = 1.0) -> float:
    """Calculate an exponential retry delay with jitter.

    Args:
        attempt: Zero-based retry attempt number.
        base: Base delay in seconds.

    Returns:
        Delay in seconds.

    Raises:
        ValueError: If attempt is negative.
    """
```

- Start with an imperative summary that ends in a period.
- Add `Args`, `Returns`, and `Raises` only when they add information.
- Explain contracts, units, side effects, and surprising constraints.
- Do not repeat the signature or narrate the implementation.
- Use comments for why a choice exists, not what the next line does.

## Data boundaries and validation

Choose a parser that understands the wire or file format, then validate the parsed values against typed domain or boundary models.

- Use Pydantic for structured external data such as TOML-derived mappings, JSON, form data, and API payloads.
- Parse binary files, MIME messages, archives, XML, and specialized formats with a format-aware parser first.
- Validate the parser's structured output before it reaches business logic.
- Decide whether coercion is safe. Use strict validation for identifiers, security controls, money, and fields where conversion can hide an error.
- Set the extra-field policy deliberately. Pydantic ignores extra fields by default.
- Enforce size, nesting, count, and time limits before or during parsing of untrusted data.
- Convert infrastructure types, such as ORM rows and HTTP objects, at the boundary.

## Configuration and secrets

Load configuration once at the composition root and validate it before work begins.

A TOML or other config file may contain non-secret settings, including paths, limits, feature flags, endpoint names, and the name of an environment variable that will hold a secret. It must not contain the secret value.

Deliver secret values at startup through Infisical or the approved launcher. Pass a typed settings object inward. Do not scatter `os.getenv()` calls through the codebase.

## Errors

- Raise the narrowest useful built-in or project exception.
- Root project exceptions in one documented base class when callers need a stable catch point.
- Include safe context about the failed operation, not secrets or unrestricted payloads.
- Chain translated exceptions with `raise DomainError(...) from exc`.
- Keep `try` blocks narrow. Use `else` when it prevents unrelated exceptions from being caught.
- Catch `Exception` only at an outer boundary under the rule in `python-standards`.
- Do not both log and re-log the same failure at every layer. Log where the system adds operational context or decides the outcome.

## Resource management

Prefer a native context manager. If a third-party object only exposes `close()`, use `contextlib.closing()`. If neither abstraction fits, use a narrow `try/finally` that guarantees cleanup.

Use `ExitStack` or `AsyncExitStack` when the number of resources is dynamic. Do not rely on garbage collection for files, locks, sessions, temporary directories, or network connections.

## Logging and observability

Applications use the structured log contract from `python-standards`. Libraries emit through a logger compatible with the host and do not configure handlers at import time.

- Use stable snake-case event names.
- Log outcomes and state transitions, not a message for every line of execution.
- Sanitize untrusted event data to prevent log injection.
- Include trace context when a trace exists.
- Keep stdout for a CLI's documented data output and stderr for diagnostics.
- Never expose a secret or sensitive personal value to make an error easier to debug.

## Proportional reliability controls

Apply controls according to how the code runs.

All network and subprocess boundaries:

- set an explicit timeout
- classify failures as transient or permanent
- retry only safe or idempotent operations
- use bounded exponential backoff with jitter
- test timeout and cancellation behavior

Long-running services, workers, and pipelines:

- emit latency, throughput, and error signals for important operations
- make background work idempotent or define deduplication
- implement graceful shutdown
- expose health or readiness checks that reflect required dependencies
- use a circuit breaker when repeated downstream failures can amplify load or exhaust resources

Small local CLIs and libraries:

- do not add a metrics backend, health endpoint, tracer provider, or circuit breaker by default
- add them only for a stated SLO, repeated remote dependency, long-running operation, or host integration
- still return documented exit codes and clean up resources

## Security in implementation

- Parameterize every database query.
- Use `secrets` for security tokens and `random` only for simulation or non-security behavior.
- Pass subprocess arguments as a sequence and avoid a shell.
- Normalize and authorize filesystem paths before access. Set restrictive permissions for sensitive output.
- Use maintained cryptographic libraries and established protocols.
- Validate authorization at the operation boundary, not only in the user interface.

## Performance and concurrency

- Measure before optimizing with a profiler or benchmark that represents real input.
- Prefer clear built-in collections and algorithms.
- Stream data when inputs can exceed memory.
- Avoid N+1 I/O patterns.
- Use async only for concurrent I/O with an async-compatible stack.
- Never call blocking I/O directly on an event loop.
- Make shared mutable state explicit and synchronized, or remove it.

## Completion check

Before finishing a code change:

1. Run the relevant locked quality commands from `python-standards`.
2. Add or update tests under `python-testing`.
3. Confirm boundary validation, timeout, cleanup, logging, and secret handling.
4. Update user-facing documentation through `technical-writing` when behavior or operation changed.
5. Report commands that were not run instead of implying they passed.
