# Review lenses and evidence gates

Use these lenses and gates as investigation prompts, not an obligation to produce findings for each category. Inspect actual contracts, runtime paths, and real usage.

## 1. Review lenses

| Lens | Check when relevant |
|---|---|
| Requirements | Missing acceptance criteria, unexpected behavior, incomplete rollout, unintended scope. |
| Correctness | Boundary conditions, invalid/empty/zero values, ordering, precision, locale/time zone, error propagation. |
| Security & privacy | Trust boundaries, authentication, object-level authorization, injection, secrets, sensitive logging, isolation. |
| Data integrity | Atomicity, migration compatibility, idempotency, conflicting writes, loss on partial failure. |
| Concurrency & lifecycle | Shared state, lock ordering, cancellation, stale events, shutdown, resource ownership and cleanup. |
| Compatibility | APIs, ABI, serialized formats, persisted data, configuration, runtime/compiler/OS versions, old clients. |
| Performance & scale | Repeated work, N+1 queries, unbounded memory/state growth, hot-path cost, queueing, blocking I/O. |
| Tests | Meaningful assertions, regression protection, negative cases, isolation, assertion strength. |
| Architecture | Cohesion, boundaries, dependency direction, concrete change cost, duplication of business knowledge. |
| User experience | Accessibility, focus, input behavior, state preservation, loading/error/empty states, localization. |

## 2. Finding acceptance gate and attribution

For each candidate finding, verify:

1. **Attribution:** Is the issue introduced, worsened, or newly exposed by this change? Separate pre-existing unrelated defects.
2. **Mechanism:** What specific input, state, or execution path triggers the failure? Trace the causal chain rather than pattern-matching.
3. **Applicability:** Does the violated invariant or contract actually apply to this code?
4. **Falsification:** Could existing guards, upstream validation, types, framework defaults, or tests prevent this? Check them before reporting.
5. **Actionability:** Recommend the smallest sound correction that resolves the problem without collateral side effects.

### Attribution rules

| Observation | Treatment |
|---|---|
| Newly introduced or worsened defect | Review finding. |
| Existing defect now reachable under the change | Review finding; explain new reachability. |
| Existing defect explicitly required to be fixed | Review finding against the requirement. |
| Pre-existing unrelated defect | Omit by default; separately flag material safety concerns if critical. Do not label as a regression. |
| Base revision unavailable | Describe the current issue and qualify attribution; avoid claiming it is newly introduced. |
| Intentional behavior change | Check requirements, consumers, and compatibility; do not automatically label intended behavior a bug. |

## 3. Behavioral tracing and testing checks

- **Security tracing:** Trace untrusted input to sensitive operations through validation, sanitization, and authorization. A dangerous-looking API is not automatically a vulnerability; account for the actual threat model. Never reproduce credentials or sensitive data in reports.
- **Evaluate tests, not test counts:** Verify that tests exercise the affected path and would fail if behavior were broken. Flag mocks that bypass the defect or tests that merely assert functions were called without verifying output.
- **Simplicity without mechanical rules:** Do not require abstractions, helpers, or design patterns based solely on line count. Single-use helpers expressing clear domain seams are valid. Retain required validation and safeguards; remove ceremony, never safety.

## 4. Counterexamples to common reviewer shortcuts

| Shortcut | Correct review behavior |
|---|---|
| Nullable value must crash | Inspect type narrowing, constructors, validation, and actual callers. |
| No references found, so delete it | Inspect registration, dynamic references, reflection, and public API consumers. |
| New check duplicates an upstream guard | Verify all entry points and whether the check protects a distinct trust boundary. |
| All tests pass, therefore no defect | Inspect assertions, covered branches, and missing edge-case paths. |
| Race exists because two threads access state | Establish conflicting operations and missing synchronization or ordering guarantees. |
| Helper has one caller, so inline it | Check cohesion, isolation seam, testability, and domain meaning. |
| Dependency has a CVE | Verify exact resolved version, vulnerability applicability, and reachability. |
| Scanner or linter flagged a pattern | Independently verify against the current snapshot and actual runtime impact. |
