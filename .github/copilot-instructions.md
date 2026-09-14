# Kritva Copilot Instructions

## 1. Repository Context

Kritva is an Open Robotic Computing Platform.

KritvaOS is the open robotic software platform within the broader Kritva
architecture.

Kritva connects Physical AI to physical action through an open,
hardware-aware, distributed robotic computing architecture.

The primary architectural principle is:

> Kritva connects intelligence to physical action.

The repository is currently in an early/pre-alpha engineering stage.
Prefer clear interfaces, architectural correctness, testability, and
incremental validation over premature optimization.

---

## 2. Read the Architecture Before Implementing

Before making a significant change, inspect the relevant architecture and
repository documentation.

Primary references:

- `AGENTS.md`
- `ARCHITECTURE.md`
- `docs/architecture/REPOSITORY_ARCHITECTURE.md`
- `docs/architecture/`
- `docs/requirements/`
- `docs/api/`
- `docs/verification/`
- `docs/adr/`
- `toolchain/VERSIONS.yaml`
- Relevant component documentation

`ARCHITECTURE.md` is the canonical system architecture.

`REPOSITORY_ARCHITECTURE.md` defines repository boundaries.

`VERSIONS.yaml` is the source of truth for the development toolchain.

Do not silently contradict these documents.

If implementation requires an architectural change, identify it explicitly
and propose the change before making broad modifications.

---

## 3. Kritva System Architecture

Use the following conceptual stack:

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
Nexus
    ↓
Edge
    ↓
Subnode
    ↓
Endpoint
    ↓
Physical World

Do not bypass architectural layers without a clear reason.

Do not introduce unnecessary cross-layer coupling.

---

## 4. Major Software Components

### Kritva Core

Core is the platform foundation.

Core Foundation provides common primitives such as:

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

Keep Core platform-independent.

Do not place the following into Core Foundation unless explicitly
architected:

- robot-specific algorithms
- ROS2 dependencies
- EtherCAT implementation
- vendor-specific drivers
- cloud services
- database dependencies
- application logic
- robot-specific perception
- motion algorithms

### Kritva Sense

Responsible for sensor integration and perception interfaces.

Examples:

- camera
- IMU
- LiDAR
- force/torque
- encoder
- tactile sensing

Keep hardware-specific implementation behind appropriate abstraction
boundaries.

### Kritva Mind

Responsible for intelligence and higher-level reasoning.

Examples:

- AI inference
- planning
- reasoning
- world models
- task planning
- decision making

Mind should not directly depend on low-level hardware implementation.

### Kritva Motion

Responsible for physical motion and control.

Examples:

- locomotion
- manipulation
- kinematics
- dynamics
- balance
- trajectory generation
- control
- motion planning
- joint coordination

Real-time constraints must be considered explicitly.

### Kritva Skill

Responsible for reusable robotic capabilities and behaviors.

Skills should compose lower-level capabilities rather than duplicate them.

### Kritva Sim

Responsible for simulation and validation.

Examples:

- digital twins
- software-in-the-loop
- hardware-in-the-loop
- Renode
- regression environments

Prefer interfaces that can be reused between simulation and physical systems.

### Kritva SDK

Provides developer-facing and customer-facing APIs.

Treat public SDK interfaces as externally consumable APIs.

---

## 5. Kritva Computing Hierarchy

The physical computing hierarchy is:

Nexus
    ↓
Edge
    ↓
Subnode
    ↓
Endpoint

### Nexus

Nexus is the system-level compute platform.

Typical responsibilities:

- high-level compute
- AI acceleration
- system orchestration
- system management
- storage
- security
- networking
- global services
- system-level EtherCAT coordination

Nexus is a compute platform family, not one mandatory fixed SoC.

### Edge

Edge is the distributed real-time compute platform.

Typical responsibilities:

- motor control
- joint control
- encoder processing
- deterministic I/O
- PWM
- ADC
- SPI
- I2C
- UART
- GPIO
- EtherCAT communication

Edge is a compute platform family, not one mandatory fixed SoC.

Do not assume that every robot requires the same physical Nexus or Edge
implementation.

---

## 6. Robot-Type Independence

Kritva supports a common architecture across multiple robot types.

Examples include:

- humanoid
- manipulator
- cobot
- mobile robot
- quadruped
- aerial robot

Do not create a separate architecture for every robot type.

Prefer:

Common Kritva Platform
    +
Robot-Specific Sense
    +
Robot-Specific Motion
    +
Robot-Specific Hardware
    =
Robot Implementation

Robot-specific behavior should remain localized.

---

## 7. External Ecosystem

Kritva should integrate with mature technologies where appropriate.

Examples:

- Linux
- PREEMPT_RT
- ROS2
- ros2_control
- micro-ROS
- EtherCAT
- Renode
- Verilator
- CMake
- Clang

Do not reimplement mature ecosystem functionality without a clear
architectural, performance, portability, or IP reason.

ROS2 is an integration ecosystem, not a reason to introduce ROS2 dependencies
into every Kritva component.

---

## 8. Real-Time Rules

When modifying real-time code, consider:

- deterministic execution
- bounded latency
- jitter
- scheduling
- priority
- synchronization
- memory allocation
- locking
- queue behavior
- communication latency
- interrupt behavior

Avoid uncontrolled operations in hard real-time paths, including:

- blocking I/O
- filesystem operations
- network operations
- unbounded loops
- uncontrolled dynamic allocation
- blocking locks

unless explicitly justified.

Do not claim code is real-time safe without appropriate evidence.

---

## 9. Hardware and RTL Rules

Never invent hardware details.

Do not assume registers, memory maps, interrupts, clocks, DMA channels,
peripherals, accelerators, or bus behavior unless supported by the available
hardware specification.

For SoC/RTL work:

Requirements
    ↓
Architecture
    ↓
Interface
    ↓
RTL / Software
    ↓
Simulation
    ↓
FPGA
    ↓
HIL
    ↓
Silicon

Keep hardware-specific assumptions explicit.

---

## 10. API-First Development

For new functionality:

1. Identify the requirement.
2. Identify the owning component.
3. Define or inspect the API.
4. Define behavior and failure modes.
5. Define tests.
6. Implement.
7. Validate.

Prefer stable interfaces over implementation-specific coupling.

Do not modify public APIs unnecessarily.

---

## 11. Requirement and Verification Traceability

Use:

Requirement
    ↓
Architecture
    ↓
API
    ↓
Implementation
    ↓
Test
    ↓
Verification

A feature is not complete merely because it compiles.

Consider:

- normal behavior
- boundary conditions
- invalid inputs
- error handling
- recovery
- interface contracts
- deterministic behavior where required

---

## 12. Coding Style

Follow repository formatting and toolchain configuration.

Primary standards:

- C++20
- CMake
- Clang
- clang-format
- clang-tidy
- Python 3.12 where Python is used

Do not introduce a new style convention inside a component.

Follow:

- `.clang-format`
- `.editorconfig`
- existing component conventions

Prefer simple, readable, strongly typed code.

Avoid unnecessary abstraction and framework dependencies.

---

## 13. Toolchain

The source of truth is:

```text
toolchain/VERSIONS.yaml
```

The standard development environment is:

```text
kritvaos-dev:0.1
```

The standard CI validation command is:

```bash
make ci
```

When possible, validate changes using the Kritva-defined container/toolchain
rather than arbitrary host versions.

Do not casually change tool versions.

Toolchain changes should include rationale and appropriate validation.

---

## 14. Testing and CI

For relevant changes, run the smallest appropriate validation first.

Examples:

```bash
make check-toolchain
make check-toolchain TOOLCHAIN_PROFILE=ci
make check-toolchain-strict TOOLCHAIN_PROFILE=ci
make ci
```

When CMake/build infrastructure is available, also use the repository's
canonical configure, build, and test commands.

Do not remove or weaken tests to make CI pass.

Do not report tests as passing unless they were actually executed.

---

## 15. Change Discipline

Prefer small and focused changes.

Do not combine unrelated:

- refactoring
- formatting
- architecture changes
- feature implementation
- dependency upgrades

into one change unless there is a clear reason.

Before editing:

1. Inspect the existing implementation.
2. Inspect related tests.
3. Identify dependencies.
4. Identify architectural ownership.
5. Determine whether documentation or an ADR is required.

After editing:

1. Review the diff.
2. Build affected components.
3. Run relevant tests.
4. Run formatting/static checks.
5. Run CI where appropriate.
6. Check for unintended changes.
7. Update documentation when required.

---

## 16. Architecture Change Discipline

Do not silently change architecture through implementation.

Changes affecting the following should be treated as architectural:

- Core boundaries
- Nexus / Edge boundaries
- communication architecture
- real-time architecture
- hardware/software boundaries
- public APIs
- SoC architecture
- repository structure
- safety architecture
- security architecture
- external middleware strategy

For significant architectural changes, propose an ADR.

---

## 17. Safety

Kritva software can control physical systems.

Treat changes affecting:

- motors
- actuators
- brakes
- power
- joint movement
- limits
- emergency behavior
- safety states

as safety-sensitive.

Do not remove or weaken safety checks simply to pass tests.

Clearly identify safety-impacting changes.

Human review is required for safety-critical changes.

---

## 18. Security

Never commit:

- passwords
- tokens
- private keys
- credentials
- production certificates
- secrets

Security-sensitive changes must be explicitly identified.

Consider security across:

- boot
- identity
- authentication
- authorization
- communication
- firmware
- storage
- update mechanisms
- AI models
- physical interfaces

---

## 19. Intellectual Property and Licensing

Kritva is intended to support an open ecosystem while preserving reusable
Kritva architecture and IP.

Before incorporating external code, RTL, models, documentation, or other
material:

1. Check its license.
2. Check compatibility with the Kritva repository license.
3. Avoid copying proprietary material.
4. Preserve required attribution.
5. Identify licensing concerns explicitly.

Do not introduce customer-specific proprietary implementation into common
Kritva repositories without an appropriate boundary.

---

## 20. Customer-Specific Development

Prefer:

Common Kritva Platform
        +
Customer Adapter
        +
Customer-Specific Implementation

rather than permanently modifying common platform functionality for one
customer.

If customer work reveals a generally useful capability, propose it for
inclusion in the common platform.

---

## 21. AI Agent Behavior

Act as an engineering assistant.

Before proposing or implementing code:

- inspect the repository
- understand the relevant architecture
- identify assumptions
- identify dependencies
- identify risks
- identify tests

Prefer minimal, reviewable changes.

Never:

- hallucinate hardware
- hallucinate APIs
- remove tests to pass CI
- weaken safety checks
- bypass security
- silently change architecture
- introduce unnecessary dependencies
- perform unrelated refactoring
- claim validation that was not performed

When uncertain, state the uncertainty.

When an architectural decision is required, explain the tradeoffs rather
than silently choosing one.

---

## 22. First Vertical Slice

The most important early system proof is:

Application
    ↓
Skill
    ↓
Mind Interface
    ↓
Kritva Core
    ↓
Nexus
    ↓
EtherCAT
    ↓
Edge
    ↓
Motor + Encoder

Prefer development decisions that help establish this end-to-end vertical
slice without prematurely building the entire robotics ecosystem.

---

## 23. Current Engineering Priority

The current priority sequence is:

1. Repository foundation
2. Toolchain reproducibility
3. CMake build foundation
4. Kritva Core Foundation
5. Unit and contract testing
6. CI
7. Hardware abstraction
8. Nexus / Edge interfaces
9. Simulation
10. First vertical slice

Avoid premature implementation of future capabilities that are not required
to establish the current foundation.

---

## 24. Preferred Engineering Decision

When multiple technically valid solutions exist, prefer the solution that:

1. preserves architectural boundaries
2. minimizes coupling
3. keeps interfaces stable
4. is testable
5. is deterministic where required
6. is portable across compute platforms
7. supports simulation
8. minimizes unnecessary dependencies
9. preserves future hardware freedom
10. is understandable to future engineers

---

## 25. Final Principle

Before changing code:

> Understand the architecture.

Before adding an interface:

> Understand the requirement.

Before adding a dependency:

> Understand why it is necessary.

Before changing hardware behavior:

> Understand the physical consequences.

Before declaring completion:

> Verify it.

Kritva's goal is:

> One Kritva architecture.
> Multiple robotic systems.
> Multiple compute implementations.
> Stable interfaces.
> Reusable software and hardware IP.
> Open ecosystem.
> Validated from simulation to physical robot.
