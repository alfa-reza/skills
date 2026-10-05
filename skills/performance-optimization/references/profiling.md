# Profiling and bottleneck attribution

## Start from the symptom

Map the path from workload entry to useful completion: application execution, scheduling, locks, queues, rendering, persistence, network, external services. Identify where elapsed time or resource demand accumulates. Use existing observability first. A slow component may be a victim of a downstream bottleneck; a busy component may be doing necessary work off the critical path.

For service workloads, look at latency, traffic, errors and saturation together. For each relevant resource, inspect utilization, queued demand and errors (USE methodology). Include CPU cores/quotas, memory pressure, disk, network, GPU, pools and locks as appropriate. Low average utilization does not rule out short bursts, a saturated core, a quota or a serialized section. High utilization alone does not prove a bottleneck.

| Symptom or evidence | Investigate next |
|---|---|
| High CPU per unit or core saturation | CPU sampling, call stacks, invocation counts, algorithm scaling, hardware counters if relevant |
| High elapsed time with low CPU | Off-CPU/wall-clock profiles, traces, queue time, I/O, lock or scheduler waits |
| Rising memory or frequent GC | Allocation rate, retention paths, native memory, caches and queue growth; compare equivalent phases |
| Throughput flattens as load grows | Resource saturation, limits, contention, backpressure, downstream capacity |
| Slow tail or periodic spikes | Slow traces, GC, retries, cold paths, periodic tasks, lock holds, throttling |
| UI stutter or real-time deadlines missed | Main/render/audio thread timeline, frame or callback duration, allocation and blocking work |
| Startup slow | Ordered initialization trace, dependency loading, disk/network, work needed before readiness |
| Database dominates | Query counts, plan, actual rows/loops, reads, locks, pool waits, result transfer |
| Network dominates | Request waterfall, payload bytes, handshake/reuse, retransmission, retries, dependency latency |

## Attribute cost correctly

- Sampling estimates where sampled execution or waiting time occurred. Verify whether the profiler samples CPU, wall time, allocations, blocked stacks, or events. Check sample coverage, symbols, process/thread scope, inlining and asynchronous stack limitations.
- Inclusive cost includes descendants; self cost attributes work to the frame itself. A wide parent may expose excessive calls rather than expensive local instructions. A flame graph's horizontal position generally is not chronology; interpret width using the selected event and unit. Do not sum overlapping inclusive costs.
- A CPU profile alone cannot explain waiting. Trace dependencies and critical paths; parallel durations cannot simply be added to explain total elapsed time. Distinguish time in a queue from execution time.
- Quantify frequency and multiplicative dimensions (items x consumers x events x operations). Identify avoidable repeated scans, recomputation, serialization, polling, invalidation, and hidden fan-out. Small costs repeated often can dominate large workloads.
- Verify actual behavior with targeted counters or a controlled experiment. Ablation can test a hypothesis in an isolated diagnostic run, but skipping work changes the contract: do not report that experiment as a valid optimized result.
- For memory, distinguish allocations from retained objects, leaks from bounded caches, and heap from overall process footprint. Allocator retention is not necessarily a leak. For hardware counters, validate collection, scope, sampling and multiplexing before interpreting ratios.

## Choose and operate tools

Use a tool available and appropriate to the installed runtime and failure mode. Prefer low-overhead sampling or existing traces for initial attribution. Use instrumentation or detailed tracing for a targeted question when sampling is insufficient. Validate the overhead and remeasure without intrusive probes. Read tool documentation before selecting version-specific modes; avoid installing a tool merely because it appears in a reference.

Local profiles, debug symbols, heap dumps and traces may contain credentials, inputs, paths or customer data. Collect only what is required; use approved environments, scrub sensitive fields, bound output size and retention, and avoid uploading artifacts without permission. Do not attach invasive instrumentation to a live shared system or change privileges without authorization.

## Rank and test hypotheses

For each meaningful candidate, identify the location, observed cost, cause of that cost, expected mechanism of improvement, affected contracts, and a probe that distinguishes it from alternatives. Favor improvements on the target's critical path or limiting resource. Estimate the maximum plausible benefit from the affected fraction of work rather than assuming a local speedup transfers directly. If several factors interact, isolate what can be isolated and test the coherent change as a unit.

After a change, verify the target metric and profile again if the result is unexpected or the bottleneck has moved. A different profile percentage can result from changes elsewhere; compare absolute cost per equivalent workload. If a probe contradicts the hypothesis, revise the diagnosis rather than forcing the candidate.
