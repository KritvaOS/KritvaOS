---
applyTo: "soc/**"
---

# Kritva SoC Engineering Instructions

## Purpose

These instructions apply to work under `soc/**`, including Kritva Nexus and Kritva Edge platform architecture, RTL, firmware-facing interfaces, FPGA prototypes, simulation models, integration, and SoC documentation.

The goal is to keep SoC work aligned with the Kritva robotic computing architecture rather than allowing an individual chip implementation to redefine the platform.

## 1. Architectural Context

Kritva uses a common robotic computing architecture with platform variants:

- **Kritva Nexus SoC** — system-level compute platform.
- **Kritva Edge SoC** — distributed real-time compute platform.
- **Subnode** — localized controller or I/O aggregation.
- **Endpoint** — sensor, actuator, encoder, motor, or other physical interface.

Do not treat Nexus and Edge as two unrelated products. Prefer reusable interfaces, architectural contracts, and IP across the platform family.

The logical hierarchy is:

    Application
      ↓
    Skill / Mind / Motion / Sense
      ↓
    Kritva Core
      ↓
    Hardware Abstraction
      ↓
    Nexus / Edge
      ↓
    Subnode
      ↓
    Endpoint
      ↓
    Physical World

## 2. Master / Node Terminology

Use **Nexus** and **Edge** as the product/platform terminology.

"Master" and "Node" may be used only as descriptive roles where technically appropriate, for example EtherCAT master or distributed node. Do not turn these role names into product names.

## 3. SoC Design Principles

Every SoC proposal should explicitly consider:

1. Functional requirements.
2. Real-time requirements.
3. Throughput and latency.
4. Memory and bandwidth.
5. Power and thermal constraints.
6. Safety requirements.
7. Security requirements.
8. Debug and observability.
9. Verification strategy.
10. Software programmability.
11. FPGA/prototype feasibility.
12. Silicon migration path.

Do not introduce hardware blocks merely because they are common in general-purpose SoCs. Each block should have a clear robotic-system purpose.

## 4. Hardware / Software Boundary

Define interfaces before implementations.

For every hardware-visible feature, identify:

- Register/interface contract.
- Clock and reset behavior.
- Interrupt/event behavior.
- DMA behavior where applicable.
- Error and fault behavior.
- Security/privilege implications.
- Firmware/software ownership.
- Verification requirements.

Avoid hiding architectural decisions inside RTL.

## 5. Nexus Guidance

Nexus is intended for workloads such as:

- High-level robot computation.
- AI/vision acceleration.
- World-model and planning workloads.
- Global robot coordination.
- System management.
- Storage and networking.
- Security services.
- EtherCAT master or equivalent real-time network coordination.

Nexus should expose stable interfaces to KritvaOS rather than tightly coupling software to implementation-specific registers.

## 6. Edge Guidance

Edge is intended for workloads such as:

- Deterministic real-time control.
- Motor/joint control.
- Encoder processing.
- PWM generation.
- ADC and sensor interfaces.
- Local safety functions.
- EtherCAT distributed control.
- Local diagnostics.

Prefer deterministic behavior and bounded latency over unnecessary architectural complexity.

## 7. Register and Interface Design

For every register/interface:

- Define reset value.
- Define access type.
- Define field ownership.
- Define reserved bits.
- Define side effects.
- Define timing assumptions.
- Define error behavior.
- Define versioning expectations.

Avoid magic addresses and undocumented bit fields.

Use explicit widths and types. Do not assume software and RTL integer widths are interchangeable.

## 8. Clock / Reset / CDC

Clock-domain crossings must be explicit.

For every CDC path, identify:

- Source clock.
- Destination clock.
- Synchronization mechanism.
- Data consistency mechanism.
- Reset interaction.
- Verification coverage.

Do not use ad-hoc multi-bit synchronization.

Reset architecture must define:

- Power-on reset.
- Warm/software reset where applicable.
- Per-domain reset.
- Peripheral reset.
- Reset ordering.
- Post-reset state.

## 9. Memory and DMA

For memory-mapped and DMA-capable blocks document:

- Address width.
- Data width.
- Burst behavior.
- Alignment.
- Ordering.
- Coherency.
- Cache interaction.
- Protection.
- Error handling.

DMA engines must define ownership and lifetime of descriptors and buffers.

## 10. Real-Time Requirements

Real-time behavior must be specified quantitatively where possible:

- Worst-case latency.
- Jitter.
- Interrupt latency.
- Service time.
- Scheduling period.
- Deadline.
- Buffer depth.
- Back-pressure behavior.

Do not describe a block as "real-time" without defining the relevant timing contract.

## 11. Safety

Safety-critical paths require explicit failure behavior.

For relevant blocks define:

- Fault detection.
- Fault containment.
- Safe state.
- Watchdog behavior.
- Timeout behavior.
- Redundancy where required.
- Diagnostic coverage where known.

Do not silently assume that a software retry is a sufficient safety mechanism.

## 12. Security

SoC security should consider:

- Secure boot.
- Hardware root of trust.
- Key storage.
- Debug authentication.
- Memory protection.
- Peripheral isolation.
- Firmware authenticity.
- Secure update.
- Fault/attack logging.

Do not invent cryptographic mechanisms when a standard hardware security primitive is appropriate.

## 13. FPGA and Silicon Strategy

Design interfaces so that the same architectural contract can be exercised through:

    RTL → simulation → Renode/model → FPGA → HIL → silicon

Avoid FPGA-only shortcuts that cannot reasonably migrate to silicon unless they are explicitly marked as prototype-only.

## 14. Verification

Every significant SoC feature should have:

- Requirement.
- Interface specification.
- RTL implementation.
- Directed tests.
- Assertions where appropriate.
- Functional coverage.
- Integration test.
- Software-visible validation.

Prefer verification plans that can be traced back to requirements.

## 15. Documentation

For significant architectural changes update the appropriate documentation under:

- `ARCHITECTURE.md`
- `docs/architecture/`
- `docs/requirements/`
- `docs/verification/`
- `docs/adr/`

Do not allow implementation comments to become the only source of architectural truth.

## 16. Dependencies

Keep SoC dependencies explicit.

Do not introduce:

- Cloud dependencies.
- Vendor SDK dependencies.
- Robotics middleware dependencies.
- Host-only utilities

into a hardware abstraction without documenting why they are required.

Vendor-specific implementations should remain behind well-defined interfaces.

## 17. Naming

Use canonical Kritva terminology:

- Kritva Nexus SoC
- Kritva Edge SoC
- Subnode
- Endpoint
- KritvaOS

Use consistent naming for clocks, resets, buses, interrupts, registers, and interfaces.

Avoid abbreviations unless they are standard or documented.

## 18. AI-Agent Change Discipline

Before modifying SoC code:

1. Read the relevant architecture and requirement documents.
2. Identify the affected interface.
3. Check existing RTL/software contracts.
4. Inspect related tests.
5. State assumptions explicitly.
6. Make the smallest coherent change.
7. Add or update verification.
8. Update documentation when the contract changes.

Never fabricate missing register specifications, bus behavior, timing requirements, or silicon constraints.

## 19. Review Checklist

Before accepting a SoC change, verify:

- [ ] Requirement is identifiable.
- [ ] Architecture boundary is preserved.
- [ ] Nexus/Edge role is clear.
- [ ] HW/SW interface is documented.
- [ ] Register behavior is specified.
- [ ] Clock/reset behavior is safe.
- [ ] CDC is addressed.
- [ ] Error/fault behavior is defined.
- [ ] Real-time requirements are considered.
- [ ] Security implications are considered.
- [ ] Verification exists or is explicitly planned.
- [ ] FPGA/silicon portability is considered.
- [ ] Documentation is updated where required.
