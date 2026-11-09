---
name: python-architecture
description: "Chooses and records Python architecture for a new project, a material redesign, or an incremental legacy refactor. Use before major implementation to identify system shape, constraints, boundaries, patterns, exceptions, and the next improvement. Requires python-standards and python-style-guide. Uses technical-writing for the architecture record."
metadata:
  version: "2.0.0"
---

# Python architecture

Read `python-standards` and `python-style-guide` before making architecture decisions. Load `technical-writing` before creating or changing the architecture record.

Use this skill for greenfield work, material redesigns, and legacy refactors. Do not force a full architecture exercise onto a narrow bug fix.

## Start with evidence

Inspect the repository before choosing a pattern. Record:

- system shape: API, CLI, library, pipeline, worker, event-driven, real-time, or hybrid
- consumers and entry points
- operation duration and concurrency
- trust boundaries and data sensitivity
- availability, latency, throughput, consistency, offline, and residency constraints
- deployment and operational environment
- current package and dependency boundaries
- whether the work is greenfield or legacy

Use `unknown` for missing evidence. Do not invent a scale, SLO, consumer, or regulation.

## Principles

### Dependencies point toward policy

Domain decisions must not import database, HTTP, CLI, cloud, or file-system implementations. Define small ports only where the domain needs to vary or where testing a real boundary would be expensive.

Do not wrap every library in an interface. A wrapper must protect a domain boundary, a compatibility surface, or a test seam.

### Side effects stay visible

Keep calculation and policy pure where practical. Put I/O in adapters and orchestrate it at a clear application entry point. A function that performs I/O should make that fact clear through its layer, name, contract, or injected dependency.

### Dependencies are explicit where they vary

Inject replaceable collaborators, clocks, randomness, clients, repositories, and mutable state.

Process-wide facilities may be created once at the composition root when they are configuration or instrumentation rather than business collaborators. Examples include a configured logger factory, tracer provider, metrics provider, and immutable registry. Domain code must not configure them or depend on vendor-specific implementations.

### Each operation has one entry point

A command, route, job, or event type should have one obvious orchestration path. Shared domain logic may serve several entry points, but entry points must not call each other to reuse behavior.

### Configuration is loaded once

Read ordinary configuration and injected secret values at startup. Validate them and pass typed settings inward. Keep secret values out of files as required by `python-style-guide`.

## Choose only needed patterns

| Situation | Default | Add only when |
|---|---|---|
| API or service | application service plus adapters | a domain use case needs orchestration |
| Persistent domain model | repository port | storage must vary or domain logic must remain storage-free |
| Multi-step transaction | unit of work | one atomic boundary spans several repository actions |
| CLI | one command handler plus adapters | complexity justifies a separate application service |
| Pipeline | extract, transform, load stages | restart cost justifies checkpoints |
| Event consumer | typed event, idempotent handler, retry and dead-letter policy | duplicate or poison messages are possible |
| Value with invariants | immutable value object | primitive values cannot safely express the rules |
| CQRS or event sourcing | no | measured read/write divergence or replay requirements justify the cost |

Record rejected patterns when a future maintainer may reasonably ask why they were not used.

## Legacy refactor mode

Do not describe a legacy system as if it already meets the target architecture.

1. Map current entry points, imports, state, and side effects.
2. List current exceptions with evidence and risk.
3. Choose the smallest boundary that reduces coupling or risk in this change.
4. Define the target state for that boundary.
5. Record a next improvement that can be completed independently.
6. Add characterization tests before moving behavior across a boundary.

A temporary adapter or facade is acceptable when it creates a stable seam. Give each temporary exception a removal condition, not an invented deadline.

## Architecture record location

Store the record at the repository's configured architecture or ADR path. Default to `docs/architecture/ARCHITECTURE.md` when no convention exists.

If a tool requires root `ARCHITECTURE.md`, make it a short link to the canonical document under `docs/` rather than maintaining two copies. If the user requires another path under `docs/`, use that path and record it in `record_path`.

## Required decision record

Use this YAML as the machine-readable core of the Markdown architecture document. Add reader-focused explanation around it only where needed.

```yaml
architecture:
  schema_version: 2
  project: <name>
  recorded_at: <ISO 8601 UTC timestamp>
  last_reviewed_at: <ISO 8601 UTC timestamp>
  record_path: <path under docs>
  mode: <greenfield | legacy_refactor>
  system_shape: <api | cli | library | pipeline | worker | event_driven | realtime | hybrid>

  problem: <what is being built or changed, for whom, and why>
  consumers:
    - <consumer>
  entry_points:
    - <command, route, job, event, or API>
  operation_duration: <milliseconds | seconds | minutes_plus | mixed | unknown>

  constraints:
    - id: C1
      statement: <testable constraint>
      source: <user, repository path, measurement, or unknown>

  principles:
    - dependencies point toward policy
    - side effects stay visible
    - variable dependencies are explicit
    - one entry point per operation
    - configuration is loaded once

  patterns:
    - name: <pattern>
      used: <true | false>
      reason: <specific problem it solves or why it is unnecessary>

  boundaries:
    - name: <domain or adapter boundary>
      path: <repository path>
      owns: <responsibility>
      may_depend_on:
        - <boundary>

  process_wide_facilities:
    - name: <logger, tracer, metrics, or immutable registry>
      configured_at: <composition-root path>
      domain_dependency: <none or abstract contract>

  current_exceptions:
    - id: E1
      applies_to: <path or boundary>
      reason: <legacy fact or accepted trade-off>
      risk: <specific risk>
      removal_condition: <observable condition>

  rejected:
    - pattern: <pattern>
      reason: <why it does not fit current evidence>

  trade_offs:
    - <cost accepted for a named benefit>

  next_boundary:
    target: <next independently useful improvement or none>
    completion_signal: <how reviewers know it is complete>
```

For greenfield work, use `current_exceptions: []` when there are no exceptions. For legacy work, include real exceptions and a real `next_boundary`.

## Validation

Before implementation:

- every constraint has a source or says `unknown`
- every selected pattern solves a named problem
- boundaries have clear ownership and allowed dependency direction
- process-wide facilities are configured at a composition root
- legacy exceptions match the current code
- operational controls are proportional under `python-style-guide`
- the record is under `docs/`, timestamped, and formatted for review under `technical-writing`

After a material change, update `last_reviewed_at`, the affected decision, exceptions, and review history. Do not rewrite the record for an unrelated code change.
