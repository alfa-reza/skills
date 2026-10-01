# Audit lenses

Use only relevant lenses. Treat each as investigative prompts, not automatic findings. Follow actual runtime paths and supported environments; cross subsystem boundaries as needed to prove a claim.

## Correctness and regressions

- Check boundaries, empty/null states, parsing, precision, locale/time zones, ordering, state transitions, and inconsistent behavior across callers.
- Compare documented or tested contracts with implementation. Trace failures and downstream consequences; distinguish an introduced regression from an existing defect.

## Security and privacy

- Trace untrusted input and authority to the enforcement point and sensitive operation. Check authentication, authorization, tenant isolation, injection, deserialization, sensitive logs, defaults, credentials, and dependency risks.
- Establish realistic reachability, prerequisites, attacker capabilities, and effective mitigations. Authentication alone does not prove authorization or safety; a scanner match alone does not establish exposure.
- Check relevant dependency versions and advisory applicability, including deployed configuration and reachable paths. Never reproduce against production or disclose credential values.

## Data integrity and reliability

- Examine atomicity, transactions, partial failure, retries, idempotency, cancellation, cleanup, concurrency, deadlocks, races, resource leaks, and error propagation.
- Trace failure and recovery paths, not just success paths. Identify which invariants can break and under what ordering or lifecycle conditions.

## Performance and scalability

- Inspect hot paths, repeated work, blocking I/O, unbounded state, excessive allocations, queries, indexing, and contention against realistic workloads.
- Distinguish measured bottlenecks from source-established complexity or resource growth and from unverified performance hypotheses. Do not invent timing or scale estimates.

## Architecture and maintainability

- Examine coupling, cohesion, broken boundaries, duplicated knowledge, inconsistent contracts, incomplete migrations, unnecessary layers, and speculative abstractions.
- Demonstrate a concrete maintenance, correctness, or testability cost. Respect project constraints. A single implementation or similar-looking code is not sufficient evidence of a bad abstraction or duplicated business rule.

## Dead code and dependencies

- Inspect semantic references, entry points, reflection, callbacks, dynamic imports, registries, public APIs, platform variants, generated code, build scripts, and external consumers before declaring code unused.
- Distinguish genuinely obsolete code from intentionally supported compatibility paths. Where external usage cannot be established, record the uncertainty instead of asserting safe removal.

## Tests and verification

- Examine missing regression coverage for material behavior, weak assertions, misleading mocks, flaky tests, unsupported platform claims, and differences between local checks and CI.
- Check indirect, integration, and parameterized coverage before declaring tests absent. Missing coverage is not proof of a runtime bug; report concrete risk rather than coverage percentages alone.

## Compatibility and user experience

- Check supported versions and platforms, APIs, data formats, migration and rollback, localization, accessibility, and relevant UI behavior.
- Verify platform-specific execution and existing fallback paths. Do not generalize one platform's result to all platforms or treat subjective design preferences as defects.

## Operations and release readiness

- Inspect build reproducibility, packaging, configuration, permissions, deployment, monitoring, diagnostics, recovery, and documentation necessary for correct use.
- Distinguish source/configuration evidence from observations about a running deployment. Do not infer production state or readiness from repository contents alone.
