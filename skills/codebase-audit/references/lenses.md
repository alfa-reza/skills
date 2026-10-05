# Audit Lenses

Apply lenses relevant to the audit mode and repository surface. Treat each lens as an active investigative inquiry into actual runtime paths and failure scenarios.

## 1. Correctness and State Invariants

- State transitions: Check state machine transitions, status flags, and lifecycle sequences. Trace impossible states, illegal transitions, and missing terminal states.
- Boundary conditions: Inspect zero, null, empty collections, off-by-one indices, precision loss in floating-point arithmetic, locale differences, and time zone offsets.
- Contract mismatch: Compare documented function signatures, types, and preconditions with actual call sites. Check for callers that disagree on return values or error types.

## 2. Security: Sink-Driven (Active Flaws)

- Injection: Trace user input reaching SQL queries, shell commands, LDAP queries, XML parsers (XXE), and server-side template engines (SSTI). Confirm parameterization or strict whitelisting.
- Deserialization and parsing: Inspect untrusted binary deserialization, unsafe YAML loading, and dynamic evaluation (`eval`, `exec`, `Function`).
- File operations and SSRF: Trace file upload paths, file downloads, path concatenation (directory traversal), and outbound HTTP requests constructed from user parameters (SSRF, cloud metadata access).

## 3. Access Control and Authorization: Control-Driven (Missing Guards)

- Broken Object-Level Authorization (IDOR): Check whether data modification or retrieval endpoints verify ownership against the authenticated identity or rely solely on client-supplied IDs.
- Unprotected mutations: Enumerate all mutation endpoints (POST, PUT, DELETE, PATCH). Verify server-side authentication and role-based authorization guards on each route.
- Whitelist and bypass paths: Inspect authentication exemption lists (such as `permitAll`, `excludePatterns`, public prefix whitelists). Verify that administrative actions, internal diagnostic endpoints, or sensitive file endpoints are not inadvertently exempted.

## 4. Data Integrity, Concurrency, and Transactions

- Atomicity and partial failures: Trace multi-step state mutations (such as debiting a balance and recording an order). Confirm that failures in secondary steps roll back prior operations via atomic database transactions.
- Concurrency and race conditions: Check check-then-act sequences, read-modify-write loops, and shared in-memory data structures across concurrent requests or worker threads. Verify locking, optimistic concurrency tokens, or atomic database operations.
- Idempotency: Inspect webhook handlers, message queue consumers, and payment processing endpoints. Verify deduplication keys and safe replay handling.

## 5. Performance, Hot Paths, and Scalability

- Algorithmic complexity: Inspect hot loops and frequently traversed paths for O(N^2) or worse complexity on potentially large collections.
- Database query efficiency: Identify N+1 query patterns, missing indexes on filtered or joined foreign keys, and unbounded queries lacking pagination limits.
- Resource consumption: Check memory buffers, streaming responses, unclosed connections, file descriptors, and background intervals that prevent garbage collection.

## 6. Architecture and Boundary Integrity

- Coupling and circular dependencies: Trace dependency flow between business logic, data access, and transport layers. Identify circular references and leaking persistence models into public contracts.
- Shotgun surgery: Identify logic fragmented across multiple disparate files where a single business rule change requires coordinated edits in five or more places.
- Speculative complexity: Identify indirection layers, wrappers, and configuration tables that exist without multiple concrete consumers.

## 7. Lean Bloat Elimination

- Delete: Locate unreachable code, obsolete feature flags, dead endpoints, commented-out blocks, and unused private methods.
- Native and standard library: Identify third-party dependencies whose functionality is natively available in the runtime standard library (such as modern fetch, path utilities, UUID generation, or simple string helpers).
- YAGNI abstractions: Identify interfaces with only one implementation, factories with only one product, and passthrough wrappers that merely forward arguments without adding logic.
- Merge: Consolidate near-identical helper functions or utility classes that perform identical transformations.
- Split: Deconstruct functions or classes that combine multiple unrelated responsibilities, making them difficult to reason about or test independently. Split by distinct operational responsibility, never by arbitrary line-count thresholds.

## 8. Test Integrity and Verification Gaps

- Missing regression coverage: Identify high-risk business logic (financial calculations, permission enforcement, token parsing, data transformations) that lacks unit or integration test coverage.
- False confidence: Check assertions in existing tests. Identify tests that assert merely truthy values, mock away the entire system under test, or catch and swallow assertions.
- Flakiness and timing: Check tests dependent on real wall-clock delays, fixed sleep intervals, unseeded random generators, or shared global state.

## 9. Secrets, Configuration, and Supply Chain

- Hardcoded credentials: Check for embedded API tokens, private keys, database passwords, and development credentials committed to source code.
- Environment validation: Check configuration loading at application startup. Verify fail-fast validation for mandatory environment variables.
- Dependency freshness and advisories: Inspect manifest files (`package.json`, `go.mod`, `pom.xml`, `requirements.txt`, `Cargo.toml`) for unpinned dependencies or known vulnerable components reachable from application code.

## 10. Operations and Recovery

- Observability and logging: Confirm that error paths log actionable diagnostic information without exposing sensitive customer data, credentials, or personal identifiable information (PII).
- Health checks: Check readiness and liveness endpoints. Verify they test critical downstream connectivity (databases, caches, queues) rather than merely returning static 200 OK responses.
- Rollback and migration safety: Inspect database schema migrations. Confirm that migrations are backward-compatible with the currently running application version during blue-green or rolling deployments.
