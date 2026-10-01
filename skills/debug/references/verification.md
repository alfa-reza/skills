# Fix and regression verification

## Prove the test measures the defect

When authorized to edit, prefer a regression test that reaches the actual failure seam. Establish that the original implementation fails for the intended reason, not because of syntax, setup, unavailable dependencies, or a deliberately broken assertion. Preserve the original reproduction separately if a reduced test is used.

- Exercise real inputs, action sequences, state, boundary conditions, and affected interactions. Use the project's existing test framework and conventions when available. Mock only what is irrelevant to the failure mechanism; an overly shallow test may encode the implementation rather than the user-visible contract.
- Use a known-good control where practical. For data corruption, compare values and invariants, not simply successful return status. For concurrency, verify event ordering and cancellation as applicable. For a UI defect, verify the actual interaction rather than only a helper's result.
- If the fix is already applied, establish the negative control in a safe isolated copy of the original revision or via a targeted reversal of only your own changes if safe. Do not reset or stash user work. If a genuine pre-fix failure cannot be demonstrated, report that limitation.
- Existing test failures are not automatically caused by this change. Compare to the baseline in the same environment. If baseline was not measured, avoid asserting that failures are pre-existing without evidence.

A regression test should protect the behavior that failed, not prescribe one implementation. Do not update snapshots, expected values, timeout limits, skip lists, or mocks merely to make a failing check green. Correct a genuinely obsolete or invalid test only when the intended contract is established; explain the evidence.

## Verify the correction

Apply the smallest coherent fix to the responsible code or configuration. A complete fix may require multiple coordinated edits. Keep the experiment that established causality distinct from the production correction. Check other consumers, supported inputs, error paths, permissions, resource lifecycle, and compatibility affected by the change. Add independent safeguards only where a real trust boundary or invariant justifies them; redundant validation is not inherently safer.

Run the targeted regression test, then repeat the original scenario with comparable inputs and environment. Broaden checks to affected components, integration paths and build targets based on risk and repository requirements. Inspect actual exit status and assertion results; a command that exits successfully after skipping tests is not equivalent to executed tests passing. Check the final diff after removing probes, and rerun affected checks if cleanup changed the tested implementation.

## Intermittent and performance failures

For flaky defects, record counts and conditions before and after (e.g. observed failures / trials, workload, runtime, scheduling settings). Prefer controlled interleavings or deterministic triggers where feasible. Select trial count based on expected frequency, risk, time and available resources, not a universal threshold. No observed failures does not prove a zero failure probability; dependent trials and environmental differences weaken comparisons. Avoid claiming a statistical confidence bound unless its assumptions are justified.

For performance, compare the same workload, build configuration, hardware and measurement method with appropriate warmup and repetitions. Check distributions, relevant resource use and correctness; report meaningful tradeoffs. A lower mean can hide worse tail latency. A changed test timeout or shorter artificial workload is not proof of a performance fix.

## If complete verification is unavailable

A missing device, CI credential, production-only input, unavailable service or inadequate test seam is a limitation, not permission to fabricate a pass. Prefer safe alternative evidence: targeted unit/contract checks, replay, static causal proof, build validation, controlled manual reproduction, or user-run instructions. State precisely what each validates and what remains unknown. Avoid invasive changes to critical paths without sufficient evidence or authorization. If only a mitigation is feasible, report it as such.

Use accurate conclusions: reproduced and verified fixed under tested conditions; corrected and partially verified; diagnosed but not changed; mitigation only; or unresolved. Do not treat a successful build, linter run, unrelated passing test, or absent log as proof the original failure is resolved.
