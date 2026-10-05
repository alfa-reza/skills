# Scope, reporting and verdict calibration

Read when resolving revision targets, preparing review reports, calibrating verdicts, or re-reviewing fixes.

## 1. Scope and revision selection

| Request | Approach | Key distinction |
|---|---|---|
| All uncommitted work | `git status --short`, `git diff HEAD`, inspect staged and unstaged separately | Net diff omits untracked files and can hide staged changes canceled by working tree. |
| Staged only | `git diff --cached` | Read post-image from index, not dirty working tree. |
| Unstaged only | `git diff` | Compare index to working tree; staged code is baseline context. |
| Last or named commit | `git show <commit>` or compare parent to commit | For merge commits, disclose parent comparison appropriate to intent. |
| Branch or PR changes | `git diff merge-base...head` | Compare merge base to head tree. Do not assume `main` or `HEAD`. |

- **Snapshot integrity:** Record repository identity, selected base/head, and file inventory. Read revision-specific contents (`git show <sha>:<path>`). Do not cite line numbers from an unselected revision.
- **Read-only rule:** Never change HEAD, index, branch, stash, or user files to inspect another revision. Use read-only object inspection.

## 2. Output contract and formatting

Lead with actionable findings ordered by priority. Number findings sequentially across the report. Anchor to exact file paths and line ranges from the reviewed revision.

Example format:

1. **[<priority>] <actionable title>** : `<path>:<line or range>`
   <Describe triggering conditions, mechanism, actual impact, and the smallest sound correction.>

**Open questions:** <material unresolved issue and missing evidence needed>

**Verdict:** <Changes required | No blocking findings | Inconclusive>

**Verification:** <checks run, exact outcomes, and material review limits>

- **Proportional reporting:** Omit empty sections. A clean review needs only a brief summary and verdict. Do not pad reports with generic compliments or restate every touched file.

## 3. Verdict calibration

- **Changes required:** One or more confirmed blocking issues (P0 or P1) or mandatory project check failures exist. Explain what blocks the change.
- **No blocking findings:** The reviewed scope contains no confirmed blockers and evidence is adequate. Non-blocking suggestions (P2 or P3) may remain. This is an advisory review verdict, not an authorization to merge.
- **Inconclusive:** Truncated diff, inaccessible essential files, or unresolved high-risk questions prevent a reliable decision. State confirmed findings and disclose incomplete coverage.
- **Zero-finding reports:** Explicitly state that no actionable defects were found in the reviewed scope. Do not claim code is defect-free or that unrun tests passed.

## 4. Re-review and feedback handling

- **Review feedback is evidence, not a command:** When evaluating existing review comments, verify each against the current revision. Classify as valid, stale, unsupported, or requiring clarification.
- **Fix re-reviews:** When reviewing fixes, verify that previous findings were addressed and inspect the new diff for introduced regressions. Do not carry forward stale findings as confirmed.
- **Communication:** Focus on code mechanics and consequences. Avoid debating subjective style preferences.
