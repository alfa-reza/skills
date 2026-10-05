# Diagnostic Patterns and Failure Modes

Select the diagnostic strategy matching the observed symptom. Use these probes across languages and runtimes to localize the initial divergence between expected and actual behavior.

## 1. Failure Categories and Differentiating Probes

| Failure Category | Initial Evidence and Discriminating Probes |
|---|---|
| Build / Compilation | Isolate the first causal compiler diagnostic instead of cascading errors. Verify build target, compiler flags, generated code, imports, lockfile resolution, ABI compatibility, and SDK versions. Compare clean versus incremental builds in an isolated directory to rule out stale build artifacts. |
| Test Failure | Inspect the violated assertion contract versus actual observed output. Verify fixture validity, setup/teardown state, test isolation versus full-suite execution, and mocking boundaries. Determine whether the defect is in the implementation, the test assertion, or the test harness environment. |
| Incorrect Output | Trace data transformations backward from output to input. Inspect boundary values, off-by-one errors, parser logic, precision loss, locale/timezone offsets, character encoding, nullability, sorting stability, and default fallback branches. |
| Crash / Panic / Exception | Inspect the original unhandled exception and full causal stack trace. Match source symbols with the exact running build revision. Identify the first invalid state transition; the line that throws is frequently a downstream victim of an earlier corrupted invariant. In native code, inspect core dumps, ASan/TSan reports, and signal numbers (SIGSEGV, SIGBUS, SIGABRT). |
| Deadlock / Hang | Capture thread and task stack dumps. Map wait-for dependency graphs, lock acquisition order, unbuffered channel operations, blocked socket I/O, thread pool exhaustion, and unhandled promise cancellation. Distinguish between true deadlock, livelock, starvation, and slow progress. |
| Flaky / Race Condition | Calculate the baseline reproduction rate under repeated executions (e.g., 20 to 100 iterations). Inspect shared mutable state across threads, coroutines, or processes without synchronization primitives. Compare execution timing with and without instrumentation. |
| Memory / Resource Leak | Measure retention paths, handle counts, open file descriptors, active database connections, and background timers over time. In managed runtimes, distinguish intentional allocator caching or GC thresholds from actual object retention. In native runtimes, check missing frees, circular references, and ownership leaks. |
| Performance Regression | Establish a comparable baseline under identical workloads. Profile CPU usage, latency distributions (p50, p95, p99), allocation volume, garbage collection pauses, disk I/O, database query execution plans, and lock contention. Measure before changing code; optimize only against a demonstrated defect. |
| Integration / Network / API | Inspect raw HTTP/gRPC request and response payloads, status codes, protocol versions, headers, TLS handshakes, timeout thresholds, and retry policies. Check client library version compatibility against the upstream service specification. |
| UI / Hydration / State | Inspect user action sequences, application state transitions, render lifecycles, event bubbling, DOM mutations, and server-vs-client hydration mismatches. Differentiate browser rendering artifacts from underlying state corruption. |

## 2. Universal Runtime Considerations

- Native and systems languages (C, C++, Rust, Zig):
  Verify memory ownership, pointer arithmetic, bounds checking, lifetime annotations, and undefined behavior. Use address sanitizers (ASan), thread sanitizers (TSan), undefined behavior sanitizers (UBSan), and Valgrind where supported. Absence of a sanitizer warning does not prove absence of undefined behavior.

- Managed runtimes (Go, Java/Kotlin, C#, Node.js/V8):
  Inspect heap allocation profiles, thread/goroutine dumps, garbage collection pauses, and finalizer behavior. Do not assume high memory usage indicates a leak without verifying heap growth under forced GC.

- Dynamic and scripting languages (Python, Ruby, PHP, Bash):
  Verify dynamic dispatch, runtime monkey-patching, implicit type coercion, module import ordering, environment variables, and asynchronous event loops. Static references may not match the actual execution path.

- Distributed and asynchronous architectures:
  Trace correlation IDs and causation headers across process boundaries. Account for network latency, packet loss, clock skew between hosts, out-of-order message delivery, and duplicate webhook delivery.
