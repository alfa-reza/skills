---
name: performance-optimization
description: Convert performance problems into a bounded, measured optimization loop across algorithms, runtimes, queries, memory, and web metrics. Use when code is slow, fails latency/throughput budgets, exhibits memory leaks or CPU spikes, has slow database queries (N+1), or needs profiling-guided optimization.
---

# Performance Optimization

Stop. Performance work without measurement is guessing. Never apply speculative optimizations, caching layers, or concurrent execution based on intuition. Measure baseline first, isolate the bottleneck with profiling data, and verify that test suites remain green.

Strictly address computational and system performance (latency, throughput, memory, I/O, queries). Not for prompt tuning, code aesthetics, or general refactoring.

## 1. Required Baseline Prerequisites

Do not write optimization code until these five elements are established:

1. **Target operation:** The exact function, endpoint, query, or user flow being optimized.
2. **Correctness gate:** The test suite or invariant check that must remain 100% green.
3. **Primary metric:** Wall time, p95/p99 latency, throughput (req/sec), heap memory, or Core Web Vitals (LCP, INP, CLS).
4. **Measured baseline:** Concrete numerical value measured under controlled, reproducible conditions.
5. **Search budget:** Explicit limit on variants tested, scope boundaries, and allowed blast radius.

## 2. The Measured Optimization Loop

Execute in strict sequential order:

1. **Measure baseline:** Run the representative workload and record baseline figures. Differentiate cold startup from steady-state warm runs.
2. **Profile bottleneck from evidence:** Trace execution to determine cost location:
   - On-CPU: algorithmic complexity, heavy serialization, regex backtracking.
   - Off-CPU: I/O waits, lock contention, queueing delays, network roundtrips.
   - Database: sequential scans, unindexed joins, connection pool exhaustion (`EXPLAIN ANALYZE`).
3. **Form single-hypothesis variants:** Change one variable per variant so performance deltas are directly attributable.
4. **Benchmark each variant:** Run under identical input distributions, concurrency, and hardware conditions.
5. **Filter by correctness gate:** Reject any variant that breaks assertions, alters observable output, or skips required work.
6. **Track in Variant Ledger:** Record every attempt (including reverted ones) to prevent repeating discarded ideas.
7. **Promote the fastest safe variant:** Merge only the best measured safe variant that beats measurement noise.
8. **Re-run baseline vs. winner:** Validate the final delta under clean conditions and codify in source control.

## 3. Variant Ledger

Track all experimental runs using this format:

```text
Variant | Hypothesis | Command / Change | Metric (Before -> After) | Correct? | Verdict
baseline | current implementation | npm run benchmark | 120ms (p95) | yes | stable
batch-50 | batch queries to fix N+1 | include related models | 42ms (p95) | yes | promoted
cache-mem | in-memory cache | add node-cache layer | 40ms (p95) | no | stale tenant data
```

## 4. Promotion Gate (Decision Matrix)

Apply this strict evaluation table to every candidate variant:

| Result vs. Baseline | Action |
|---|---|
| Statistically improved delta and suite green | **Keep** |
| Within measurement noise (delta < variance threshold) | **Revert** |
| Regressed (higher latency, memory, or throughput drop) | **Revert** |
| Faster metric, but any correctness check fails | **Revert** (regression disguised as performance) |

- **Neutral is a revert, not a keep:** Code that fails to measurably improve performance is dead weight. Discard it immediately; never leave unverified complexity in the codebase.
- **Correctness gates the metric:** Never win performance by weakening security, skipping input validation, omitting required `await` calls, or caching stale tenant data.

## 5. Domain Optimization Heuristics

- **Algorithmic:** Convert $O(n^2)$ nested lookups to $O(n)$ via Map/Set structures; hoist invariant computations outside loops; avoid sorting inside iterations.
- **Database & Queries:** Eliminate N+1 queries via batching or joins; add composite indexes matching query filter order; paginate unbounded result sets; right-size connection pools.
- **Network & API:** Batch independent requests with concurrent primitives; stream large payloads; enable HTTP/2 or keep-alive; eliminate redundant waterfall requests.
- **Frontend & Web Vitals:** Split bundles dynamically; lazy-load below-the-fold media; enforce image dimensions (CLS); break long tasks >50ms off the main thread (INP).
- **Memory & Resources:** Profile heap snapshots; eliminate uncollected listeners or closure leaks; bound caches with explicit eviction policies.

## 6. Output

Report in the user's language. Present measured figures first:

- **Target:** `<operation, workload, and primary metric>`
- **Bottleneck:** `<profiled location and decisive evidence>`
- **Winner:** `<best measured safe variant and mechanism>`
- **Delta:** `<baseline vs. candidate numbers, units, and percentage improvement>`
- **Verification:** `<correctness suite status, regression checks, and trade-offs>`

## 7. References (conditional loading)

Read relevant references only when specific technical guidance is required:

- [Measurement](references/measurement.md): benchmark design, metric selection, and statistical interpretation.
- [Profiling](references/profiling.md): on-CPU vs. off-CPU attribution, sampling overhead, and bottleneck tracing.
- [Techniques](references/techniques.md): candidate solutions, caching trade-offs, and batching constraints.
- [Platforms](references/platforms.md): language-specific runtime details (Native, JVM, Go, Node.js, Web UI, GPU, SQL).
