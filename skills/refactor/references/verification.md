# Behavior and verification

Use for nontrivial refactors, weak baselines, or risky contracts. Verification establishes evidence for specific behavior, not proof that every possible execution is equivalent.

## Establish a usable baseline

- Record the actual starting revision and relevant local modifications. Compare before and after using comparable dependency versions, configuration, environment, data and inputs.
- Choose checks covering changed paths, boundary values, failure behavior and relevant invariants. Inspect assertions; a passing test that never executes the affected path is weak evidence.
- Run relevant existing checks before edits when available. In analysis-only mode, do not add tests or alter project files. Inspect test commands for writes and network effects; disposable caches, coverage files and isolated temporary databases are acceptable only when safely contained and no project data is sent externally. Otherwise obtain authorization or skip and report the limit.
- If implementation is authorized and important behavior is uncovered, write characterization tests against the original implementation. Observe actual behavior rather than guessing desired requirements. Confirm the tests exercise the intended path and pass against the baseline; an artificial red phase is unnecessary. Do not expose secrets or enshrine unsafe behavior as a new requirement; surface discovered defects separately.

| Baseline condition | Response |
|---|---|
| Relevant tests pass | Use them as evidence and supplement gaps proportionately. |
| Unrelated failure | Continue if its independence and affected behavior are established; report it. |
| Relevant failure | Investigate before restructuring; proceed only with alternative adequate evidence. Do not fix the defect without authorization. |
| Missing tests, low-risk mechanical edit | Use reliable static evidence and available scoped checks; state limits. |
| Missing tests, risky behavior | Establish characterization or equivalent evidence before editing; otherwise defer the risky change. |
| Unavailable target/toolchain | Verify available targets, explicitly identify the unverified ones, and do not claim compatibility. |

## Select evidence by contract

| Boundary | Useful evidence |
|---|---|
| Pure computation | Table-driven cases, edge values, properties, old/new comparisons |
| Stateful logic | Transition sequences, invariants, idempotency, recovery, restart |
| APIs and protocols | Requests, responses, status codes, errors, serialization, auth, version compatibility |
| UI and interaction | State, focus, navigation, accessibility, lifecycle, relevant visual comparisons |
| Persistence | Round trips, transactions, rollback, ordering, constraints on isolated fixtures |
| Concurrent or async code | Ordering guarantees, cancellation, resource cleanup, race tests, backpressure; use deterministic synchronization where possible |
| Library or CLI | Public surface, named parameters, ABI where promised, exit status, stdout/stderr, consumer and packaging checks |
| Performance-sensitive path | Comparable workloads and existing budgets; measure if resource or timing guarantees are at risk |

Choose the smallest sufficient combination. Compiler and linter results cannot substitute for behavioral tests; passing unit tests may miss integration and platform regressions.

## Compare and handle differences

- For risky transformations, run identical inputs against old and new implementations, or compare captured fixtures. Use separate isolated state. Normalize nondeterministic fields only if the contract permits it. Compare errors and side effects as well as results; do not run both versions against production or shared mutable state.
- Preserve observable expectations. Implementation-coupled tests may be restructured when their behavioral intent and coverage remain. Investigate changed snapshots, weakened tolerances, missing assertions or newly skipped tests; never use these to hide regressions.
- Check coherent risky steps and run final checks covering affected consumers. On new failure, immediately revert the latest transformation to the last green state; do not patch forward over broken tests. Diagnose the failed assumption before retrying with a smaller step. Never suppress diagnostics, weaken assertions, skip broken tests, or reset user work. If relevant behavior cannot be verified adequately, stop that risky step.
- Distinguish fresh final results, baseline results, failures, skipped checks and static reasoning. A flaky check is not a verified pass. Report material unverified behavior and the next useful validation step. Do not claim speed improvements without comparable measurements.
