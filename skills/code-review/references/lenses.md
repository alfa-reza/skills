# Review lenses

Use these as investigation prompts, not an obligation to produce findings for each category. Inspect the changed contract, relevant scale, runtime and real usage. Consult version-matched primary documentation where semantics or APIs are uncertain; no particular tool is required.

| Lens | Check when relevant |
|---|---|
| Requirements | Missing acceptance criteria, unexpected behavior, incomplete rollout, unintended scope. |
| Correctness | Boundary conditions, invalid/empty/zero values, ordering, precision, locale/time zone, duplicate or partial input, error propagation. |
| Security and privacy | Trust boundaries, authentication, object-level authorization, injection, secrets, sensitive logging, isolation, unsafe defaults. |
| Data integrity | Atomicity, migration compatibility, idempotency, conflicting writes, loss or corruption on partial failure. |
| Concurrency and lifecycle | Shared state, lock ordering, cancellation, stale events, shutdown, resource ownership and cleanup. |
| Compatibility | APIs, ABI, serialized formats, persisted data, configuration, runtime/compiler/OS versions, feature flags and old clients. |
| Performance and scale | Repeated work, N+1, unbounded growth, hot-path cost, queueing, blocking I/O, backpressure. |
| Tests | Meaningful assertions, affected behavior, regression detection, isolation, negative cases. |
| Design and maintainability | Cohesion, boundaries, dependency direction, concrete change cost, duplication of knowledge, speculative abstractions. |
| User experience | Accessibility, focus, input behavior, state preservation, loading/error/empty states, localization. |
| Delivery | Build/package changes, dependency resolution, rollout ordering, documentation and operational impact. |

## Trace behavior and contracts

Compare old and new behavior for the same relevant inputs and state. Trace values from source to sink, and state transitions through success, failure, retry and cancellation. Check whether a changed default, return type, error, side effect, or evaluation order affects an existing consumer. For a bug fix, test the original failing scenario and sibling paths sharing the mechanism. For a refactor, check behavior preservation rather than demanding identical internal calls.

For security-sensitive changes, trace attacker-controlled data to sensitive operations through actual validation, encoding, authorization and isolation. Check enforcement on every relevant entry path, including privileged/background flows. Do not claim a vulnerability based on a dangerous-looking API alone; account for the relevant threat model and protections. Do not expose credentials or reproduce sensitive data in reports. Scanner findings need contextual validation; a clean scan is not a security proof. Prefer safe local reasoning and synthetic tests over live exploitation.

For persistence and distributed systems, inspect transaction boundaries, retry semantics, stale reads, uniqueness and consistency, mixed-version operation, schema defaults, backfill and rollback constraints. For concurrency, identify the concrete interleaving or ownership violation rather than labeling all shared state a race. Do not require locks where confinement, immutability or atomic operations already provide guarantees.

## Evaluate tests, not test counts

- Identify what changed and which tests exercise the affected path. Check whether they would fail if the relevant behavior were broken. Read fixtures and assertions, not just names and coverage numbers.
- Look for mocks that bypass the actual defect, assertions that merely repeat implementation, swallowed failures, nondeterministic timing, and changed expected values that normalize a regression.
- Recommend a specific missing case only where it protects meaningful behavior. Use the smallest adequate test level; do not demand E2E tests for every helper or arbitrary coverage percentages. A proven regression test is especially valuable for a bug fix.
- Existing tests can be run safely where available. Targeted mutation experiments are optional, only in an isolated disposable copy, when useful and authorized. Never mutate the reviewed working tree to test its tests. A surviving mutation must correspond to meaningful changed behavior before becoming a finding.

## Simplicity without mechanical rules

- Prefer direct code and established helpers over unnecessary indirection. Identify the actual duplicated rule, unused capability, extra dependency, or maintenance cost. Check semantic equivalence before recommending standard-library replacements.
- Single-use abstractions may express a domain invariant, isolation seam or ownership boundary; multiple similar code paths may evolve independently. Neither counts as a defect by itself.
- Retain useful abstractions, explicit error handling, compatibility layers, and required security checks. Do not equate fewer lines with reduced complexity. Do not suggest new generic frameworks, interfaces, configuration, or speculative extension points without current need.
- Check repository conventions against actual rules. Naming or readability can be material when it misrepresents behavior; avoid formatting nits and stylistic rewrites. If an established shortcut is documented, judge its real impact within the agreed constraints, not the label alone. Documentation cannot waive correctness or security requirements.

## Performance and environment

Use realistic workload and constraints. A proven unbounded operation or repeated expensive request can be a valid finding without a benchmark, but do not invent latency, speedup or capacity. Distinguish theoretical complexity from observed performance. Avoid suggesting caching or concurrency without considering invalidation, isolation, memory and ordering. For UI and platform changes, inspect actual lifecycle and target-specific behavior; runtime claims require the relevant environment.
