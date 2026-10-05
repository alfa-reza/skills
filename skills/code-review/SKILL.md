---
name: code-review
description: Review software changes for defects, regressions, security risks, specification compliance, compatibility, and maintainability. Use when reviewing diffs, patches, staged changes, commits, pull requests, or implementations, including self-review and re-review.
---

# Code Review

Stop. A review with zero findings is completely valid. Never manufacture speculative issues, cosmetic nits, or hypothetical edge cases to justify the review. Only report concrete, actionable defects introduced or exposed by the reviewed change. Judge code by its actual behavior and requirements, not personal taste.

## 1. Scope and Diff Anchor

- **Target selection:** Review the exact target requested. Default to uncommitted changes (staged, unstaged, and relevant untracked files); if clean, review the latest commit. State the resolved scope.
- **Diff Anchor rule:** Anchor findings strictly to lines added or modified in the diff. Do not audit untouched legacy code unless the reviewed diff directly breaks it (`outside the diff caused by path:line`).
- **Read-only rule:** Review is strictly read-only. Never fix, format, stage, commit, approve, or merge during review unless explicitly authorized.

## 2. Pre-Report Gate

Before recording any finding, verify all four criteria. If any answer is negative or uncertain, drop or downgrade the finding:

1. **Exact citation:** Cite the exact file path and line number from the reviewed revision. Vague file-level complaints are not actionable.
2. **Concrete failure scenario:** Name the specific input, state transition, and bad outcome. If a trigger cannot be named, it is pattern-matching, not a defect.
3. **Surrounding context verified:** Read callers, callees, and test files. Confirm that upstream validation, types, or framework handlers do not already catch the issue.
4. **Defensible severity:** Severity must reflect realistic impact and exposure, not theoretical risk. High or Critical findings require undeniable proof.

Only report findings where you are >80% confident of a real defect with material consequences.

## 3. Common False Positives to Skip

Do not flag the following patterns unless concrete evidence proves a defect:

- **Upstream error handling:** Do not flag "missing error handling" on calls whose errors are caught by callers, Express error middleware, React error boundaries, or top-level handlers.
- **Internal input validation:** Do not flag "missing validation" on private or internal functions whose callers already validate inputs.
- **Obvious constants:** Do not flag "magic numbers" for standard constants (`200`, `404`, `1000` ms, `60`, `24`, `1024`, array index `0` or `-1`, HTTP status codes).
- **Stylistic alternatives:** Do not flag "consider using X instead" when the existing code is correct, safe, and readable.
- **Unused parameters:** Do not flag parameters required by an external interface, base class, or framework callback.

## 4. Prioritize and Decide

Use repository review conventions when defined; otherwise use:

| Priority | Meaning | Action |
|---|---|---|
| P0 | Catastrophic, release-blocking impact (irreversible data loss, severe exploit). | Immediate blocker |
| P1 | Serious defect in correctness, security, data integrity, or required contract. | Blocks change |
| P2 | Material localized defect, meaningful test gap, or maintenance regression. | Should address |
| P3 | Concrete low-impact improvement. Omit routine nits by default. | Non-blocking |

**Impact Floor:** Every surviving finding must prevent a concrete bad outcome (wrong calculation, data loss, security vulnerability, broken contract). If the worst outcome is merely cosmetic or stylistic, drop it. When in doubt about impact, drop the finding.

## 5. Report Format

Lead with actionable findings, ordered by priority, numbered sequentially across the report.

Example format:

1. **[<priority>] <actionable title>** : `<path>:<line or range>`
   <Triggering condition, causal mechanism, concrete impact, and the smallest sound correction.>

**Verdict:** <Changes required | No blocking findings | Inconclusive>

**Verification:** <checks run, exact test outcomes, and material review limits>

For zero findings, report that no actionable defects were found in the reviewed scope. A clean review is an expected outcome for quality diffs. Do not create report files or publish comments unless requested.

## 6. References (conditional loading)

Read relevant references only when specific detailed guidance is required:

- [Lenses & Evidence](references/lenses.md): review lenses, behavioral tracing, security/test checks, and shortcut counterexamples.
- [Reporting & Scope](references/reporting.md): Git revision selection, verdict calibration, and re-review handling.
