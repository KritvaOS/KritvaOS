---
applyTo: "**/*.{sv,v,svh,vh,vhdl}"
---

# Kritva RTL Engineering Instructions

## Purpose

These instructions apply to RTL and hardware-description-language work in Kritva, including Nexus, Edge, Subnode, Endpoint, FPGA prototypes, and synthesizable hardware IP.

The objective is deterministic, synthesizable, portable, verifiable RTL that implements an already-defined architecture.

## 1. Architecture Before RTL

RTL must implement a documented requirement or interface.

Before changing RTL:

1. Locate the relevant architecture.
2. Identify the requirement.
3. Identify the interface contract.
4. Inspect existing modules and conventions.
5. Check associated verification.
6. Make the smallest coherent change.

Do not use RTL implementation as a substitute for architecture definition.

## 2. Supported HDL Direction

The planned primary RTL language is:

- SystemVerilog, IEEE 1800-2017.

Follow the repository toolchain definition for the exact supported simulator, linter, synthesis, and FPGA versions.

Do not silently introduce a newer language feature that is unsupported by the baseline toolchain.

## 3. Synthesizability

Unless a file is explicitly simulation-only, RTL should be synthesizable.

Clearly separate:

- Synthesizable RTL.
- Testbench code.
- Assertions.
- Simulation models.
- FPGA-only logic.
- Verification-only utilities.

Do not hide non-synthesizable constructs inside production RTL.

## 4. Sequential Logic

Prefer explicit sequential structures.

For clocked logic:

- Use the intended clock.
- Define reset behavior.
- Define reset polarity and synchronization.
- Avoid unintended latches.
- Make enable behavior explicit.
- Avoid ambiguous multiple drivers.

Every state-holding element must have a clear reset or documented initialization strategy.

## 5. Combinational Logic

Combinational logic should have complete assignments.

Avoid inferred latches unless deliberately required and documented.

For next-state logic, use an explicit default path and define behavior for unexpected encodings.

## 6. FSM Design

For FSMs:

- Define state type explicitly.
- Define reset state.
- Define legal transitions.
- Define behavior for illegal/unknown states.
- Keep state transition and output logic understandable.
- Use symbolic state names rather than unexplained numeric literals.

Do not duplicate state encodings in multiple places without a documented reason.

If an FSM is safety- or control-critical, add appropriate assertions and illegal-state recovery where required.

## 7. Width Discipline

Width mismatches are a major RTL risk.

Always consider:

- Signal width.
- Signedness.
- Extension/truncation.
- Constant width.
- Parameter width.
- Arithmetic overflow.

Prefer explicitly sized constants where width matters.

Do not rely on implicit truncation or signed conversion.

## 8. Parameters

Parameters should describe genuine architectural variation.

Avoid parameterizing code simply to make it appear reusable.

For every important parameter document:

- Meaning.
- Legal range.
- Default.
- Impact on interfaces.
- Verification coverage.

## 9. Clock-Domain Crossing

CDC must be deliberate.

For single-bit control signals use an appropriate synchronization mechanism.

For multi-bit data use an appropriate protocol such as:

- Handshake.
- Async FIFO.
- Gray-coded pointer scheme.
- Stable-data protocol.

Never synchronize individual bits of a changing multi-bit bus independently unless the protocol explicitly guarantees correctness.

Document every intentional CDC boundary.

## 10. Reset

Define:

- Reset source.
- Reset polarity.
- Synchronous/asynchronous behavior.
- Reset domain.
- Release behavior.
- Dependencies on other domains.

Avoid asynchronous reset release without appropriate synchronization where required.

## 11. Interfaces and Protocols

For AXI, APB, AHB, TileLink, Wishbone, custom interfaces, EtherCAT-related logic, or other protocols:

- Follow the protocol specification.
- Preserve handshake semantics.
- Define back-pressure behavior.
- Define timeout/error behavior.
- Avoid assuming zero-latency behavior.
- Document deviations explicitly.

Do not implement a "simplified" protocol interface that violates interoperability expectations.

## 12. Memory-Mapped Registers

Register blocks must define:

- Address.
- Offset.
- Width.
- Reset value.
- Access permissions.
- Side effects.
- Reserved fields.
- Write/read behavior.
- Interrupt interaction.

Register definitions should have a single source of truth where practical.

Avoid duplicated magic constants between RTL and firmware.

## 13. Arithmetic and Control

For control paths, explicitly consider:

- Saturation.
- Overflow.
- Underflow.
- Fixed-point scaling.
- Signedness.
- Numerical precision.
- Deterministic execution.

Do not silently replace a fixed-point or bounded implementation with floating-point hardware assumptions.

## 14. Timing and Determinism

Critical robotic control paths should have predictable timing.

Consider:

- Combinational depth.
- Pipeline stages.
- Arbitration.
- FIFO depth.
- Worst-case latency.
- Back-pressure.
- Interrupt/event latency.

Do not make timing claims without measurement or analysis.

## 15. Safety-Critical RTL

Where applicable:

- Define safe-state behavior.
- Detect illegal states.
- Define timeout behavior.
- Add assertions.
- Avoid single-point uncontrolled failure.
- Make fault reporting observable.

Safety behavior must be derived from requirements, not invented during implementation.

## 16. Security-Critical RTL

For security-related blocks consider:

- Access control.
- Key isolation.
- Secure state transitions.
- Debug restrictions.
- Fault injection implications.
- Side-channel considerations where relevant.

Do not implement custom cryptography unless explicitly required and reviewed.

## 17. Verification

Every significant RTL change should have appropriate verification.

Prefer:

- Directed tests for exact requirements.
- Assertions for invariants.
- Random/constrained-random tests where useful.
- Functional coverage.
- Protocol checks.
- Regression tests.

A passing simulation is not by itself evidence of complete verification.

## 18. Lint and Static Checks

RTL should pass the repository-defined lint and quality checks.

Treat the following as issues requiring explicit resolution or justification:

- Width warnings.
- Signedness warnings.
- Latch inference.
- Multiple drivers.
- Unconnected signals.
- Undriven signals.
- Implicit nets.
- Clock/reset misuse.
- Unreachable states.
- Suspicious case statements.

Do not suppress warnings globally to make a build pass.

## 19. Simulation vs Synthesis

Do not assume simulation semantics equal hardware semantics.

Check:

- Initial values.
- X/Z behavior.
- Reset sequencing.
- Blocking/non-blocking assignment semantics.
- Timing constructs.
- `$display`/testbench-only constructs.
- Synthesis treatment of unsupported constructs.

Simulation-only behavior must be clearly isolated.

## 20. FPGA Portability

Where FPGA is used as a prototype for future silicon:

- Keep architecture independent of one vendor where practical.
- Isolate vendor primitives.
- Document vendor-specific blocks.
- Avoid unnecessary FPGA-specific behavior in common RTL.

Vendor-specific code should be easy to identify and replace.

## 21. Naming and Style

Use consistent names for:

- `clk`
- reset signals
- valid/ready interfaces
- request/response channels
- state variables
- counters
- interrupts
- register fields

Follow `.editorconfig`, `.clang-format` where applicable, and repository RTL style guidance.

Do not create a new naming convention inside a single module.

## 22. AI-Agent Discipline

An AI agent must not invent:

- Register maps.
- Bus semantics.
- Clock frequencies.
- Timing constraints.
- Reset requirements.
- Synthesis assumptions.
- Safety guarantees.
- Silicon characteristics.

When information is missing, preserve the existing contract or mark the assumption explicitly for human review.

## 23. Change Discipline

Prefer small, reviewable patches.

Do not combine unrelated:

- Refactoring.
- Interface changes.
- Formatting changes.
- Functional changes.

When an interface changes, update the corresponding:

- Specification.
- RTL.
- Firmware/software interface.
- Tests.
- Documentation.

## 24. RTL Review Checklist

- [ ] Requirement identified.
- [ ] Architecture boundary preserved.
- [ ] Synthesizability checked.
- [ ] Clock/reset behavior defined.
- [ ] CDC reviewed.
- [ ] Width and signedness reviewed.
- [ ] FSM behavior reviewed.
- [ ] Protocol compliance checked.
- [ ] Register behavior documented.
- [ ] Error/fault behavior defined.
- [ ] Timing/determinism considered.
- [ ] Safety/security implications considered.
- [ ] Assertions/tests added where appropriate.
- [ ] Lint warnings resolved or justified.
- [ ] FPGA portability considered.
- [ ] Documentation updated for contract changes.
