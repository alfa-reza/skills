---
name: simplicity
description: Keeps code changes minimal and complete (KISS, YAGNI, DRY). Use before designing, writing, fixing, refactoring, or reviewing code, including when choosing dependencies or abstractions.
---

# Simplicity

Make the simplest complete change that meets the actual requirements. Minimize overall complexity, not lines of code.

- Inspect affected code, callers, and contracts first. Fix root causes, not symptoms.
- Prefer suitable existing project code, then standard library or platform features, installed dependencies, and focused new code. Skip changes if requirements are already met. Choose a different option if it is demonstrably simpler overall.
- Do not add unrequested features, speculative options, configuration, or extension points. Avoid optimization without evidence or an explicit requirement.
- Do not introduce abstractions without a concrete benefit. Consolidate duplicated knowledge or business rules, not merely similar code.
- Do not add dependencies without a concrete net benefit, or refactor or delete unrelated code. Follow existing conventions and architecture.
- Preserve required behavior, compatibility, security, accessibility, validation, and necessary error handling. Update affected callers, tests, and other dependent code.
- Test changed nontrivial behavior; reuse applicable results. Report material risks and unverified behavior. Mention worthwhile out-of-scope improvements without implementing them.
- When reviewing, identify concrete unnecessary complexity and suggest a simpler alternative; do not modify code unless authorized.
