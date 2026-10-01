# Techniques and code smells

Use to choose a transformation, not to enforce a checklist. Identify a real maintenance problem; a smell is a lead, not proof. Compare a local improvement, a structural alternative, and no change. Prefer the inverse technique when it is clearer. Names below are common refactoring terms; not every candidate is safe for every language or contract.

| Signal | Candidate | Preconditions and risks |
|---|---|---|
| Unclear identifier | Rename Symbol / Change Function Declaration | Check named arguments, exports, reflection, serialized names and consumers; retain public names where required. |
| Dense expression | Extract Variable | Preserve conditional evaluation, side effects, number of evaluations and scope. |
| Mixed responsibilities | Extract Function / Extract Class | Keep related behavior cohesive; check capture, returns, errors, resource ownership and async context. |
| Trivial or misleading abstraction | Inline Function / Inline Class | Preserve dispatch, receiver binding, evaluation count, identity and public contracts. |
| Excessive delegation | Remove Middle Man | Retain validation, instrumentation, access controls, lazy loading and genuine isolation boundaries. |
| Deep branching | Decompose Conditional / Guard Clauses | Preserve predicate semantics, short circuiting, cleanup, default cases and error precedence. |
| Repeated policy | Extract shared rule | Confirm shared meaning and reasons to change; account for rounding, errors and trust boundaries. |
| False shared abstraction | Split or inline it | Preserve each variant; remove flags only after accounting for supported behaviors. |
| Related parameters | Introduce Parameter Object | Preserve external signatures, defaults, identity and mutability as needed; do not introduce builders automatically. |
| Misplaced responsibility | Move Function / Move Field | Check visibility, cycles, dependency direction, initialization and exports. |
| Distinct processing stages | Split Phase | Preserve stage ordering, lazy behavior, failure semantics and transaction boundaries. |
| Query mixes reads and writes | Separate Query from Modifier | Preserve when and how often mutations occur; account for retries and atomicity. |
| Redundant temporary | Inline Variable / Replace Temp with Query | Recompute only if repeated evaluation is equivalent, including cost and effects. |
| Branches represent distinct operations | Remove Flag Argument | Preserve supported callers, defaults and branch semantics; do not force extra APIs for a simple option. |
| Shared mutable state | Encapsulate Variable / Collection | Preserve aliases, identity, ordering and synchronization; avoid accidental copies or contract changes. |
| Repeated dispatch | Table, explicit dispatch, or polymorphism | Maintain unknown cases, error behavior and precedence; use the simplest real variant model. |
| Oversized module | Extract cohesive responsibility | Preserve state ownership, invariants and transactional boundaries; size alone is not a reason. |
| Speculative layer | Collapse unnecessary layers | Establish that no useful seam, security control, external consumer or compatibility obligation is lost. |
| Suspected dead code | Remove Dead Code | Check build variants, plugin registries, reflection, public APIs, configuration and dynamic loading. Retain if non-use remains uncertain. |
| Complex iteration | Replace Loop with Pipeline or inverse | Preserve laziness, order, early exit, exceptions, mutation, memory and numeric semantics. |
| Unclear invariant | Name or document the invariant; Introduce Assertion if safe | An assertion that changes reachable runtime behavior is not a pure refactor. Preserve production behavior and failure contracts. |

## Avoid false simplification

- Keep separate validation at separate trust boundaries even if it looks duplicated. Do not replace precise failures with a catch-all fallback just to shorten code.
- A single-use helper or interface can be useful for cohesion, testing or isolation; justify it by current value, not by a hypothetical second implementation.
- Keep domain types, useful abstractions, distinct responsibilities and non-obvious explanations. Remove comments that are redundant or stale, not rationale or invariants. Do not chase arbitrary function length or zero-duplication targets.
- Check the preconditions of each transformation rather than copying a cookbook example. For example, a guard that negates a positive predicate must preserve its behavior for unordered values such as NaN; replacing `!(x > 0)` with `x <= 0` does not do so in JavaScript.
- Changing error aggregation to fail-fast, introducing new validation, altering defaults or replacing a public return type changes behavior. Treat it as separately authorized functional work.
