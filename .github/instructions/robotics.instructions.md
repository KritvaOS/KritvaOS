---
applyTo: "{sense/**,mind/**,motion/**,skill/**,robots/**,hardware/**,drivers/**,examples/**}"
---

# Kritva Robotics Engineering Instructions

## Purpose

These instructions apply to robotics-facing software and integration work across Kritva Sense, Mind, Motion, Skill, robot definitions, hardware interfaces, drivers, and robotics examples.

The objective is to preserve a clean boundary between:

- Intelligence.
- Behavior.
- Real-time control.
- Hardware abstraction.
- Distributed compute.
- Physical endpoints.

## 1. Kritva Robotics Architecture

Use the canonical stack:

    Application
      ↓
    Kritva SDK
      ↓
    Kritva Skill
      ↓
    Kritva Mind / Kritva Motion
      ↓
    Kritva Sense
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

Do not collapse all robotics functionality into a single runtime layer.

## 2. Module Responsibilities

### Kritva Sense

Responsible for:

- Sensor interfaces.
- Sensor abstraction.
- Sensor synchronization.
- Perception pipelines.
- Sensor health and diagnostics.
- Sensor metadata and calibration interfaces.

Sense should not become the owner of high-level planning.

### Kritva Mind

Responsible for:

- Reasoning.
- World models.
- Planning.
- Task-level decision making.
- AI inference interfaces.
- High-level robot intelligence.

Mind should not directly manipulate hardware registers or own hard real-time motor loops.

### Kritva Motion

Responsible for:

- Kinematics.
- Dynamics.
- Motion planning.
- Locomotion.
- Balance.
- Manipulation.
- Joint/motor control interfaces.
- Real-time control policies.

Motion must distinguish high-level planning from deterministic control loops.

### Kritva Skill

Responsible for:

- Reusable robot behaviors.
- Task/action composition.
- Skill lifecycle.
- Skill preconditions/postconditions.
- Coordination between Mind and Motion/Sense.

Skills should not duplicate low-level device drivers.

### Robots

`robots/**` should describe robot-specific topology, configuration, capabilities, deployment, and integration.

Do not duplicate generic robotics algorithms under individual robot folders.

## 3. Physical AI to Physical Action

Maintain a clear chain:

    Perception
        ↓
    World State
        ↓
    Decision / Planning
        ↓
    Skill
        ↓
    Motion Command
        ↓
    Real-Time Control
        ↓
    Edge
        ↓
    Endpoint
        ↓
    Physical Action

Every interface between these stages should have an explicit contract.

## 4. Real-Time Boundary

Separate:

- Non-real-time AI/planning.
- Soft real-time coordination.
- Hard/deterministic control.

Do not place unpredictable AI inference, dynamic memory allocation, blocking I/O, or network-dependent operations directly inside a hard real-time control loop unless explicitly justified and bounded.

## 5. Timing

Robotics interfaces should define where relevant:

- Timestamp.
- Sampling period.
- Deadline.
- Jitter expectation.
- Timeout.
- Queue behavior.
- Drop policy.
- Synchronization source.

Do not treat timestamps as optional metadata when temporal ordering affects correctness.

## 6. Sensor Data

Sensor interfaces should preserve:

- Sensor identity.
- Timestamp.
- Sequence information where required.
- Frame/reference information.
- Calibration information.
- Validity/status.
- Units.
- Quality/confidence where meaningful.

Avoid passing untyped arrays when a stable semantic data type is appropriate.

## 7. Coordinate Frames

For spatial data, explicitly define:

- Frame name/identity.
- Parent frame.
- Transform direction.
- Units.
- Convention.

Do not silently mix coordinate conventions.

Where ROS2 integration is used, maintain compatibility with the relevant TF/frame conventions rather than inventing an incompatible parallel system.

## 8. Units

Physical quantities must have explicit units.

Common examples:

- Position: m.
- Velocity: m/s.
- Acceleration: m/s².
- Angle: rad unless an interface explicitly requires another unit.
- Angular velocity: rad/s.
- Force: N.
- Torque: N·m.
- Time: s or explicit duration type.

Do not mix degrees/radians or metric/non-metric units implicitly.

## 9. Hardware Abstraction

Robotics algorithms should depend on stable abstractions rather than:

- GPIO registers.
- Vendor-specific peripheral APIs.
- EtherCAT implementation details.
- Specific MCU SDKs.
- FPGA register maps.

Hardware-specific behavior belongs behind the appropriate HAL/driver boundary.

## 10. Distributed Robotics

Assume that a robot may contain multiple compute domains.

Examples:

- Nexus for global computation.
- Multiple Edge controllers for joints/actuators.
- Subnodes for local I/O.
- Endpoints for physical devices.

Interfaces must account for:

- Communication delay.
- Packet loss/drop.
- Clock differences.
- Fault isolation.
- Reconnection.
- Stale data.
- Command validity windows.

Do not assume every subsystem shares a process or memory space.

## 11. EtherCAT

EtherCAT may be a first-class transport for distributed real-time control.

Keep EtherCAT-specific mechanisms below stable robotics APIs where practical.

Do not make application or skill logic depend directly on EtherCAT frames.

## 12. ROS2 Integration

ROS2 is an ecosystem/integration layer, not the definition of Kritva architecture.

Use ROS2 where it provides value for:

- Ecosystem interoperability.
- Tools.
- Visualization.
- Development workflows.
- Existing robotics packages.

Do not duplicate mature ROS2 functionality without a clear architectural reason.

Where appropriate, reuse `ros2_control` and micro-ROS rather than creating incompatible replacements.

## 13. AI Integration

AI interfaces should specify:

- Input schema.
- Output schema.
- Model/version identity.
- Timestamp/context.
- Confidence/uncertainty where available.
- Latency expectations.
- Resource requirements.
- Failure behavior.

Do not assume AI inference is deterministic unless the implementation and requirements establish that property.

## 14. AI and Real-Time Separation

A robust architecture normally keeps:

    AI / Planning
          ↓
    bounded command interface
          ↓
    deterministic controller
          ↓
    actuator

Do not allow an AI model to directly bypass safety and real-time control boundaries.

## 15. Safety

Robotics code should define behavior for:

- Invalid sensor data.
- Communication loss.
- Stale commands.
- Controller timeout.
- Actuator faults.
- Emergency stop.
- Limit violations.
- Unexpected state.

Safety behavior must be explicit and testable.

Never describe a system as "safe" solely because it has a software check.

## 16. Lifecycle / Health / Diagnostics

Use Kritva Core concepts consistently:

- Lifecycle.
- Status.
- Health.
- Statistics.
- Errors.
- Events.
- Capabilities.
- Configuration.
- Time.

Keep operational status distinct from health.

Example:

    Status = RUNNING
    Health = DEGRADED

Do not overload one field to represent both.

## 17. Configuration

Configuration should be:

- Typed.
- Validated.
- Versioned where required.
- Traceable.
- Separated from source code.

Do not hard-code robot-specific calibration, limits, network addresses, or hardware mappings into generic algorithms.

## 18. Robot Variants

Kritva should support multiple robot types through a common architecture with specialization where necessary.

Examples:

- Humanoid.
- Manipulator/cobot.
- Mobile robot.
- Quadruped.
- Aerial robot.

Specialization should primarily occur in:

- Motion.
- Sense.
- Hardware.
- Drivers.
- Robot configuration.

Do not fork the complete software architecture for each robot type.

## 19. Simulation and Digital Twin

Robotics code should be designed so that appropriate components can run in:

- Unit tests.
- Simulation.
- SIL.
- HIL.
- FPGA/SoC prototypes.
- Physical robots.

Avoid unnecessary dependencies on physical hardware in algorithmic code.

## 20. Testing

Use multiple testing levels:

1. Unit tests.
2. Contract/interface tests.
3. Simulation tests.
4. Integration tests.
5. HIL tests.
6. Physical robot tests.

Test nominal behavior and failure behavior.

For control and safety paths, include boundary and timing-related cases.

## 21. Observability

Important robotics components should expose:

- State.
- Health.
- Counters.
- Timing.
- Errors.
- Events.
- Resource utilization where appropriate.

Do not rely exclusively on printf-style debugging.

## 22. Performance

For performance-sensitive robotics paths, consider:

- CPU utilization.
- Memory allocation.
- Copy count.
- Cache behavior.
- Data locality.
- Queue depth.
- Serialization overhead.
- Network latency.
- Sensor-to-action latency.

Optimize only after identifying the relevant bottleneck, unless a hard real-time requirement already dictates the design.

## 23. Dependency Discipline

Prefer portable interfaces.

Avoid coupling core robotics algorithms directly to:

- Cloud services.
- Vendor-specific SDKs.
- One robot manufacturer.
- One AI model provider.

External integrations should be adapters where practical.

## 24. AI-Agent Change Discipline

Before modifying robotics code:

1. Read the applicable architecture.
2. Identify the module boundary.
3. Identify the timing and safety requirements.
4. Inspect existing APIs and data types.
5. Inspect tests and simulation coverage.
6. Make the smallest coherent change.
7. Add/update tests.
8. Document changed contracts.

Never invent physical limits, actuator capabilities, sensor characteristics, or robot topology.

If the required information is missing, mark it as an assumption for human review.

## 25. Review Checklist

- [ ] Correct Kritva module ownership.
- [ ] Real-time boundary preserved.
- [ ] Hardware abstraction preserved.
- [ ] Sensor timestamps/frames/units are explicit.
- [ ] Distributed-system failure modes considered.
- [ ] Safety behavior defined.
- [ ] Lifecycle/status/health used consistently.
- [ ] AI is separated from deterministic control where required.
- [ ] ROS2 integration does not redefine Kritva architecture.
- [ ] Simulation/test path exists.
- [ ] Robot-specific configuration is not embedded in generic code.
- [ ] Performance implications considered.
- [ ] API and documentation updated when contracts change.
