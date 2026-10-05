# Large and cross-boundary refactoring

Use for changes across meaningful module, platform, persistence, or public contract boundaries. The size of a repository alone does not justify architectural change.

## Operational boundaries

Separate functional changes from structural ones. Do not invent future features or use a refactoring request as permission for an unrelated migration.

- **Opportunistic cleanup:** Confine incidental cleanup strictly to code touched by the active task; defer broader refactoring.
- **Preparatory refactoring:** Restructure to make an upcoming authorized change simpler, without implementing new functionality during the refactoring step.
- **Architectural increments:** Deliver cross-boundary changes in small, independently verifiable, behavior-preserving increments. Never execute a multi-module rewrite in a single unverified step.

## Map the boundary and risk

Identify dependencies, entry points, consumers, invariants, current failures and the expected design benefit. Consider published libraries, external clients, plugin contracts, configurations, platform variants and old persisted data. Unknown external users of a supported interface are a reason to preserve it, not evidence that it is unused.

| Risk | Response |
|---|---|
| Local mechanical edit with good coverage | Scoped build and behavioral checks |
| Shared internal state, types or module moves | Map consumers, ownership and invariants; verify affected integration and targets |
| Public API/ABI, persistence, security, concurrency or generated clients | Establish contract evidence, account for compatibility and stop risky work when evidence or authorization is insufficient |

Risk depends on reachability, reversibility, coverage and consequences, not just file or line count. Identify high-risk areas first; use stable boundary tests rather than implementation details.

## Sequence the work

For broad analysis, provide numbered, independently actionable recommendations with locations, benefit, risks, dependencies and validation. If implementation is authorized, use a compact dependency-aware plan. Do not require repeated approval for each already-authorized step.

1. Establish behavior at the stable boundary and record current failures.
2. Introduce the smallest useful seam only if needed; retain supported entry points.
3. Move or extract one cohesive responsibility and update its consumers. Verify before expanding.
4. Repeat in dependency order, keeping the affected system working where practical. Separate mechanical moves when they make review easier.
5. Remove temporary internal scaffolding once consumers have transitioned. Preserve public compatibility obligations.
6. Validate the affected boundary and review the final diff.

A temporary bridge or branch-by-abstraction approach can enable a real transition, but it must have an exit condition and must not leave permanent competing sources of truth. Do not add flags or dual implementations merely to imitate a pattern. A breaking contract, schema migration, deployment or external coordination requires appropriate separate authorization; discuss rollback and data compatibility before undertaking it.

## Stop conditions

Stop when the agreed structural benefit is achieved, necessary consumers are updated and the relevant verification is complete or its limitations are explicit. If a risky phase is blocked, retain the last safe state of your own edits and report what remains. Do not hunt unrelated smells, perform repo-wide formatting, create persistent mode markers, or commit intermediate work without authorization.
