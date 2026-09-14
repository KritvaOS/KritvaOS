---
applyTo: "{sim/**,tests/**,tools/**}"
---

# Kritva Simulation Engineering Instructions

## Purpose

These instructions apply to simulation, digital-twin, SIL/HIL, Renode, Verilator, simulation utilities, and simulation-facing tests under Kritva.

The objective is to make simulation a first-class engineering and verification environment that preserves the same architectural contracts used by FPGA, silicon, and physical robot deployments.

## 1. Simulation Architecture

Use the Kritva development path:

    Requirements
        ↓
    Architecture
        ↓
    API / Contract
        ↓
    Implementation
        ↓
    Simulation
        ↓
    Renode / FPGA
        ↓
    HIL
        ↓
    Silicon
        ↓
    Physical Robot

Simulation must validate architecture and interfaces, not become a separate implementation of the product.

## 2. Simulation Fidelity

Every simulation model should document its fidelity level.

Distinguish:

- Functional model.
- Timing-aware model.
- Cycle-accurate model.
- Instruction-level model.
- Hardware-accurate model.
- Robot/environment simulation.

Do not make timing or hardware-accuracy claims that the model does not support.

## 3. Contract Preservation

Simulation must use the same logical contracts as the target system wherever practical:

- APIs.
- Data types.
- Lifecycle.
- Status/health.
- Error behavior.
- Configuration.
- Timing semantics.
- Hardware interfaces.

Avoid creating simulation-only APIs that cannot be mapped to the real implementation.

## 4. Renode

Renode should be used where it provides value for:

- SoC/platform modeling.
- Firmware execution.
- Peripheral integration.
- Distributed-system validation.
- Automated regression.

Keep Renode platform descriptions and scripts version-controlled and documented.

Simulation assumptions must be explicit when a Renode model differs from future silicon.

## 5. RTL Simulation

For RTL simulation:

- Follow the repository SystemVerilog baseline.
- Use the defined simulator/toolchain versions.
- Keep testbench code separate from synthesizable RTL.
- Validate reset, protocol, error, and corner cases.
- Use assertions where appropriate.
- Preserve reproducibility of regressions.

Do not modify RTL merely to make a simulation pass without understanding the architectural reason.

## 6. Verilator and Fast Simulation

Fast simulation may be used for:

- Regression.
- Software/hardware integration.
- Interface testing.
- Performance-oriented development.

Document behavioral differences from event-driven RTL simulation when relevant.

## 7. Robot Simulation

Robot simulation should preserve:

- Sensor interfaces.
- Actuator interfaces.
- Coordinate frames.
- Units.
- Timestamps.
- Robot topology.
- Communication behavior.
- Fault behavior.

Do not hide robot-specific assumptions inside generic simulation infrastructure.

## 8. SIL / HIL

### SIL

Software-in-the-loop should validate software behavior against simulated hardware/environment interfaces.

### HIL

Hardware-in-the-loop should validate software and hardware interaction using real hardware where appropriate.

For HIL tests document:

- Hardware configuration.
- Firmware version.
- Toolchain version.
- Test environment.
- Expected timing.
- Pass/fail criteria.

## 9. Determinism and Reproducibility

A regression should be reproducible.

Where applicable record:

- Random seed.
- Model version.
- Configuration version.
- Tool versions.
- Firmware/software version.
- Hardware revision.
- Test case identifier.

Avoid tests that depend on uncontrolled wall-clock timing or external services.

## 10. Time

Simulation time must be distinguished from host wall-clock time.

Tests should not accidentally depend on host execution speed.

For real-time behavior, explicitly identify:

- Simulated time.
- Logical time.
- Target time.
- Host execution time.

## 11. Fault Injection

Simulation is an important place to test failures.

Where applicable test:

- Sensor loss.
- Stale data.
- Packet loss.
- Communication timeout.
- CRC/protocol errors.
- Memory errors.
- Invalid commands.
- Actuator faults.
- Watchdog expiration.
- Power/reset events.

Fault injection must have explicit expected behavior.

## 12. Distributed Simulation

When simulating Nexus/Edge/Subnode/Endpoint systems, preserve distributed boundaries.

Consider:

- Latency.
- Jitter.
- Packet loss.
- Reordering where applicable.
- Clock offsets.
- Restart/reconnect.
- Partial subsystem failure.

Do not assume an ideal zero-latency network unless that is the specific test objective.

## 13. Simulation Configuration

Keep configuration explicit and version-controlled.

Do not hard-code:

- Robot topology.
- Network addresses.
- Sensor mappings.
- Hardware revisions.
- Timing parameters

inside reusable simulation code unless required by the model.

## 14. Test Organization

Prefer clear test levels:

    unit/
    contract/
    integration/
    renode/
    rtl/
    sil/
    hil/
    system/

Use the actual repository structure where these directories exist; do not create duplicate hierarchies unnecessarily.

## 15. Regression Quality

A simulation regression should report:

- Test identifier.
- Configuration.
- Tool/model versions.
- Result.
- Failure reason.
- Relevant logs/artifacts.

Do not consider "process exited successfully" sufficient evidence of correctness.

## 16. Performance

Simulation performance matters for large regressions.

Prefer:

- Deterministic test inputs.
- Reusable fixtures.
- Efficient models.
- Parallelizable tests where safe.
- Artifact retention only when useful.

Do not trade away fidelity silently for speed.

## 17. AI-Agent Change Discipline

Before modifying simulation infrastructure:

1. Identify what real system behavior is being modeled.
2. Locate the authoritative interface.
3. Identify fidelity assumptions.
4. Inspect existing tests.
5. Make the smallest coherent change.
6. Add/update regression coverage.
7. Document changed assumptions.

Never invent hardware timing, register semantics, robot dynamics, or simulator capabilities.

## 18. Review Checklist

- [ ] Simulation purpose is clear.
- [ ] Fidelity level is documented.
- [ ] Real API/contract is preserved.
- [ ] Tool versions follow the Kritva toolchain.
- [ ] Time semantics are explicit.
- [ ] Determinism/reproducibility is addressed.
- [ ] Fault behavior is tested where relevant.
- [ ] Distributed boundaries are preserved.
- [ ] SIL/HIL assumptions are documented.
- [ ] Regression coverage exists.
- [ ] Simulation-only behavior is clearly identified.
- [ ] Documentation is updated for changed contracts.
