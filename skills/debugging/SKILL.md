---
name: debugging
description: Diagnose and resolve software failures through evidence-driven debugging across languages, runtimes, and architectures. Use for bugs, test failures, crashes, hangs, race conditions, memory leaks, performance regressions, and integration breaks. Enforces feedback loop creation, hypothesis discrimination, the 3-attempt stop gate, and regression test verification.
---

# Debugging

Investigate through disciplined evidence gathering before modifying code. A disappearing symptom, passing unrelated test, or plausible narrative is not proof of causation. Follow this universal debugging lifecycle across all languages and runtimes.

## 1. Hard Guardrails

- The 3-attempt stop gate: If a bug persists after 3 fix attempts, or if an edit introduces more errors than it resolves, STOP immediately. Revert all speculative changes back to a clean working state. Do not patch forward on broken assumptions; reformulate hypotheses or consult the user.
- Feedback loop prerequisite: Do not start reading wide codebases or guessing fixes before establishing a repeatable command or test that fails on this specific defect.
- Tagged instrumentation: Every temporary log, print, or diagnostic probe must include a unique tag (e.g. `[DEBUG-trace]`). Untagged probes risk leaking into production; tagged probes can be purged in a single command.
- Surgical correction: Fix the broken contract at the responsible seam. Do not introduce drive-by refactoring, formatting sweeps, silent fallbacks, arbitrary sleep delays, or speculative wrappers.
- Redact credentials: Never output API keys, private tokens, passwords, or personal data in logs, captured traces, or reports.

## 2. Debugging Lifecycle

### Phase 1: Build the Red-Capable Feedback Loop

Construct a fast, repeatable pass/fail signal before changing code. A valid feedback loop runs in seconds, executes unattended, and fails specifically on the reported bug:

1. Select the lowest-cost viable feedback mechanism from [Verification and Feedback Loops](references/verification.md):
   - Failing unit or integration test.
   - CLI script with input fixture and diff check.
   - HTTP/RPC invocation asserting on response status and payload.
   - Captured trace or payload replay.
   - Standalone throwaway harness exercising the isolated module.
2. Tighten the loop: eliminate unrelated setup overhead, pin wall-clock time, seed random number generators, and isolate network calls.
3. For intermittent or race condition failures: construct a stress loop (50 to 200 iterations) to elevate reproduction frequency to a debuggable rate.

Exit criterion: One runnable command that reliably exits red on the active defect and will exit green once fixed.

### Phase 2: Reproduce and Minimize

1. Run the feedback loop and verify that the failure symptom matches the reported problem, not a coincidental setup error.
2. Shrink the reproduction case: remove configuration flags, payload fields, middleware, and setup steps one at a time.
3. Re-run the loop after each reduction. Keep only elements whose removal causes the failure to disappear.

Exit criterion: A minimal reproduction scenario where every remaining parameter is load-bearing.

### Phase 3: Hypothesize and Discriminate

1. Generate 3 to 5 ranked, falsifiable hypotheses before inspecting or modifying code. Single-hypothesis exploration causes cognitive anchoring on the first plausible idea.
2. Frame each hypothesis with a testable prediction:
   "If <Component X> is the root cause, then <Condition Y> will trigger the defect, while <Condition Z> will prevent it."
3. Consult [Diagnostic Patterns](references/patterns.md) to match symptoms against universal failure categories (concurrency, memory leaks, type coercion, deadlock, boundary conditions).

Exit criterion: A ranked list of distinct, falsifiable hypotheses with explicit predictions.

### Phase 4: Targeted Instrumentation and Causal Isolation

1. Insert tagged diagnostic probes (`[DEBUG-trace]`) at boundaries separating competing hypotheses.
2. Prefer non-invasive inspection (interactive debuggers, breakpoints, thread dumps, query plans) over broad logging sweeps.
3. Change one variable at a time. Record observations and eliminate disproved hypotheses; do not resurrect rejected hypotheses without new evidence.
4. Trace data flow backward from the failure point to the initial invalid state transition. The line that throws an exception is often a downstream victim of an earlier invariant breach.

Exit criterion: Verified causal path demonstrating the exact trigger, mechanism, and consequence of the defect.

### Phase 5: Regression Test and Surgical Fix

1. Convert the minimal reproduction from Phase 2 into a permanent regression test at the appropriate architectural seam.
2. Run the regression test against unpatched code to verify the negative control (confirm it fails for the correct reason).
3. Apply the minimal root-cause fix to the responsible module. Preserve existing contracts, compatibility, and resource ownership.
4. Run the regression test and verify that it passes.

Exit criterion: Regression test passes and locks the defect against future recurrence.

### Phase 6: Verification and Deterministic Cleanup

1. Re-run the Phase 1 feedback loop against the un-minimized original scenario to prove full resolution.
2. Run adjacent test suites and build checks to ensure no regressions were introduced.
3. Purge all temporary instrumentation: search for the unique probe tag across the repository (e.g. `grep -rn "\[DEBUG-trace\]"`) and remove every probe.
4. Inspect the final `git diff` to confirm that only intended, surgical changes remain.

Exit criterion: Original failure resolved, regression suite green, all debug probes removed, and working tree clean.

## 3. Reporting Format

Provide concise, evidence-grounded reports in conversation:

- Problem: Observed symptom versus expected specification.
- Cause: Exact component, file path, line numbers, and verified causal mechanism.
- Decisive Evidence: Concrete observation, log line, or dump confirming the diagnosis.
- Fix: Scoped correction applied to the responsible component.
- Verification: Regression test added, original feedback loop outcome, and adjacent suites verified.
- Residual Risk: Remaining environmental constraints or uninspected failure modes, if any.
