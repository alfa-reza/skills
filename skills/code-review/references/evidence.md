# Evidence, attribution and severity

Read when findings are subtle, high impact, disputed, or depend on assumptions. Review is about supported problems, not plausible-sounding warnings.

## Finding acceptance gate

For each candidate, answer:

1. What changed? Is the issue caused, worsened, newly reachable, or required to be addressed by this change?
2. What invariant, contract, requirement or material engineering property is violated? Is it actually applicable?
3. Which input, configuration, execution path, state or maintenance task exposes it? Can that situation occur in the supported system?
4. What exactly happens, and why does it matter? Trace the mechanism rather than relying on names or appearances.
5. What existing guard, caller, type constraint, configuration, test, documented decision or other evidence could invalidate the finding? Check it.
6. What correction addresses the mechanism without introducing collateral problems? What check would distinguish the corrected behavior?

For missing requirements, point to the actual requirement and the absent behavior. For design findings, show a present maintenance consequence (for example two independent sources of a rule that must stay synchronized), not an imaginary future bug. For test gaps, specify a material behavior unprotected by the existing checks. A concrete runtime failure is not required for these finding types.

## Attribution rules

| Observation | Treatment |
|---|---|
| Newly introduced or worsened defect | Review finding. |
| Existing defect now reachable under the change | Review finding; explain new reachability. |
| Existing defect explicitly required to be fixed | Review finding against the requirement. |
| Pre-existing unrelated defect | Omit by default; separately flag material safety or release concerns if relevant. Do not call it a regression. |
| Base unavailable | Describe the current issue and qualify attribution; avoid claiming it is newly introduced. |
| Intentional behavior change | Check requirements, consumers and compatibility; do not automatically label intended behavior a bug. |
| Unclear specification | State assumption or question; do not invent requirements. |

## Evidence and confidence

- **Runtime-observed:** reproducible check or reliable current trace demonstrates the consequence. Record the exact scenario and outcome, not just the exit code.
- **Statically established:** source and known semantics prove the mechanism under supported conditions. Clearly label it as reasoning; no runtime claim.
- **Supported but unresolved:** credible evidence exists, but a material assumption cannot be checked. Put it under open questions, not confirmed findings. State the missing evidence and consequence if true. Treat safety-critical unknowns as review limitations, not as fabricated defects.
- **Disproven or speculative:** omit. An absence of search hits is not sufficient proof that code is unused; an analyzer warning is not automatically a confirmed defect.

Use the existing test harness where appropriate. Compare base and target under equivalent conditions for regression claims. Reuse results only if revision, environment and relevant scope match. Separate pre-existing test failures from new ones, and do not ignore a failed or unavailable required gate. If testing code could execute untrusted scripts, contact external systems, write shared data or expose secrets, inspect first and use an authorized safe environment; otherwise do not run it.

## Calibrate severity separately from confidence

P0 is reserved for exceptional catastrophic impact with a credible triggering path, such as widespread irreversible corruption or broad security compromise; not every crash or vulnerability is P0. P1 covers serious impact, broken required behavior, material security or compatibility failures. P2 covers meaningful localized defects, test gaps and maintenance regressions; blocking status depends on actual impact and repository policy. P3 covers concrete low-impact non-blocking improvements, not arbitrary preferences.

Consider affected users/data, frequency or reachability, recovery cost, security boundary, required guarantees, and operational blast radius. A rare but credible irreversible loss can be serious. A common cosmetic issue can remain minor. Avoid numerical confidence scores or fabricated probability estimates. When the repository uses another scale, map by meaning rather than mixing taxonomies.

## Counterexamples to common reviewer shortcuts

| Shortcut | Correct review behavior |
|---|---|
| A nullable-looking value must crash | Inspect type narrowing, constructors, validation and actual callers. |
| No references found, so delete it | Inspect registration, generated references, reflection and public API consumers. |
| A new check duplicates an upstream guard | Verify all entry points and whether the check enforces a different boundary. |
| All tests pass, therefore no defect | Inspect the assertions and affected paths. |
| A race exists because two threads access state | Establish conflicting operations and the missing synchronization or ordering guarantee. |
| A helper has one caller, so inline it | Check ownership, cohesion, test seam and domain meaning. |
| A new dependency has a CVE | Verify exact resolved version, applicability, and reachability or policy violation; do not guess. |
| A scanner or another reviewer said so | Independently verify it against the current snapshot. |

No finding quota, mandatory praise, arbitrary file size threshold, or synthetic overall score. Stop investigating a candidate when it is disproven; deepen investigation when material risk remains unresolved.
