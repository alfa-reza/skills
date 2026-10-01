# Optimization candidates and trade-offs

This is a selection guide, not a checklist of changes to apply. Each candidate needs a credible cost mechanism, appropriate evidence and preservation of the performance contract.

| Measured problem | Candidate | Check before keeping |
|---|---|---|
| Work scales poorly with input | Better algorithm, index, lookup structure, incremental computation | Worst case, memory, ordering, equality, duplicates, numeric precision |
| Repeated equivalent expensive work | Hoist invariant computation, share result, remove redundant passes | Inputs actually invariant; side effects, lifetime, identity and invalidation |
| Excessive work per event | Narrow dependency/invalidation scope, coalesce or batch | Event delivery, ordering, fairness, freshness, deadline and cancellation |
| Repeated data access or N+1 | Fetch in batches, joins, prefetch required fields | Cardinality, consistency, permissions, memory, round trips and failure semantics |
| Excessive allocation or copying | Preallocate when justified, reuse bounded buffers, borrow, stream | Aliasing, ownership, retained memory, lifetime, stale/sensitive data, concurrency |
| Poor locality | Contiguous layout or access order | ABI, data layout, alignment, platform and real hardware measurements |
| CPU-bound work on critical thread | Reduce work; offload or parallelize independent tasks | Transfer, synchronization, scheduling, CPU quotas, cancellation, UI thread rules |
| Lock or pool contention | Reduce lock duration/work, partition where appropriate, right-size bounds | Atomicity, lock order, fairness, backpressure and downstream limits |
| I/O overhead | Buffer or batch, reduce calls, use appropriate asynchronous I/O | Flush semantics, durability, partial failures, buffer limits and latency |
| Payload/serialization cost | Avoid redundant conversions, transfer necessary data, compression if justified | Schema compatibility, accuracy, decompression/CPU cost, security |
| Rendering cost | Reduce invalidation, virtualize or defer offscreen work | Layout, focus, state identity, accessibility, scroll and interaction behavior |
| Startup or bundle cost | Eliminate unused initialization/dependencies, defer optional work | Actual time to usable readiness, first-use latency, errors, side effects |
| Proven arithmetic or loop bottleneck | Compiler-friendly code, vectorization, specialization | Equivalent semantics, overflow, floating point, portability, maintenance |

## Prefer less work over clever work

Remove unnecessary work only after establishing it is unnecessary, including under rare inputs or failure paths. Improve algorithmic scaling where the real workload warrants it. A simpler linear scan may beat a hash structure for small inputs; a more complex algorithm may be justified at large scale. Prefer existing idioms and libraries over custom mechanisms when their behavior and cost fit. Do not introduce a framework or generic abstraction for a single performance problem without concrete benefit.

## Caching and memoization

First ask whether the operation can be avoided without storing results. If caching is justified, define the key and ownership, freshness/consistency requirements, invalidation triggers, memory or item bounds, eviction, concurrency, failure behavior and tenant/authorization isolation. Measure hit and miss paths, construction and invalidation costs, retained memory, and contention. Avoid stale or incomplete results, unbounded growth, cross-user data leakage, and stampedes. Memoization itself consumes work; it is not free.

## Concurrency, batching and scheduling

Parallel execution can reduce wall time while increasing CPU, memory, or contention. Asynchronous execution does not make CPU work cheaper. Choose bounded concurrency and explicit backpressure when introducing more simultaneous work; preserve ordering, error aggregation, cancellation and resource cleanup. Batching can reduce overhead but add wait time or alter transaction boundaries. Debounce, throttle and coalescing can drop or delay events; use only if the contract permits the new semantics. Do not move work to another thread, process or service and ignore its cost.

## Low-level and resource trade-offs

Use profiling and toolchain evidence before manual inlining, unrolling, SIMD, allocator changes, specialized containers or unsafe operations. Preserve safety and portability; use safe compiler-supported alternatives first. Measure on the hardware and compiler configuration the claim concerns. Changes to numerical tolerance, validation, cryptography, authorization, durability, compression quality, consistency or observable output are not free optimization knobs. If needed, surface the trade-off explicitly and obtain authorization before weakening a guarantee.

Do not remove observability, audit logging, error handling or safeguards just to make a benchmark pass. Where overhead is proven, seek an equivalent mechanism, appropriate sampling, or an agreed policy change. Evaluate speed vs memory, latency vs throughput, initialization vs first use, and local gains vs downstream cost.

## Decide whether to retain a change

A theoretical improvement, fewer allocations or a prettier profile may be useful evidence for a specific objective, but does not alone establish a user-visible speedup. Retain only justified improvements; if a candidate is slower, inconclusive or adds unreasonable complexity, abandon it or refine the experiment. Test affected workloads and guardrails before concluding. Do not clean up unrelated code or generalize an experimental mechanism merely because it succeeded once.
