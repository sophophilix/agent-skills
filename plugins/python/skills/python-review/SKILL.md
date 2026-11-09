---
name: python-review
description: "Reviews Python files, changes, pull requests, or whole repositories against the shared standards, style, testing, security, and accepted architecture. Use for audits and quality assessments. Requires python-standards, python-style-guide, and python-testing. Always returns the structured YAML schema in this skill. Uses technical-writing when saving a review document."
metadata:
  version: "2.0.0"
---

# Python review

Read `python-standards`, `python-style-guide`, and `python-testing` before reviewing. Read the repository's architecture record and local instructions when present. Load `technical-writing` only when the review will be saved as a document.

Review the code, not the author. A finding must cite a rule or be labeled as a suggestion.

## Establish scope and evidence

Set scope to one of:

- `file` for one path
- `multiple` for a named set of paths or a diff
- `repository` for a broad audit

Inspect the diff and enough surrounding code to understand contracts and call sites. Record the commit when available. Never infer that a gate passed from an old report or a config file.

## Run gates

Use repository commands first. Otherwise use the locked commands from `python-standards`.

Record each gate as:

- `pass`: the command ran and met policy
- `fail`: the command ran and did not meet policy
- `unavailable`: the command, dependency, environment, or configuration was missing
- `not_run`: the reviewer did not run it
- `not_applicable`: the gate does not apply to the reviewed scope, with a reason

A failed or unavailable mandatory gate makes the verdict `rejected`. Continue the static review and preserve useful findings. Mark a finding `unverified` when the failed gate prevents confirmation. Never turn known findings into an empty list because a tool failed.

## Review order

### 1. Security floor

Check the prohibited behavior in `python-standards`, including secrets, unsafe SQL, dynamic execution, pickle, shell use, sensitive logs, mutable defaults, bare catches, and swallowed security results.

Confirm that any `except Exception` is at a real outer boundary and has a defined outcome.

### 2. Correctness and contracts

Check behavior before style:

- invalid states and boundary values
- data loss, partial writes, and non-atomic transitions
- incorrect exception or exit behavior
- compatibility of public APIs, schemas, entry points, and CLI output
- concurrency, cancellation, ordering, and idempotency
- cleanup on success and failure

### 3. Typed boundaries and configuration

Check that format-aware parsers handle binary and specialized formats before typed validation. Check Pydantic coercion and extra-field policy where they affect correctness.

Confirm that ordinary settings are separate from secret values, settings are loaded once, and required secret values are injected and validated at startup.

### 4. Error handling

Check exception specificity, narrow `try` blocks, chaining, safe context, duplicate logging, and whether permanent failures are surfaced rather than retried.

### 5. Observability and reliability

Apply the proportional rules in `python-style-guide`.

- All external calls need timeouts.
- Retries must be bounded, jittered, and safe for the operation.
- Services and long-running work need appropriate signals and shutdown behavior.
- Small local CLIs do not need metrics or circuit breakers without a stated reason.
- Structured levels must match the uppercase shared schema in final rendered logs.

### 6. Tests

Check that tests prove changed behavior, meaningful failures, and edge cases. Confirm deterministic cleanup, boundary-level doubles, optional adapter contracts, and distinct heavy integration status.

Coverage must target every publishable package. An aggregate core result does not verify a plugin package.

### 7. Architecture

Discover the canonical record from repository instructions or common paths such as `docs/architecture/ARCHITECTURE.md`, `docs/decisions/`, and a root link.

Check dependency direction, visible side effects, entry points, composition-root facilities, selected patterns, current legacy exceptions, and the next boundary. Use `no_record` when there is no applicable accepted record.

### 8. Maintainability and performance

Check public typing, useful docstrings, naming, blocking calls in async paths, N+1 I/O, unbounded collections, and complexity that obscures the contract.

A style preference that is not a repository rule is a suggestion, not a merge blocker.

## Severity and verdict

- `must_fix`: blocks merge. Includes security issues, correctness defects, broken public contracts, failed mandatory gates, and missing required types.
- `should_fix`: should be fixed before the next release. Includes enforceable maintainability, test, documentation, and observability gaps.
- `suggestion`: advice without a binding rule.

Use `rejected` when a mandatory gate failed or was unavailable, or a critical security issue makes further verification unsafe. Use `changes_requested` for verified must-fix findings with completed gates. Use `approved` only when no must-fix finding remains and mandatory gates pass.

## Required output

Return only this YAML for the review itself. Use `[]` for no findings. Quote values when YAML could interpret them as another type.

```yaml
review:
  schema_version: 2
  reviewed_at: <ISO 8601 UTC timestamp>
  scope:
    kind: <file | multiple | repository>
    paths:
      - <path or ".">
  commit: <short hash or "none">
  verdict: <approved | changes_requested | rejected>

  gates:
    ruff_lint:
      status: <pass | fail | unavailable | not_run | not_applicable>
      evidence: <command and concise result>
    ruff_format:
      status: <pass | fail | unavailable | not_run | not_applicable>
      evidence: <command and concise result>
    mypy:
      status: <pass | fail | unavailable | not_run | not_applicable>
      evidence: <command and concise result>
    bandit:
      status: <pass | fail | unavailable | not_run | not_applicable>
      evidence: <command and concise result>
    semgrep:
      status: <pass | fail | unavailable | not_run | not_applicable>
      evidence: <command and concise result>
    tests:
      status: <pass | fail | unavailable | not_run | not_applicable>
      evidence: <command and concise result>
    coverage:
      status: <pass | fail | unavailable | not_run | not_applicable>
      evidence: <package targets and percentages, or reason unavailable>
    dependency_audit:
      status: <pass | fail | unavailable | not_run | not_applicable>
      evidence: <command and concise result>

  findings:
    - id: F1
      path: <path>
      line: <line, range, or "n/a">
      severity: <must_fix | should_fix | suggestion>
      verification: <verified | unverified>
      rule: <specific skill section, repository rule, or "suggestion">
      what: <one problem and its impact>
      fix: <specific corrective action>

  architecture:
    record: <path or "none">
    compliant: <true | false | no_record>
    drift: <concise drift statement or "none">

  summary:
    must_fix: <count>
    should_fix: <count>
    suggestion: <count>
    blocked_checks:
      - <failed or unavailable mandatory gate, or none>
```

## Output validation

Before returning:

1. Get `reviewed_at` from the environment in UTC.
2. Confirm scope covers every reviewed path.
3. Confirm gate evidence names what actually ran.
4. Keep known findings when a gate fails and mark uncertainty precisely.
5. Give each finding one issue, one rule, and one actionable fix.
6. Recount the summary.
7. Parse the YAML when a parser is available.
8. If saving the review, place it under `docs/` and apply `technical-writing` without changing the YAML schema.
