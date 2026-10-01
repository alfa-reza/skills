---
name: optimize
description: Use when software is slow, resource-intensive, expensive to run, or fails performance budgets; when investigating latency, throughput, CPU, memory, allocations, startup, rendering, I/O, energy, scalability, or performance regressions; and when asked to profile, benchmark, or optimize code, queries, workloads, or systems.
---

# Optimize

Improve a defined performance outcome with evidence. Reduce unnecessary work before making necessary work faster. Preserve correctness, security, supported behavior, and required operational guarantees; performance itself is the intended change.

## 1. Scope and performance contract

- Distinguish analysis, benchmarking, and implementation. Analysis-only requests do not authorize edits to source, tests, configuration, or tracked files. An explicit optimization request authorizes scoped implementation, not unrelated cleanup, dependency migrations, infrastructure changes, or production actions.
- Identify the affected operation, workload, target platform, scale, primary metric and relevant guardrails (correctness, latency, throughput, memory, errors, quality, cost, or energy). Use existing SLOs and requirements. If no numeric target exists, use a reasonable, disclosed comparison objective; ask only when a material trade-off or scope decision cannot be resolved safely. Do not invent requirements.
- Inspect the implementation, callers, existing benchmarks, tests, build configuration and relevant runtime versions. Preserve current user changes. Use existing tools and harnesses first; consult version-matched official documentation for uncertain tool or runtime behavior.
- Distinguish a measured bottleneck, a suspected inefficiency, and a capacity forecast. Static complexity analysis can establish a theoretical cost improvement, not an observed runtime speedup.

## 2. Establish a trustworthy baseline

- Reproduce the actual workload or select a justified representative workload. Record the original code state, environment, build mode, data scale and distribution, concurrency, cache state, measurement method, and relevant baseline metrics.
- Measure before changing the target implementation. Use the smallest valid measurement that answers the question; use production-equivalent builds for runtime claims about shipped software. Separate diagnostic instrumentation from final timing when its overhead matters.
- Confirm the intended work actually executes and completes correctly. Control relevant confounders, repeat measurements as needed, and quantify variability. Measure cold and warm states separately when relevant. Do not compare incompatible workloads or silently exclude failures.
- If a baseline is unavailable, try safe local measurements, operation counts, existing trustworthy telemetry, or an isolated equivalent environment. Label proxies and limitations. Do not present speculative changes as measured improvements. If runtime validation is impossible, default to recommendations; implement a provisional change only within authorization when its correctness and risk are adequately established, and report it as unverified.

## 3. Locate and explain the bottleneck

- Start at the measured symptom and follow the actual execution path. Separate elapsed time, CPU work, queueing, blocked time, resource pressure, and downstream dependencies. Use appropriate profiles, traces, counters, execution plans, or controlled experiments rather than a favorite tool.
- Attribute cost to the responsible work and its frequency. Distinguish inclusive cost from self cost, correlation from causation, and resource utilization from saturation. Look for repeated work, fan-out, poor scaling, contention, allocation churn, or unnecessary transfers.
- Form a testable hypothesis: what change should improve which metric, why, and what observation would disprove it? Rank candidates by expected end-to-end benefit, evidence, risk and maintenance cost. A hot function is not automatically the best target.

## 4. Make the smallest effective change

- Prefer eliminating work, reducing frequency or data volume, improving algorithms and data structures, or fixing inefficient boundaries before micro-optimizations. Apply KISS, YAGNI and DRY without forcing abstractions or removing necessary domain complexity.
- Use one coherent hypothesis per experiment. Keep changes reviewable and reversible; do not overwrite, stash, or revert unrelated work. Reprofile as bottlenecks shift. Do not add speculative caching, concurrency, pooling, dependencies, unsafe code, or architecture solely because they sound faster.
- Preserve outputs, ordering, precision, error behavior, validation, authorization, isolation, consistency, cancellation, accessibility, and resource lifetimes where required by the contract. Do not trade them away silently. Changes to freshness, durability, approximation, compatibility, or other guarantees require explicit agreement if not already authorized.
- Benchmark with safe workloads. Do not install tools, change system privileges, upload traces or private data, run costly external workloads, attach invasive production profilers, or load-test shared/production systems without authorization. Use bounded disposable local artifacts and sanitize collected data.

## 5. Verify and decide

- Repeat the baseline measurement against the candidate under comparable conditions. Inspect correctness tests, actual completed work, errors, and relevant secondary metrics. Check representative end-to-end behavior for claims about user or system impact; a microbenchmark proves only its measured scope.
- Keep an optimization when its benefit is credible relative to measurement noise and its trade-offs are acceptable. If evidence is inconclusive, improve the experiment or report uncertainty. Reject regressions or needless complexity; undo only your own rejected experimental changes safely.
- Add a focused regression guard when warranted (stable benchmark, operation-count test, performance budget, or existing monitoring). Do not add brittle wall-clock assertions to ordinary tests or weaken correctness checks to improve scores.
- Stop when the agreed target is met, worthwhile candidates are exhausted, further gains require disproportionate complexity or risk, or evidence/access is insufficient. No arbitrary percentage threshold or endless search for marginal gains. Remove temporary instrumentation and artifacts unless useful, safe and intended for retention.

## Output

Report in the user's language. Give the measured outcome or current diagnosis first. Group related metrics and use actual values, units, measurement scope, and direction of improvement. Include only material evidence, changes, trade-offs, and verification gaps. Use a compact paragraph for simple cases, a table for several metrics. No routine command transcript, redundant success summary, or unsolicited report file.

Example format (replace placeholders with verified details; omit inapplicable fields):

- **Target:** <operation, workload and primary metric>
- **Bottleneck:** <location and decisive evidence, or unconfirmed hypothesis>
- **Changes / recommendation:** <actual changes or proposed actions, clearly distinguished>
- **Result:** <baseline, candidate, units, comparison and relevant variability; or not measured>
- **Verification:** <correctness checks, material trade-offs and remaining gaps>
