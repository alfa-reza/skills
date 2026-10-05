# Verification, Feedback Loops, and Incident Containment

## 1. Constructing a Red-Capable Feedback Loop

A tight, repeatable pass/fail feedback loop is the foundation of debugging. Without a command that fails specifically on this defect and passes when resolved, debugging degrades into speculative trial and error.

Select the cheapest viable feedback loop in this priority order:

1. Failing Unit or Integration Test: A targeted test assertion exercising the exact failing function, method, or query with fixture inputs.
2. CLI Command with Diff: A shell invocation passing input arguments or piping a file, comparing stdout/stderr against a known-good baseline or exit code.
3. HTTP / RPC Script: A `curl`, `fetch`, or client script sending a captured payload to a local service endpoint and asserting on status code and response body.
4. Captured Trace Replay: Replaying a recorded network payload, message queue event, or database snapshot through the component in isolation.
5. Throwaway Minimal Harness: A standalone script (e.g. `repro.py`, `repro.ts`, `main.go`) importing only the affected module and exercising the failing call path.
6. Headless Browser Driver: Playwright, Puppeteer, or Cypress script reproducing UI interaction failures, network intercept issues, or DOM assertion breaks.
7. Stress / Concurrency Loop: A tight loop running the trigger 50 to 500 times in parallel or under artificial CPU/memory pressure to elevate the reproduction rate of race conditions.
8. Differential Execution: Running the same input through the current revision and a known-good prior commit, comparing outputs or side effects.
9. Git Bisection Harness: Automating `git bisect run <script>` where the script returns 0 for good, 1 for bad, and 125 to skip unbuildable revisions.
10. Human-in-the-Loop Protocol: When manual user action is mandatory, provide an interactive, step-by-step checklist with unambiguous expected outcomes.

Tighten the loop before investigating: ensure it executes in seconds, isolates external network flakiness, and asserts on the exact failure rather than general success.

## 2. Minimizing to Load-Bearing Elements

Once the failure is reproduced, reduce the reproduction case to its minimal form:

- Cut extraneous configuration flags, middleware, headers, payload fields, and execution steps one at a time.
- Re-run the feedback loop after each removal.
- If removing an element makes the test pass, that element is load-bearing; keep it. If the test still fails, discard the element permanently.
- Stop when every remaining element in the test case is load-bearing. This minimal reproduction form becomes the direct blueprint for the permanent regression test.

## 3. Regression Test Seams and Test Validity

- Select the seam that captures the real defect: Place the regression test at the boundary where the invariant broke. A unit test that mocks away the component containing the bug provides false confidence. If no clean seam exists in the current architecture, document this architectural deficiency explicitly in the audit report.
- Verify the negative control: Run the regression test against the unpatched codebase. Confirm that it fails with the exact reported error. A test that passes before the fix is applied does not measure the bug.
- Avoid assertion dilution: Never weaken existing assertions, increase global test timeouts, skip flaky tests, or broaden regex matchers simply to obtain a passing suite.

## 4. Intermittent and Concurrency Verification

- Measure statistical reproduction rates: Record the failure frequency before the fix (e.g., 18 failures out of 100 iterations).
- Verify resolution under equal stress: Run the identical stress loop (e.g., 200 iterations) against the patched codebase. Zero observed failures across a statistically significant trial count provides confidence, though never mathematical proof of race elimination.
- Check lock contention and deadlocks: Confirm that introducing synchronization primitives (mutexes, channels, transactions) does not introduce latency bottlenecks or circular lock dependency chains.

## 5. Incidents and Production Containment

- Prioritize reversible containment over immediate root-cause fixes: In live production incidents, mitigate user harm first via traffic draining, feature flag disablement, read-only degradation, or rolling rollback to the previous known-good release.
- Preserve forensic evidence: Before restarting nodes, clearing caches, or redeploying, capture process thread dumps, memory snapshots, unredacted error logs, and metrics graphs into an isolated directory.
- Differentiate mitigation from permanent resolution: Clearly label temporary operational workarounds (such as process restarts or rate limiting) as containment mitigations. Never close a defect ticket until the underlying software defect is corrected and locked with a regression test.
