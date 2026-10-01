# Investigation techniques

Use the cheapest reliable method that distinguishes the remaining explanations. These techniques are alternatives, not a mandatory sequence.

## Reproduction and reduction

- Identify the real oracle: a wrong value, error signature, lost event, invariant violation, state transition, unacceptable latency, or other measurable deviation. A nonzero exit code alone may represent setup failure rather than the reported bug.
- Preserve the initial case, including invocation, inputs, versions, runtime, build mode, feature flags, locale, timezone, relevant state, and sequence of actions. Record only information needed; avoid collecting private data indiscriminately.
- Choose a feedback loop: existing test, CLI fixture, isolated request, browser/device interaction, replay, deterministic scheduler, differential check, or minimal harness. Favor the real dependency at the failing boundary if substituting it would hide the defect. Make the loop faster by reducing unrelated setup, not by weakening the oracle.
- Simplify one dimension at a time: input size, action sequence, callers, data, configuration, components, or concurrency. Preserve a known-good control and compare the reduced case to the original. A smaller case that fails differently is a different problem.
- For user-operated devices, provide precise actions, expected observations, and the smallest relevant redacted output to return. Ask for only necessary information; do not claim to have operated an unavailable device.

## Trace cause through the system

Start at the violated expectation. Trace backward to the actual producer of the unexpected value or state, and forward to confirm the downstream effect. Inspect runtime dispatch rather than assuming a similarly named function is used. Include asynchronous handoffs, retries, lifecycle events, generated code, configuration selection, and persistence when relevant.

At each relevant boundary, establish what is promised, what enters, what leaves, and what happens next. Use correlation IDs, sequence numbers, timestamps with clock limitations, stack frames, or state snapshots to connect the same operation. A normal downstream rejection of invalid input is evidence of an upstream problem, not necessarily a faulty rejection.

A useful causal explanation separates:

- Trigger: the action or condition that exposes the failure.
- Mechanism: the violated invariant and the path that produces the wrong result.
- Contributors: conditions that enable or amplify the mechanism.
- Consequence: what the caller or user experiences.

Check whether fixing a proposed cause prevents the original failure under its trigger, whether another cause explains the same observations, and whether the explanation accounts for known-good cases. For interacting factors, test their combinations or state why that is impractical.

## Hypothesis discipline

Use a small working ledger only when it helps:

| Hypothesis | Prediction | Probe / result | Status |
|---|---|---|---|
| <specific mechanism> | <distinguishing observation> | <actual observation or planned check> | <open / supported / rejected / confirmed> |

Prefer probes that discriminate between hypotheses, not tests that merely confirm one could be possible. A negative result is useful only if the probe was capable of detecting the effect. Check controls, sample size, execution path, and whether the measurement itself failed. Re-rank based on results; do not resurrect rejected explanations without new evidence. A mechanism can be established by a sound code-level deduction when runtime execution is unavailable, but enumerate assumptions (reachability, versions, inputs, dispatch) and do not imply a reproduction occurred.

## Compare and bisect

Compare successful and failed executions using equivalent inputs, dependencies, state, and environment. Review recent changes in relevant code, configuration, data, toolchains, and deployments. Commit authorship or temporal proximity does not establish causation.

When a regression has known good and bad revisions and a trustworthy classifier, use Git bisection in an isolated worktree or other authorized disposable checkout. First verify both endpoints with the same failure predicate. Keep setup errors, unbuildable commits, or inconclusive outcomes distinct from the bug. For `git bisect run`, verify the harness exit convention: 0 is good, 1–127 except 125 is bad, 125 skips; other values abort. Do not classify unrelated command failures as the target regression. Preserve user changes and any existing bisect session. Clean up only your own investigation state. Inspect the candidate change and reproduce its causal effect; the first bad revision is a lead, not proof of the exact defective line. Nonmonotonic behavior and flaky tests can invalidate a simple bisection.

## Instrument selectively

Use existing logs, traces, debugger watches, breakpoints, metrics, and profilers before modifying source. Select probes based on what evidence is missing. When temporary source instrumentation is authorized, use a unique marker and track changed paths. Avoid secret values, full request bodies, credentials, private identifiers, or excessive tracing. Use safe summaries such as type, length, status, redacted correlation ID, or presence.

Breakpoints, logging, profiling, or extra allocation can alter scheduling and hide races; compare with normal execution. Bound runtime, output volume, and resource use. Clean up temporary processes, probes, files, and configuration created for the experiment. Do not delete existing user artifacts.
