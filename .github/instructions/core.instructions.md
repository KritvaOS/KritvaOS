---
applyTo: "core/**"
---

# Kritva Core Instructions

## 1. Scope

These instructions apply to files under `core/**`.

Kritva Core is the platform foundation of the KritvaOS software platform.
Core must remain platform-independent and reusable across robotic systems,
compute platforms, simulation environments, and future hardware
implementations.

Follow the repository-wide rules in:

- `AGENTS.md`
- `.github/copilot-instructions.md`

The canonical system architecture is `ARCHITECTURE.md`.

---

## 2. Core Architectural Boundary

Kritva Core provides common platform primitives and foundational runtime
interfaces.

Core Foundation includes:

- Identity
- Lifecycle
- Status
- Health
- Statistics
- Error / Fault
- Result
- Events
- Capability
- Configuration
- Version
- Timestamp
- Duration
- Metadata

Core is not the place for robot-specific behavior.

Do not introduce the following into Core Foundation unless explicitly
approved by the architecture:

- robot-specific algorithms
- perception algorithms
- motion algorithms
- ROS2 dependencies
- DDS dependencies
- EtherCAT implementation
- vendor-specific drivers
- cloud services
- database dependencies
- customer-specific application logic
- hardware-specific register access
- AI model implementations

---

## 3. Core Design Principles

Prefer:

- platform independence
- small stable interfaces
- strong types
- deterministic behavior where required
- explicit ownership
- clear error semantics
- testability
- minimal dependencies
- API stability
- portability

Avoid unnecessary abstractions.

Do not introduce a framework when a small, well-defined C++20 interface is
sufficient.

---

## 4. C++ Standard

Kritva Core targets C++20.

Use the repository's configured:

- C++20 compiler
- `.clang-format`
- `.editorconfig`
- CMake configuration
- clang-tidy configuration when available

Do not introduce compiler-specific extensions unless explicitly justified.

---

## 5. Core API Design

Core APIs should be:

- explicit
- strongly typed
- composable
- predictable
- easy to test
- independent of transport technology
- independent of operating system implementation where practical

Prefer domain types over primitive values when semantics matter.

For example, prefer:

```cpp
LifecycleState
Status
Health
Error
Result<T>
CapabilityId
Version
Timestamp
Duration
```

over unstructured strings or loosely typed maps.

Do not expose implementation details through public interfaces.

---

## 6. Ownership and Lifetime

Use RAII and clear ownership semantics.

Prefer:

- value semantics where appropriate
- `std::unique_ptr` for exclusive ownership
- `std::shared_ptr` only when shared ownership is actually required
- references or references-to-const for non-owning access
- explicit lifetime contracts

Avoid raw owning pointers.

Do not introduce reference-counting merely as a convenience.

---

## 7. Error Handling

Operational failures should be represented explicitly.

Prefer:

```cpp
Result<T>
```

for operations where failure is an expected part of the API contract.

Errors should provide enough context to support diagnosis.

Where applicable, preserve:

- error code
- severity
- source
- timestamp
- context

Do not silently swallow errors.

Do not use exceptions merely to represent normal operational failures unless
the relevant component architecture explicitly permits them.

---

## 8. Lifecycle

Use the defined lifecycle model consistently:

```text
UNKNOWN
INITIALIZING
READY
RUNNING
STOPPING
STOPPED
FAULT
RECOVERING
```

Do not invent alternative lifecycle states for a Core object without
architectural justification.

Lifecycle and health are separate concepts.

For example:

```text
Status = RUNNING
Health = DEGRADED
```

is valid and should not be collapsed into one state.

---

## 9. Status and Health

Status describes operational state.

Health describes condition or quality.

Do not use health as a replacement for lifecycle state.

Do not overload status codes with hardware-specific diagnostic meanings.

Keep generic Core semantics separate from component-specific diagnostics.

---

## 10. Statistics

Statistics should distinguish counters from gauges.

Examples of counters:

- sample_count
- error_count
- retry_count
- drop_count

Examples of gauges:

- queue depth
- utilization
- temperature

Avoid ambiguous fields whose units or update semantics are unclear.

If a metric has physical units, document them.

---

## 11. Events

Events should use a consistent event envelope.

Where applicable include:

- event ID
- source ID
- event type
- timestamp
- severity
- correlation ID
- event-specific payload

Do not couple the Core event model directly to a specific transport such as
DDS, ROS2, EtherCAT, or a proprietary network protocol.

---

## 12. Capability

Capabilities should be represented explicitly.

A capability should be identifiable and, where required, versioned.

Do not use arbitrary string flags as a substitute for a capability model when
the capability affects interoperability or behavior.

---

## 13. Configuration

Core configuration should provide typed, validated parameters.

Consider:

- parameter identity
- type
- default
- constraints
- version
- validation
- metadata

Do not make YAML, JSON, databases, or other persistence mechanisms part of
the Core API unless explicitly architected.

Configuration parsing belongs at an appropriate higher layer.

---

## 14. Time

Use explicit `Timestamp` and `Duration` abstractions.

Distinguish monotonic timing from wall-clock time.

Do not silently mix clock domains.

Core may define time primitives but should not absorb a complete PTP or
distributed clock synchronization implementation unless explicitly
architected.

---

## 15. Real-Time Considerations

Core primitives may be used by real-time components.

Therefore, avoid introducing uncontrolled behavior into foundational APIs.

Be cautious with:

- dynamic allocation
- locks
- blocking operations
- filesystem access
- network access
- logging
- exceptions
- unbounded containers
- unpredictable initialization

Do not claim an API is hard-real-time safe without evidence.

Where an API is intended for real-time use, document its real-time
assumptions and constraints.

---

## 16. Thread Safety

Do not add synchronization automatically.

First establish the ownership and concurrency model.

Document whether an API is:

- single-threaded
- thread-compatible
- thread-safe
- externally synchronized

Avoid hidden global state.

Avoid global mutable state unless explicitly justified.

---

## 17. Dependencies

Kritva Core should have a minimal dependency footprint.

Before adding a dependency, evaluate:

- necessity
- portability
- licensing
- security
- build impact
- runtime impact
- real-time implications
- long-term maintenance

Prefer the C++ standard library when it provides an adequate solution.

Do not add ROS2, DDS, EtherCAT, vendor SDKs, cloud SDKs, or database
libraries to Core merely because another Kritva component uses them.

---

## 18. Hardware Independence

Core must not depend on a specific:

- CPU
- SoC
- MCU
- GPU
- accelerator
- operating system
- board
- sensor
- motor
- bus
- vendor

Hardware-specific functionality belongs behind appropriate hardware
abstraction boundaries.

---

## 19. Testing

Every meaningful Core API should have tests.

At minimum consider:

- normal behavior
- boundary conditions
- invalid input
- error behavior
- state transitions
- recovery behavior
- API contracts

Core should emphasize unit and contract testing.

Preferred validation:

```text
API
 ↓
Unit Test
 ↓
Contract Test
 ↓
Integration Test
```

Do not consider a Core feature complete merely because it compiles.

---

## 20. Public API Stability

Treat headers under:

```text
core/include/
```

as potentially public interfaces.

Before changing a public API, check:

- existing users
- examples
- tests
- documentation
- compatibility implications

Prefer additive changes over unnecessary breaking changes during early
development.

If a breaking change is necessary, identify it explicitly.

---

## 21. Implementation Discipline

Before editing:

1. Inspect existing Core APIs.
2. Check architecture requirements.
3. Check related tests.
4. Check existing conventions.
5. Determine whether the change affects the public API.
6. Determine whether an ADR is required.

After editing:

1. Review the diff.
2. Build Core.
3. Run Core tests.
4. Run formatting checks.
5. Run static analysis when available.
6. Run `make ci` when appropriate.

Do not modify unrelated components unless required.

---

## 22. Core Should Not Become a "Everything" Layer

Do not solve architectural uncertainty by putting functionality into Core.

If functionality does not clearly belong in Core Foundation, stop and
identify the appropriate owner.

Use this decision direction:

Common platform primitive
    → Core

Sensor functionality
    → Sense

AI / reasoning
    → Mind

Motion / control
    → Motion

Reusable behavior
    → Skill

Simulation
    → Sim

Hardware-specific implementation
    → Hardware / drivers / Edge / Nexus

Customer-facing API
    → SDK

---

## 23. Documentation

When changing a Core public API, update the appropriate:

- API documentation
- requirements
- architecture documentation
- tests
- changelog

Do not duplicate the complete architecture inside Core source comments.

Use comments to explain implementation decisions, invariants, constraints,
or non-obvious behavior.

---

## 24. Review Checklist

Before considering a Core change complete, verify:

- [ ] Core boundary is preserved.
- [ ] API is clearly defined.
- [ ] Ownership is explicit.
- [ ] Error behavior is defined.
- [ ] Lifecycle/status/health semantics are correct.
- [ ] Real-time implications are considered.
- [ ] No unnecessary dependency was introduced.
- [ ] Hardware independence is preserved.
- [ ] Unit tests exist.
- [ ] Contract tests exist where appropriate.
- [ ] Documentation is updated.
- [ ] CI passes.
- [ ] No unrelated files were modified.

---

## 25. Core Engineering Principle

> Kritva Core should provide the smallest stable foundation required by the
> rest of Kritva.

Keep Core boring, predictable, portable, testable, and dependable.
