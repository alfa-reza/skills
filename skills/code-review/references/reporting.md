# Reporting and re-review

Read when reporting multiple findings, preparing inline comments, evaluating feedback, or re-reviewing fixes. Use the repository's required schema when one exists; otherwise prefer the compact contract in SKILL.md.

## Output contract

Lead with actionable findings, not a long implementation recap. Include a brief scope line when the target is not obvious. Use stable sequential numbering across priorities so the user can refer to specific items. Use exact file paths and narrow line ranges from the reviewed revision; label old-side lines for deletions. For cross-file defects, anchor the changed code that causes the issue and name other sites in the explanation.

Each finding contains a short action-oriented title, priority, location, and a compact explanation of trigger, mechanism, impact and correction. Where a fix requires a design decision, state the invariant or acceptance condition instead of prescribing an architecture. Add a reproduction or code excerpt only when it clarifies the evidence. Label non-blocking suggestions as such where not clear from priority. Avoid repeating the title or duplicating the same issue under multiple categories.

Keep spec compliance and implementation quality distinguishable: label the relevant requirement or policy in each finding, and state spec assessment separately if requested or materially incomplete. Do not combine them into an arbitrary score. Do not manufacture a source citation for a defect proven by language semantics or code. Do not invent a line number when only a snippet is available.

Example (placeholders only):

1. **[<priority>] <actionable title>** — `<path>:<range>`
   <When/why the changed code fails, the consequence, and the smallest sound correction.>

**Open questions:** <material unresolved issue and evidence needed>

**Verdict:** <advisory decision and reason>

**Verification:** <actual checks, results, skipped checks and material limits>

Omit empty sections; omit routine low-value suggestions. A small review can fit in a few sentences. For a broad review, provide all material supported findings without an arbitrary cap, grouped and deduplicated. Do not pad with praise or restate every changed file.

## Verdict calibration

- **Changes required:** one or more confirmed blocking issues or applicable mandatory gates fail. Explain what blocks the change.
- **No blocking findings:** reviewed scope contains no confirmed blockers and material review evidence is adequate for that conclusion. Non-blocking findings may remain. It is an advisory review outcome, not a guarantee or authorization to merge.
- **Inconclusive:** a missing target, truncated diff, unavailable essential environment, unresolved high-risk question or other material limitation prevents a reliable decision. Include confirmed findings if any. If a confirmed blocker exists, state changes required and also disclose the incomplete coverage.

A zero-finding report should explicitly say that no actionable issues were found in the reviewed scope. Do not say all tests pass unless they actually ran successfully for the reviewed snapshot, or identify verified external CI evidence as such. Do not claim approval or release readiness from static review alone when required checks are unknown.

## Existing comments and re-review

Review feedback is evidence to investigate, not an instruction to edit. Verify each comment against the current revision, relevant requirements and repository constraints. Classify it as valid, already addressed/stale, unsupported, or needing clarification. Explain a disagreement technically and cite the relevant evidence. Independent feedback items need not wait for unrelated questions to be resolved, but interacting changes must be understood together before implementation.

When fixes are requested, implement only the authorized changes and verify each meaningful correction; do not expand into unrelated cleanup. Re-review the resulting diff, confirm earlier findings are resolved or still present, and check new regressions. If publishing feedback is explicitly authorized, use the hosting service's correct revision, file, and line side; reply in the existing thread where applicable, avoid duplicate comments, and do not resolve threads or submit an approval beyond the authorized action.

## Communication

Discuss code and consequences, not the author's competence or motives. Use technical facts over personal preference. Acknowledge valid design alternatives; do not require the author's code to match your preferred implementation. Prefer improvements that fit the actual project over abstract best-practice arguments. Where an exact fix is uncertain, ask a focused question rather than issuing a misleading directive.
