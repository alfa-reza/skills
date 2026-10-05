---
name: codebase-audit
description: Audit codebases, repositories, subsystems, or releases across three execution tiers (quick, standard, deep) for security flaws, reliability defects, performance bottlenecks, race conditions, test gaps, and lean bloat elimination. Use for evidence-based software audits, security audits, repository health checks, and pre-release audits.
---

# Codebase Audit

Execute structured, evidence-based code audits across three explicit execution tiers. Prioritize verifiable defects and measurable bloat over cosmetic style or speculative smells.

## 1. Audit Modes and Load Grounding

Establish the audit mode and the operational load baseline before reading source files:

| Mode | Scope | Target | Depth |
|---|---|---|---|
| `quick` | Incremental or focused surface | Changed files (`git diff`), public entry points, exposed secrets, and high-risk dependency CVEs. | Single-pass sink scan. Verify direct reachability. |
| `standard` | Subsystems and critical paths | Core business workflows, API routes, mutation handlers, authentication, and state boundaries. | Dual-track audit: sink-driven tracing and control-driven guard checks. |
| `deep` | Full repository | Entire repository end-to-end, cross-module dataflows, concurrency, architectural invariants, and lean bloat elimination. | Exhaustive dual-track, multi-hop taint tracing, attack-chain synthesis, and code elimination analysis. |

State the assumed operational load in the audit baseline:
- Single-user or local CLI script.
- Background batch job or queue worker.
- Concurrent service or multi-tenant API (specify estimated request volume and concurrency level).

Judge scalability, race conditions, memory growth, and connection pooling strictly against this assumed load.

## 2. Hard Guardrails

- Read-only execution: Do not modify files, install packages, change runtime configuration, switch branches, commit, or execute destructive commands.
- Anti-hallucination gate: Every finding must anchor to current, verified file paths and exact line numbers read through local inspection tools. Never report theoretical vulnerabilities based on typical project layouts. If a file or line was not inspected, do not report it.
- Anti-tunnel-vision rule: Do not spend more than 30% of total audit effort on a single file or subsystem. Establish breadth across all in-scope entry points before deep-diving into individual implementations.
- Impact floor: Apply the "If we skip it" test to every candidate finding. If skipping a finding produces no operational failure, data corruption, security exposure, or measurable maintenance drag, drop the finding or classify it under optional suggestions.
- Redact secrets: Report secrets and credentials by location and token type only. Never output raw credential values in reports or logs.

## 3. Execution Controller

### Phase 1: Reconnaissance and Surface Mapping

1. Inventory repository structure, technology stack, dependency manifests, build scripts, entry points (HTTP routes, CLI commands, message queues, jobs), and existing test suites.
2. In `deep` mode: classify all directories into core application code, embedded third-party libraries, generated code, or build artifacts.
3. Map the endpoint-permission matrix: identify which entry points are public, which require authentication, and which enforce role-based authorization.
4. Fast exclusion: identify technologies not present in the repository and discard irrelevant inspection tracks.

Exit criterion: Complete list of in-scope entry points, declared load baseline, and explicit list of excluded or unexamined areas.

### Phase 2: Dual-Track Investigation

Audit concurrently along two complementary tracks:

- Track A (Sink-Driven for Active Flaws):
  Search for hazardous sinks (raw SQL execution, command execution, path traversal, deserialization, SSRF, memory leaks, unsafe concurrent state modification).
  Trace backward from the sink to the source to prove user-controlled or untrusted input reaches the sink without effective sanitization, validation, or parameterization.

- Track B (Control-Driven for Missing Guards):
  Enumerate all entry points and critical state transitions from Phase 1.
  Verify whether expected controls actually exist: authentication middleware, authorization checks, tenant isolation filters, database transaction boundaries, mutexes or locks on shared state, and rate limits.
  Flag endpoints that lack expected guards on non-public paths. Grep cannot locate missing code; verify controls by inspecting route definitions and handler chains directly.

Consult [Audit Lenses](references/lenses.md) for detailed investigative prompts across correctness, security, reliability, performance, architecture, tests, and lean bloat elimination.

Exit criterion: Every candidate finding substantiated with a complete causal path from source to sink or verified absence of required controls.

### Phase 3: Verification and Taint Tracing

1. Trace dataflow across each intermediate transformation: Source -> Sanitizer/Validator -> Intermediate Data Structures -> Sink.
2. Verify existing mitigating controls (framework-level auto-escaping, ORM query parameterization, CSP headers, middleware filters). If a mitigating control neutralizes the flaw, dismiss the finding or document it under defense-in-depth suggestions.
3. Attack chain construction: In `deep` mode, evaluate whether low-severity findings (such as verbose error leakage or unauthenticated info endpoints) combine into high-severity exploit chains (such as credential harvesting leading to administrative takeover).
4. Corroborate execution behavior using existing non-destructive tests or safe static analysis tools when available locally.

Exit criterion: Each finding assigned a verified confidence level (High: proven causal path; Medium: established defect, secondary environment factors unverified; Needs validation: plausible risk requiring dynamic runtime verification).

### Phase 4: Triaged Reporting and Lean Accounting

Structure the report following [Report Guidance](references/report.md):

1. Header: Target, audit mode (`quick`, `standard`, `deep`), assumed operational load, and one-line Verdict.
2. Findings grouped by actionability:
   - Must Fix: Bugs producing incorrect outputs, exploitable security vulnerabilities, data loss or corruption, or crashes under assumed load.
   - Should Fix: High-risk code paths lacking test coverage, real performance bottlenecks, architectural boundary leaks, and missing transaction guards.
   - Nice to Have: Minor optimizations, documentation gaps, and non-critical cleanups.
3. Every finding formatted with four required elements:
   - What this is: concise context and component role.
   - Problem and causal path: triggering conditions and exact failure mechanism with line references.
   - Minimal root-cause fix: smallest complete correction, preferring deletion over added layers.
   - If we skip it: concrete operational failure or business consequence.
4. Lean Ledger: Quantify opportunities to delete dead code, replace third-party dependencies with native standard libraries, and eliminate YAGNI abstractions (`Lean: -<N> lines, -<M> dependencies possible`).
5. Blindspots and Unexamined Areas: Explicitly list files, subsystems, or dependencies that were sampled or left unexamined.

Exit criterion: Complete structured report delivered in conversation without modifying repository code.
