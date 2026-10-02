# Scope and revision selection

Read when the target involves version control, a pull request, a partial patch, or uncertain local changes. Use the repository's VCS and hosting tools; Git examples are illustrative, not a required toolchain.

## Select the actual comparison

| Request | Git approach | Important distinction |
|---|---|---|
| All uncommitted work | `git status --short`, `git diff HEAD`, inspect staged/unstaged separately and relevant untracked files | Net diff omits untracked files and can hide staged changes canceled by unstaged edits. Record what was reviewed. |
| Staged only | `git diff --cached` | Read post-image from index, not dirty working tree. |
| Unstaged only | `git diff` | Compare index to working tree; staged code is baseline context. Include untracked only if scope includes them. |
| Last or named commit | `git show <commit>` or compare parent to commit | For root commits compare to empty tree; for merge commits choose and disclose parent or combined comparison appropriate to intent. |
| Branch / PR changes | Resolve base tip and head; compute merge base and compare merge-base tree to head tree | Do not assume `main`, upstream remote or `HEAD`. Review actual PR head, not unrelated local edits. |
| Exact endpoint comparison | `git diff <base> <head>` | Two endpoint trees, not necessarily branch-introduced changes. |
| Files / snippet / pasted diff | Inspect provided content and available surrounding context | Do not invent a base or assume absent context is absent code. |

For branch-introduced changes, `git diff A...B` compares a merge base of A and B with B. Pin the chosen commit IDs; when already using a fixed merge-base commit, compare that commit directly to the target. For a PR, distinguish branch diff from the proposed merge result against the current base if integration risk matters. If there are multiple merge bases or no common ancestor, use the provider's documented comparison or clarify the intended base rather than choosing silently.

On an unborn branch, inspect staged changes relative to the empty tree and working/untracked content as applicable. In non-Git projects, use the corresponding revision history, snapshot or explicit comparison; if unavailable, review the supplied current code without claiming regression attribution.

## Protect snapshot integrity

- Record repository identity, selected base/head, working-tree scope, and file inventory. For local mutable changes capture the actual diff and relevant content; recheck material changes before finalizing. If content changes, re-review affected areas or qualify results.
- Read revision-specific file contents (for example `git show <sha>:<path>` or `git show :<path>`). Do not cite line numbers from a different revision. Resolve renames and deleted lines using the correct side. When a missing requirement has no added line, anchor it to the nearest affected implementation or explicitly cite the requirement.
- Do not change HEAD, index, branch, stash, or user files to inspect another revision. Use read-only object inspection or an authorized isolated temporary checkout if runtime checks require it. Do not overwrite existing temporary paths. Do not restore files from a stale snapshot over concurrent user changes.
- Remote review: verify repository, PR number, base/head, diff completeness, and accessible CI metadata. Paginate truncated results; distinguish current head from earlier review comments. Network access, credentials and external actions remain subject to user permissions.

## Coverage without context waste

Start with change summary and file list, then review coherent groups by feature or subsystem. Include new and deleted files, tests, generated inputs, lockfiles, config, build scripts, and submodule changes when relevant. Large generated or binary outputs may be inspected by provenance, generator inputs, manifest and targeted checks; disclose what was not inspected. Do not ignore a generated artifact just because it is generated if the shipped result is material.

Read contracts and affected code before declaring a defect; expand only as needed to resolve impact. Search references using available semantic tools or ordinary search; inspect dynamic registration, reflection, callbacks and external consumers when relevant. If context is limited, keep a coverage ledger of paths or subsystems reviewed and remaining, not a mandatory user-facing checklist.

On re-review, identify the new head and changes since the prior review. Revalidate old findings against the current snapshot, check the corrections and affected paths, and inspect newly introduced changes. Do not carry stale comments forward as confirmed.
