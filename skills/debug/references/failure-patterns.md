# Failure patterns and discriminating checks

Select the relevant symptom category. These are candidate probes, not diagnoses or a language whitelist. Confirm the actual compiler, runtime, operating system, dependency version, and execution environment. Use their official documentation for tool behavior and semantics when needed.

| Failure | Initial evidence and differentiating checks |
|---|---|
| Build / compilation | First causal diagnostic rather than cascades; actual build target, compiler flags, generated sources, imports, dependency resolution, lockfile, ABI and SDK compatibility. Compare clean and incremental builds in isolation if stale outputs are suspected. |
| Test failure | Asserted contract, actual values, fixture correctness, setup/teardown, prior baseline, isolation versus full suite, timing and test doubles. Determine whether test or implementation is wrong. |
| Incorrect output | Trace inputs and transformations; examine boundary values, parsing, units, precision, rounding, locale, encoding, nullability, ordering, and fallback branches. |
| Crash / exception | Original exception and causal chain, matching source symbols and build, stack frames and first invalid state. In native code, inspect relevant dump and sanitizer findings. |
| Hang / deadlock | Thread/task stacks, wait-for relationships, lock ordering, blocked I/O, cancellation and queue saturation; distinguish deadlock, livelock, starvation, and slow progress. |
| Flaky / race | Reproduction rate, interleavings, shared state, cancellation, task ownership, test order, lifecycle and external timing. Compare with and without instrumentation. |
| Memory / resource leak | Retention paths, allocation and release rates, handles, listeners, timers, sockets, buffers, ownership and lifecycle. Distinguish retained objects from allocator caching or intentional pooling. |
| Performance regression | Comparable baseline and workload; latency distribution, CPU, allocation, I/O, waits, throughput, warmup, contention and profiling. Measure first; optimize separately if no defect is demonstrated. |
| Integration / API | Actual request and response shape, status, protocol version, timeout semantics, authentication, serialization, proxy behavior, retry and idempotency contracts. Inspect each relevant boundary. |
| UI / state | User action sequence, state before/after, event ordering, render identity, lifecycle, persistence, focus, accessibility and platform differences. Verify the running app where possible. |
| Data / database | Exact query and parameters, transaction boundaries, isolation, cardinality, NULL behavior, schema/version, constraints, stale data, query plan and migration state. Use read-only or disposable data. |
| Configuration / deployment | Effective configuration, precedence, process environment, build artifact identity, deployment revision, flags, permissions, DNS and network path. Compare actual state, not just configuration files. |

## Async and timing-sensitive failures

Establish which event must precede which other event. Trace task ownership, state updates, cancellation, resource acquisition/release, and all completion or failure paths. Prefer deterministic coordination primitives, event completion, or a bounded condition wait over guessing a delay. A condition wait must have a meaningful predicate, timeout, cancellation, and useful failure message; do not busy-wait or conceal an error. Real timing requirements (debounce, timeouts, audio deadlines) still need tests of time itself, preferably with controllable clocks where representative.

In a disposable environment, controlled scheduling, repeated runs, load, or diagnostic delays can expose race windows. Such delays are experimental, not the fix. Do not introduce unbounded stress. Reproduce under representative execution as well. A debugger can serialize execution and hide the defect.

## Runtime-specific considerations

- Managed runtimes: inspect heap retention, task/thread dumps, GC pressure and finalization behavior rather than assuming high memory is a leak.
- Native and systems languages: use symbolized crashes, ownership and bounds checks, sanitizers or race detectors as supported. Distinguish undefined behavior from a reproducible symptom; absence of a sanitizer warning is not proof of safety.
- Dynamic languages: verify actual dispatch, coercion, imports, monkey patches, environment and async execution; static references may miss the running path.
- Multiplatform projects: confirm shared versus target-specific implementation, compiler/runtime versions, permissions, native interfaces, lifecycle and packaging. A successful test on one target does not verify another.
- Browser/mobile/desktop: inspect live UI, logs and network at the relevant layer; distinguish rendering, event handling, application state, OS/device services, and backend effects.
- Audio, video and real-time systems: distinguish playback clock, decoded data, processing state, queues, output device, underruns, scheduling and recovery. Trace control changes and state transitions; avoid instrumentation that disrupts the timing being measured.

Choose native debugging facilities or already authorized tools; do not install or enable privileged tooling automatically. Apply the same principles to unlisted languages, embedded devices and other runtimes.
