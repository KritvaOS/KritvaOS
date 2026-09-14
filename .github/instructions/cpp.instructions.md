---
applyTo: "**/*.{cpp,cc,cxx,h,hpp,hxx}"
---

# Kritva C++ Instructions

## 1. Scope

These instructions apply to C++ source and header files in Kritva.

Follow the repository-wide rules in:

- `AGENTS.md`
- `.github/copilot-instructions.md`

Follow more specific component instructions when present, including:

- `.github/instructions/core.instructions.md`
- future SoC / RTL / robotics / simulation instructions

---

## 2. Language Standard

Kritva C++ currently targets:

```text
C++20
```

Use the standard defined by the repository toolchain.

Do not introduce compiler-specific language extensions unless explicitly
justified.

Prefer standard C++ facilities over platform-specific alternatives when
portability is important.

---

## 3. Code Quality

Prefer code that is:

- clear
- explicit
- readable
- strongly typed
- testable
- maintainable
- predictable

Do not optimize for minimum line count.

Do not create abstractions merely to demonstrate abstraction.

Prefer simple designs with explicit ownership and clear interfaces.

---

## 4. Ownership and Lifetime

Use RAII.

Prefer:

```cpp
std::unique_ptr
```

for exclusive dynamic ownership.

Use:

```cpp
std::shared_ptr
```

only when shared ownership is semantically required.

Prefer references or pointers with explicit non-owning semantics for
non-owning access.

Avoid owning raw pointers.

Avoid unnecessary heap allocation.

---

## 5. Const Correctness

Use `const` whenever an object or operation does not modify state.

Prefer:

```cpp
const T&
```

for large non-owning read-only parameters where appropriate.

Prefer value parameters for small types where ownership/copying is clear.

Use `constexpr` and `consteval` where they improve correctness or express
compile-time intent.

---

## 6. Types

Prefer strong domain types over primitive values when semantics matter.

For example:

```cpp
Timestamp
Duration
Status
Health
CapabilityId
Version
ErrorCode
```

are preferable to ambiguous:

```cpp
int
uint64_t
std::string
```

when the value represents a distinct domain concept.

Avoid "stringly typed" interfaces.

---

## 7. Interfaces

Design interfaces around behavior and contracts rather than implementation.

Prefer small interfaces with:

- clear ownership
- explicit inputs
- explicit outputs
- explicit failure behavior
- stable semantics

Avoid large "god" interfaces.

Do not expose internal data structures unnecessarily.

---

## 8. Error Handling

Use the error model appropriate to the owning component.

For Kritva Core operational failures, prefer:

```cpp
Result<T>
```

where the architecture defines it.

Do not silently ignore failures.

Do not catch exceptions merely to suppress them.

If exceptions are used, define the exception boundary and ownership clearly.

Do not use exceptions for normal hard-real-time control flow.

---

## 9. Containers

Choose containers based on semantics and execution requirements.

Consider:

- allocation behavior
- iterator/reference stability
- lookup complexity
- memory footprint
- deterministic behavior

For real-time paths, avoid containers or operations whose allocation or
execution characteristics are uncontrolled unless explicitly justified.

---

## 10. Real-Time C++

When code executes in a real-time path, explicitly consider:

- dynamic allocation
- locks
- blocking calls
- system calls
- I/O
- logging
- exceptions
- unbounded loops
- container growth
- priority inversion
- cache behavior
- execution-time variability

Do not assume ordinary application-safe C++ is automatically real-time safe.

Document real-time constraints where relevant.

---

## 11. Concurrency

Do not introduce concurrency without defining the concurrency model.

Before adding:

- mutexes
- atomics
- condition variables
- worker threads
- lock-free structures

identify:

- ownership
- synchronization
- memory ordering
- lifetime
- shutdown behavior
- failure behavior

Prefer the simplest concurrency model that satisfies the requirement.

Avoid hidden background threads in foundational libraries unless explicitly
required.

---

## 12. Atomics

Use atomics only when their synchronization semantics are understood.

Specify memory ordering deliberately when the default is not sufficient.

Do not replace mutexes with atomics merely for perceived performance.

For real-time systems, consider bounded execution and contention behavior.

---

## 13. Initialization

Prefer deterministic initialization.

Avoid static initialization order dependencies.

Prefer function-local initialization or explicit initialization when
cross-module initialization order matters.

Avoid global mutable state.

---

## 14. Headers

Headers should be self-contained where practical.

A header should compile correctly when included independently.

Include what you use.

Avoid relying on transitive includes.

Use forward declarations where they genuinely reduce coupling and are safe.

Do not overuse forward declarations when they reduce readability.

---

## 15. Include Discipline

Keep include dependencies minimal.

Prefer:

```cpp
#include <...>
```

for standard library headers.

Do not include large umbrella headers when a smaller header is sufficient.

Follow `.clang-format` and repository include ordering.

---

## 16. Namespaces

Use the appropriate Kritva namespace hierarchy.

Prefer:

```cpp
namespace kritva
{
namespace core
{
...
}
}
```

or the repository's established equivalent.

Do not introduce global symbols.

Avoid namespace pollution.

Do not add `using namespace ...;` to headers.

---

## 17. Classes and Structs

Use `struct` when the type is primarily a simple data aggregate with
appropriate public semantics.

Use `class` when invariants, encapsulation, or behavior require controlled
access.

Keep classes focused.

Avoid classes that simultaneously own resources, perform I/O, manage
threads, implement business logic, and expose unrelated APIs.

---

## 18. Constructors and Invariants

Constructors should establish valid object invariants whenever practical.

Avoid objects that can exist in partially initialized states unless the
lifecycle architecture explicitly requires such a state.

Use explicit constructors for conversions that should not occur implicitly.

---

## 19. Enums

Prefer scoped enumerations:

```cpp
enum class
```

over unscoped enums.

Use explicit values when the numerical representation is part of the API or
serialization contract.

Do not assume enum numeric values are stable unless explicitly defined.

---

## 20. Serialization

Do not expose C++ object memory layout as a wire protocol.

Define explicit serialization formats when data crosses:

- process boundaries
- machine boundaries
- persistent storage
- network boundaries
- hardware interfaces

Serialization should preserve versioning and compatibility requirements.

---

## 21. Logging

Do not add uncontrolled logging to high-frequency real-time paths.

Logging should not accidentally become:

- blocking
- allocation-heavy
- unbounded
- network-dependent

Use the repository's logging architecture when available.

Do not print secrets or sensitive information.

---

## 22. Assertions

Use assertions to detect programmer assumptions and invariants.

Do not use assertions as the only mechanism for handling expected runtime
failures.

For externally controlled or physical-system inputs, validate explicitly.

---

## 23. Undefined Behavior

Avoid undefined behavior.

Be especially careful with:

- object lifetime
- pointer arithmetic
- alignment
- aliasing
- integer overflow
- signed/unsigned conversions
- uninitialized memory
- dangling references
- data races

Do not suppress compiler warnings merely to hide potential correctness
issues.

---

## 24. Compiler Warnings

Treat compiler warnings seriously.

Do not add broad warning suppressions to make code compile cleanly.

If a warning must be suppressed, keep the suppression narrow and document
why it is safe.

---

## 25. Static Analysis

Use clang-tidy and other repository-defined analysis tools when applicable.

Do not mechanically apply every automated suggestion.

Evaluate whether a suggestion preserves:

- architecture
- readability
- performance
- real-time behavior
- API stability

---

## 26. Formatting

Use the repository's `.clang-format`.

Do not manually introduce a separate formatting style.

Avoid formatting unrelated files as part of a feature change.

---

## 27. Testing

New C++ functionality should include appropriate tests.

Test:

- normal behavior
- boundary cases
- invalid input
- error behavior
- lifecycle behavior
- concurrency behavior where applicable
- deterministic behavior where required

Prefer tests that validate public behavior rather than implementation
details.

---

## 28. Testability

Design components so that behavior can be tested without requiring physical
hardware when practical.

Prefer dependency injection or interfaces where they provide meaningful
test isolation.

Do not over-engineer test seams.

For hardware-dependent code, provide simulation or mock boundaries where
appropriate.

---

## 29. API Compatibility

Treat public headers as contracts.

Before changing an API:

1. Find existing callers.
2. Check tests.
3. Check documentation.
4. Consider ABI/API implications.
5. Determine whether the change is breaking.

Prefer additive evolution during early development.

Document intentional breaking changes.

---

## 30. Performance

Do not optimize without evidence.

Prefer:

```text
Correctness
    ↓
Clarity
    ↓
Measurement
    ↓
Optimization
    ↓
Verification
```

When performance matters, identify the relevant metric:

- latency
- throughput
- memory
- CPU utilization
- jitter
- startup time
- power

Do not assume a micro-optimization improves system behavior.

---

## 31. Robotics Context

C++ code in Kritva may eventually execute across:

- Linux
- PREEMPT_RT
- RTOS
- MCU
- ARM
- RISC-V
- x86
- custom SoCs
- simulation
- FPGA-assisted environments

Avoid unnecessary platform assumptions.

Keep portable code portable.

Put platform-specific implementation behind explicit abstraction boundaries.

---

## 32. Hardware Safety

C++ code that can influence physical actuators requires additional care.

Never bypass:

- safety limits
- state checks
- interlocks
- emergency behavior
- fault handling

to simplify implementation or testing.

Clearly identify safety-sensitive code.

---

## 33. Dependency Discipline

Before adding a C++ dependency, evaluate:

- necessity
- licensing
- portability
- build cost
- runtime cost
- security
- maintenance
- real-time impact

Prefer the standard library when adequate.

Do not add a dependency for convenience when a small local implementation
is clearer and architecturally appropriate.

---

## 34. Comments

Comments should explain:

- why
- invariants
- constraints
- hardware assumptions
- non-obvious decisions

Avoid comments that merely restate the code.

If behavior is important enough to document, prefer documenting the contract
rather than narrating implementation syntax.

---

## 35. Change Discipline

Before editing:

1. Inspect the existing code.
2. Identify ownership.
3. Inspect related APIs.
4. Inspect tests.
5. Check architecture.
6. Check component-specific instructions.

After editing:

1. Review the diff.
2. Build the affected target.
3. Run tests.
4. Run formatting/static checks.
5. Run `make ci` when appropriate.
6. Check for unrelated modifications.

---

## 36. C++ Review Checklist

Before completing a C++ change:

- [ ] C++20 is respected.
- [ ] Ownership and lifetime are clear.
- [ ] Public APIs are intentional.
- [ ] Error behavior is explicit.
- [ ] Threading model is understood.
- [ ] Real-time implications are considered.
- [ ] Hardware assumptions are isolated.
- [ ] No unnecessary dependency was introduced.
- [ ] Headers are self-contained.
- [ ] Tests cover meaningful behavior.
- [ ] Formatting is compliant.
- [ ] Static analysis is considered.
- [ ] No unrelated refactoring was introduced.
- [ ] CI passes.

---

## 37. Final C++ Principle

> Write C++ that a robotics engineer can reason about at 3 AM when the robot
> is not behaving as expected.

Prefer explicitness, determinism, strong interfaces, and maintainability
over cleverness.
