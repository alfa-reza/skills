# Measurement and benchmark design

Use this reference to establish comparable evidence, not as a mandatory ceremony for every edit. Choose the measurement that answers the performance contract in SKILL.md.

## Select meaningful metrics

| Objective | Primary evidence | Guardrails and context |
|---|---|---|
| Interactive latency | End-to-end time until the user can act; relevant percentiles, frame times or deadline misses | Input size, device, errors, dropped frames, accessibility |
| Service latency | Distribution of successful and failed request durations, per operation and load level | Arrival rate, concurrency, timeout/error rate, queue depth |
| Throughput | Successfully completed units per time | Latency budget, offered load, dropped/rejected work, quality |
| CPU efficiency | CPU time or cycles per completed unit, process/thread scope | Wall time, workload, frequency, concurrency |
| Memory | Relevant peak or steady-state footprint; retained/live heap and allocation rate as needed | GC, native memory, off-heap/GPU memory, workload duration |
| Startup | Time to useful readiness, including first required operation | Cold/warm process and cache state, deferred initialization |
| Storage/network | Bytes and operations per completed unit, latency, bandwidth | Compression cost, durability, retransmission, data equivalence |
| Cost/energy | Actual billed units or energy per equivalent completed workload | Pricing/configuration, accuracy, power conditions, time and error rate |

Select mean, median, tails, maxima or distributions according to the question. A mean alone hides tail behavior; a p99 from too few independent observations is unstable. Avoid combining endpoint percentiles by averaging them. Do not use throughput that counts failures or less work as useful output.

## Design the comparison

1. Freeze the comparison scope. Identify both source states, tool and dependency versions, compiler flags, runtime settings, hardware/container limits, fixture or dataset, seed, concurrency, and relevant external services. For a configuration optimization, the intended setting may differ; other material conditions should not.
2. Use representative small, typical, large and adversarial cases as appropriate, including skew, empty inputs, cache misses, and expensive paths. Avoid optimizing only the convenient fixture. For stateful tests, reset or control state, and count real successful work.
3. Select a measurement boundary: microbenchmark, component, end-to-end, or production telemetry. Separate setup and teardown only if the contract excludes their cost. Wait for asynchronous work to finish; measure useful completion, not merely scheduling or a readiness flag.
4. Run comparable builds under comparable load. Separate cold startup from steady state; allow relevant JIT, cache or connection warmup when measuring warm behavior. Record power/thermal throttling, CPU quotas, frequency scaling, competing jobs and network variation where material.
5. Repeat with sufficient duration, independent processes and samples for the effect size. Interleave or randomize baseline/candidate runs when drift could bias comparisons. Preserve raw measurements or reproducible references for review; do not cherry-pick favorable runs or discard outliers without explaining the cause.
6. Check measurement liveness: independent output assertions, known work counters, or a positive control when a probe may be wrong. Check profiler and load generator overhead. A zero metric can indicate an instrument that never ran.

## Microbenchmark pitfalls

Use the project's established harness. Check that results are consumed, inputs are not trivially constant-folded, the compiler has not removed the operation, and setup is not accidentally dominant. Inspect emitted code when relevant. A benchmark barrier has specific semantics and does not disable all optimizations. CPU time for one thread can miss worker activity; measure process CPU and wall time according to the target. Instruction counts and simulation are proxies for hardware latency, not interchangeable with it. A useful local speedup can still be negligible at application scale.

## Load and scalability

Model the real demand. Fixed-user/concurrency (closed) workloads suit clients that wait for responses; independent arrivals (open) suit external request streams. Closed workloads may send less traffic as the service slows, masking overload for an arrival-rate objective. Measure intended vs achieved arrivals, dropped iterations, errors and timeouts; ensure the generator is not the limiting resource. Compare equivalent offered load, data and completion criteria. Use controlled ramp, steady-state and recovery phases; soak only when duration-dependent behavior matters. Avoid unbounded stress. Measure scale curves rather than extrapolating one point.

## Interpret results

- For lower-is-better metrics, reduction = (baseline - candidate) / baseline. For higher-is-better metrics, increase = (candidate - baseline) / baseline. Multiply by 100 for percent; report absolute changes when the baseline is zero or unsuitable for ratios.
- Speedup for equivalent work is baseline duration / candidate duration. Do not confuse percent time reduction with percent throughput gain.
- Compare distributions or paired runs, not just a selected best number. Use effect sizes, spread and uncertainty intervals when justified by the sampling design. Statistical significance alone is not practical significance. Do not treat correlated samples as independent.
- Separate observed measurements, extrapolations and theoretical complexity. Do not claim no regression from underpowered or unrepresentative tests. If the result overlaps ordinary noise or environments differ materially, mark the comparison inconclusive.

For final results, use the same benchmark definition before and after. If the harness changes, rerun both versions using the corrected harness. A historical number is a valid comparison only if its workload and measurement conditions are sufficiently comparable.
