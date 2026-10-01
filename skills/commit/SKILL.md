---
name: commit
description: Create safe, atomic Git commits. Use before any commit, including automatic commits, or when composing commit messages.
---

# Commit

Follow user instructions and repository rules. Use Conventional Commits in English unless the user or repository requires another format. Infer scope and style from recent commits. Use native Git or repository-approved tooling. Avoid redundant inspection, narration, and confirmation.

## 1. Scope

- Explicit commit requests authorize committing; otherwise require standing permission or approval. Message-only requests are read-only.
- Default: include only changes attributable to the current task/session. Staged-only: use exactly the existing index. All: include all reviewed, authorized changes, including untracked files.
- Inspect repository state and relevant diffs. Derive ownership from actual session edits, not dirty paths or timestamps. Preserve unrelated and pre-staged changes; ask if scope or ownership cannot be safely resolved.
- Group by logical intent, not files. Include necessary tests, docs, and dependencies. Separate unrelated hunks safely; never leave intentionally incomplete commits.

## 2. Prepare and Review

- Stage explicit paths or hunks. Avoid blanket staging outside all mode. Never overwrite, discard, or stash unrelated work to simplify committing.
- Run relevant formatting and validation on the intended changes; reuse applicable results. Stop on failing required checks; disclose checks not run.
- Review the exact staged content, including deletions, renames, new files, and generated changes. Exclude secrets, unintended artifacts, debug leftovers, and conflict markers.
- Record the reviewed HEAD and staged tree. Use this evidence for the message; do not substitute mutable working-tree content. Recheck both immediately before committing; stop or re-review if either changed.

## 3. Conventional Commits

`type(scope)!: description` — scope and `!` are optional.

- Types: `feat` (feature), `fix` (bug), `docs` (documentation), `style` (formatting only), `refactor` (behavior-preserving restructuring), `perf` (performance), `test` (tests), `build` (build/dependencies), `ci` (CI/CD), `chore` (maintenance), `revert` (reversal). Choose by intent, not file type; avoid `chore` as a catch-all. Respect repository-specific types.
- Scope: short, lowercase, meaningful module or area; follow existing scopes, omit when unhelpful.
- Subject: imperative, lowercase opening unless a proper noun, no trailing period, specific to the reviewed diff. Prefer at most 72 characters total unless repository rules differ.
- Body: optional, separated by a blank line; explain why, non-obvious changes, or impact. Wrap prose at 72 characters. Do not repeat the subject or list files.
- Breaking changes: use `!` and a `BREAKING CHANGE:` footer explaining incompatibility and migration where applicable.
- Footers: separate with a blank line; reference real issues using `Refs: #123`, and use closing keywords only when the issue is fully resolved and closure is intended.
- No AI attribution, AI co-author trailers, filler, inflated claims, or invented facts. Preserve legitimate authorship and required trailers.

## 4. Commit and Verify

- Respect hooks, signing, and repository safeguards. Never bypass them, alter identity, delete locks, or rewrite history without explicit authorization.
- If hooks or concurrent work change prepared content before commit, re-review it; if detected after commit, report the mismatch. Stop if ownership or scope becomes uncertain.
- Verify the resulting commit, message, and tree against reviewed evidence. On failure or interruption, check whether the commit already exists before retrying. Never silently amend or reset to recover.
- Report commit hashes and subjects, relevant failures, and intentionally excluded changes. Push only when authorized; verify the destination. Never infer permission to rebase, squash, or force-push.
