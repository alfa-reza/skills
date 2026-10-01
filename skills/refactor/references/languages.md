# Language and platform risks

Read only the relevant section. These are risk prompts, not exhaustive language rules or requirements to use particular tools. Identify actual versions, compilation targets, runtime, paradigms, and repository checks. When a semantic detail is uncertain, read the matching official reference or installed source rather than assuming behavior across languages.

## Cross-language concerns

Check only semantics that the proposed transformation could affect: evaluation order, laziness, precision, identity, mutation, defaults, exceptions, resource lifetimes and initialization. Internal details may change if supported behavior and invariants remain intact. Use available semantic rename tools and check dynamic consumers separately. A successful compile does not establish behavioral equivalence.

## JavaScript and TypeScript

- Check `this`, closures, hoisting, getters, enumeration, sparse arrays, coercion and promise scheduling. `0 || fallback` uses the fallback, whereas `0 ?? fallback` returns zero; the same distinction applies to false and empty strings. Array pipelines may differ in sparse-array treatment and early exit.
- Check module side effects, ESM/CJS boundaries, package exports, dynamic imports, decorators, framework lifecycle, and public types. TypeScript types may disappear at runtime; compile success does not establish runtime correctness.
- For React and similar UI runtimes, preserve component identity, keys, effect ordering/cleanup, hook rules, and state ownership. Do not introduce memoization by default.
- Sources: [MDN JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript), [TypeScript Handbook](https://www.typescriptlang.org/docs/handbook/intro.html), [React](https://react.dev/learn), [MDN nullish coalescing](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Nullish_coalescing).

## Python

- Check mutable defaults, late-bound closures, generator laziness, context manager cleanup, exception behavior, descriptors, decorators, dynamic attributes, imports and monkey-patching. Moving code can change import side effects and patch targets.
- Preserve public keyword parameters, dataclass/model fields, serialization, and async cancellation. Check packaging entry points and runtime discovery before deleting symbols.
- Sources: [Python language reference](https://docs.python.org/3/reference/), [Python library reference](https://docs.python.org/3/library/).

## JVM: Java, Kotlin and other JVM languages

- Check overload resolution, virtual dispatch, generic variance/erasure, nullability, boxing, identity/equality, annotations, reflection, visibility, and initialization. Renaming public types or changing signatures can break binary consumers even if local source builds.
- For Kotlin, preserve suspend behavior, coroutine scopes, dispatcher and cancellation semantics, inline/reified constraints, and generated data-class contracts. Do not swallow CancellationException when moving error handling across coroutine boundaries. For Compose, preserve state keys, recomposition inputs, effect lifecycle, and stable identity.
- For Kotlin Multiplatform, check `expect`/`actual`, common and platform source sets, native interop, and platform-specific lifecycle. Verify relevant targets; report untested ones.
- Sources: [Java language specification](https://docs.oracle.com/javase/specs/), [Kotlin docs](https://kotlinlang.org/docs/home.html), [Compose docs](https://developer.android.com/develop/ui/compose), [Coroutine cancellation](https://kotlinlang.org/docs/cancellation-and-timeouts.html).

## Go

- Check interface method sets, typed nils, pointer/value receivers, zero values, slice/map aliasing, error wrapping and `defer`. Moving a deferred cleanup into a helper changes when it runs.
- Check goroutine ownership, channels, context cancellation, lock ordering, build tags, package initialization, exported API and generated interfaces. Loop capture semantics depend on language version.
- Sources: [Go specification](https://go.dev/ref/spec), [Go documentation](https://go.dev/doc/).

## Rust

- Check ownership, borrowing, lifetimes, drop order, traits, error propagation, feature flags, macros and API compatibility. Extracting a scope or moving a value can change when a lock guard or other resource is dropped; clones can change costs and sharing semantics.
- Check async cancellation, pinning, Send/Sync bounds, unsafe invariants, FFI layout and ABI. Do not add `unsafe`, broad clones, `unwrap`, or synchronization merely to satisfy the compiler.
- Sources: [Rust Reference](https://doc.rust-lang.org/reference/), [Rust standard library](https://doc.rust-lang.org/std/), [Destructors and drop scopes](https://doc.rust-lang.org/reference/destructors.html).

## C and C++

- Check object lifetime, pointer aliasing, ownership, integer conversions, evaluation order, alignment, undefined/unspecified behavior, RAII, exception guarantees, templates, and overload resolution. An apparent equivalence may depend on compiler settings or the language standard.
- Check binary interfaces, layouts, linkage, macro expansion and conditional compilation where relevant; preserve promised ABI and cleanup behavior. Use available sanitizers and compiler checks for relevant memory or concurrency risk.
- Sources: [C++ standards resources](https://isocpp.org/std), [GCC manuals](https://gcc.gnu.org/onlinedocs/), [Clang documentation](https://clang.llvm.org/docs/).

## C# and .NET

- Check value/reference types, nullable behavior, overloads, async/await, disposal, deferred LINQ execution, expression trees, reflection, events, attributes and dependency injection lifetimes.
- Preserve public assembly/API contracts, serialization names, source generators, configuration binding and framework-specific discovery. A LINQ rewrite can alter query translation and execution boundaries.
- Source: [Microsoft .NET documentation](https://learn.microsoft.com/en-us/dotnet/).

## Swift and Apple platforms

- Check value semantics and copy-on-write, reference identity, ARC, actor isolation, Sendable constraints, async cancellation, optional chaining, property wrappers, protocol dispatch, and Objective-C interop.
- Preserve UI identity, observation, lifecycle, resource ownership, and public ABI/module interfaces where supported.
- Sources: [Swift language guide](https://docs.swift.org/swift-book/), [Apple developer documentation](https://developer.apple.com/documentation/).

## PHP and Ruby

- PHP: check coercion, strict typing, array keys and ordering, references, autoloading, magic methods, traits, attributes, framework container/routing conventions and serialized contracts.
- Ruby: check blocks/procs/lambdas and return semantics, method lookup, dynamic dispatch, mixins, constants, metaprogramming, monkey patches, and framework conventions.
- Sources: [PHP manual](https://www.php.net/manual/en/), [Ruby documentation](https://www.ruby-lang.org/en/documentation/).

## Dart and Flutter

- Check null safety, futures/streams, cancellation and disposal, equality, named parameters, generated code, platform channels and serialization.
- Preserve widget keys, state lifetime, focus, controller ownership, rebuild dependencies, rendering and accessibility behavior. Test supported platforms when applicable.
- Sources: [Dart documentation](https://dart.dev/guides), [Flutter documentation](https://docs.flutter.dev/).

## SQL and data

- SQL: check null semantics, duplicates, ordering guarantees, collation, join cardinality, transaction isolation, triggers, constraints, and dialect-specific behavior. Compare results and side effects on disposable data. Do not treat schema migration as automatically behavior-preserving.
- Check schema contracts, query plans when resource budgets matter, and dialect-specific semantics. Consult the actual database documentation, not a different SQL dialect.

## Shell

- Check quoting, word splitting, globbing, exit codes, pipelines, subshells, signal traps, environment expansion and portability. Moving code into a function or subshell may change state propagation and error handling. Read the actual shell manual and supported POSIX requirements; use isolated fixtures.

## Other languages and paradigms

Do not force any of the above onto an unlisted language. Determine its semantics and idioms from project code and official documentation. For functional languages, consider purity, laziness, pattern matching exhaustiveness and effect boundaries; for embedded and real-time systems, consider memory, interrupts, timing budgets and hardware constraints. For HTML/CSS and declarative UI, check cascade, specificity, ordering, semantics, accessibility and responsive behavior. For generated code, preserve source-of-truth and generation rules. For build and infrastructure definitions, check evaluation phases, resource identity, replacement behavior and deployment contracts; do not apply live changes merely to verify a refactor. Use relevant native tools, not a prescribed universal command.
