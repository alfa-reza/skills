# Platform and runtime considerations

Choose the applicable sections. The following tools are examples, not prerequisites or a whitelist. Prefer existing project tooling and verify commands, features and versions using official documentation. Select metrics and test conditions from the actual target, not the language name alone.

## Native and systems software (C, C++, Rust, Zig, etc.)

- Measure representative optimized builds with appropriate symbols for profiling. Account for target architecture, compiler flags, LTO/PGO and feature sets. Keep these constant except when they are the subject of the experiment.
- Check ownership, aliasing, allocation, data locality, destructor/drop timing, ABI and FFI contracts, synchronization and integer/floating-point semantics. Do not introduce undefined behavior, unsafe code or relaxed ordering merely to reduce benchmark numbers.
- Candidate tools: platform profilers (Linux perf, Instruments, Windows Performance Analyzer), Google Benchmark, Criterion, native runtime or compiler diagnostics. Consume benchmark results and verify generated code if optimizer elision is suspected. Validate wall time on real target hardware when claiming latency gains.

## Managed runtimes (Java, Kotlin/JVM, .NET, etc.)

- Separate cold start, JIT/tiered compilation and warmed execution. Control GC mode, heap settings, process forks and data shape. Track allocation pressure, retained memory, pauses, thread-pool queues and contention.
- Distinguish CPU execution from blocked threads and asynchronous tasks. Preserve synchronization, exception and cancellation semantics. Use existing tools such as JFR/JMC, JMH, dotnet-trace, BenchmarkDotNet or runtime counters when available.
- For Kotlin Multiplatform and other cross-platform targets, JVM evidence does not establish native, browser or mobile performance. Validate relevant source sets and platform-specific lifecycles separately.

## Go

- Distinguish CPU, heap, allocation, goroutine, blocking and mutex evidence. Track scheduler behavior, GC, concurrency, escape/allocation and resource lifetimes; check channel, cancellation, and synchronization contracts.
- Use existing Go benchmarks, pprof, runtime tracing and benchstat where appropriate. Record toolchain, GOMAXPROCS, build tags, data sizes and concurrency. Allocation counts and time are different objectives; fewer allocations do not automatically prove lower latency.

## Dynamic languages and scripting (Python, JavaScript/Node.js, Ruby, PHP, shell, etc.)

- Inspect interpreter/runtime and execution mode, native extensions, process boundaries, dynamic dispatch and expensive cross-language conversions. Measure full process cost if startup matters.
- Check event-loop lag, blocking work, worker overhead, GC, interpreter locks where applicable, and lazy evaluation. Preserve coercion, evaluation order, iteration and error behavior.
- Candidate tools: cProfile/pyperf, runtime sampling profilers, Node.js CPU/heap profiles, existing benchmark harnesses and CLI timers. Measure subprocess and I/O overhead separately when useful. Do not assume a language rewrite is necessary or authorized.

## Web, mobile and desktop UI

- Measure the interaction the user experiences: time to visible/usable content, input response, frame-time distributions, dropped frames, scrolling, startup and memory. For web applications, use the applicable field and lab metrics, including LCP, INP and CLS where relevant; lab results are not field results.
- Separate JavaScript, native execution, layout, paint, raster/compositing, network and waiting. Confirm windows are active and frames actually produced; background tabs, throttling, headless rendering and software GPUs can distort results.
- Measure production-equivalent UI, representative device sizes, hardware, refresh rates, data and network conditions. Browser traces, framework profilers, Android Studio/Perfetto and platform instruments are possible tools.
- Reduce unnecessary invalidation and work frequency before blanket memoization. Preserve layout, visual quality, input behavior, focus, accessibility, navigation, animation and state identity. Verify the actual rendered interaction, not just a helper or render count. Do not hide required content or defer it beyond the measured endpoint.

## Audio, games, GPU and real-time workloads

- Measure per-frame or per-callback deadlines, jitter, underruns, queue depth, CPU/GPU utilization, memory and end-to-end latency as appropriate. Average FPS or average callback duration can conceal severe stutter.
- Confirm GPU commands complete when measuring GPU time; CPU submission time alone is insufficient. Use representative scenes, assets, quality settings, drivers, hardware, thermal state and workload duration.
- Avoid introducing blocking work, unbounded allocation, I/O or contention on deadline-sensitive paths. Respect thread affinity, resource ownership, synchronization, input timing and cancellation. Preserve audio/visual quality, simulation correctness, input latency and determinism where required. Larger buffers and lower quality are explicit trade-offs, not silent wins.

## Databases and distributed systems

- Use real query shapes, parameter distributions, cardinality, statistics, transaction isolation and cache conditions. Inspect plan estimates vs actual rows, loops, access methods, reads, spills, locking and query count. Estimated plan cost is not elapsed time.
- Execution-plan modes that analyze real execution may run the statement, including writes or expensive reads. Use a safe isolated environment and appropriate authorization; rollback is not a universal guarantee against side effects. Check database-specific documentation.
- Indexes can improve reads but increase write, storage and maintenance costs; verify the representative workload. Preserve result sets, ordering where promised, NULL/duplicate semantics, transactions, authorization and consistency. Do not introduce migrations or tune a live database without permission.
- Trace external latency, retries, connection reuse, pool saturation, queueing, fan-out and critical paths. Separate service time from user-observed time. Compare useful throughput at comparable load and errors, accounting for all affected services and costs. Avoid transferring load to a downstream bottleneck or loosening durability/freshness unnoticed.

## Other runtimes

For an unlisted language, compiler, embedded target, ML pipeline or specialized workload, identify its execution model, measurement tooling, semantic constraints and deployment environment. Use equivalent principles; do not force an unrelated runtime's harness. For model inference or approximate numerical workloads, preserve quality, accuracy and determinism requirements and account for batching, accelerator utilization, memory, pre/post-processing and end-to-end latency.
