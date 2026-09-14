# Kritva GitHub Engineering & AI Configuration

This directory contains the GitHub-native engineering, AI-agent, code-review,
and CI configuration for the **Kritva Open Robotic Computing Platform**.

The `.github/` directory defines how humans and AI coding agents collaborate
on Kritva source code, architecture, verification, and releases.

---

## 1. Purpose

The Kritva repository uses a layered AI-assisted engineering model.

The configuration under `.github/` provides:

- Repository-wide engineering guidance.
- Path-specific coding instructions.
- Specialist AI agents.
- Cross-layer code review.
- Verification guidance.
- Continuous integration.

The objective is to ensure that AI-assisted development remains aligned with
the Kritva architecture rather than allowing each coding task to make
independent architectural decisions.

---

## 2. Directory Structure

```text
.github/
├── agents/
│   ├── kritva-architect.agent.md
│   ├── kritva-code-reviewer.agent.md
│   ├── kritva-core.agent.md
│   ├── kritva-rtl-reviewer.agent.md
│   └── kritva-verification.agent.md
│
├── copilot-instructions.md
│
├── instructions/
│   ├── core.instructions.md
│   ├── cpp.instructions.md
│   ├── documentation.instructions.md
│   ├── robotics.instructions.md
│   ├── rtl.instructions.md
│   ├── simulation.instructions.md
│   └── soc.instructions.md
│
└── workflows/
    └── ci.yml
```

---

# 3. Configuration Layers

Kritva uses three main AI configuration layers:

```text
                    Kritva Architecture
                           │
                           ▼
                copilot-instructions.md
                  Repository-wide rules
                           │
             ┌─────────────┴─────────────┐
             ▼                           ▼
      instructions/                  agents/
      Path-specific rules            Specialist roles
             │                           │
             └─────────────┬─────────────┘
                           ▼
                    Implementation
                           │
                           ▼
                       CI / Tests
```

These layers have different responsibilities.

| Layer | Purpose |
|---|---|
| `copilot-instructions.md` | Global Kritva context and engineering rules |
| `instructions/*.instructions.md` | Rules associated with specific paths/file types |
| `agents/*.agent.md` | Specialist AI roles for architecture, implementation, review, and verification |
| `workflows/*.yml` | Automated repository validation |

---

# 4. Repository-Wide Instructions

## `copilot-instructions.md`

### Focus

`copilot-instructions.md` is the repository-wide Kritva context for GitHub
Copilot.

It should establish:

- What Kritva is.
- Kritva's architectural thesis.
- Canonical terminology.
- High-level software architecture.
- Nexus/Edge/Subnode/Endpoint hierarchy.
- Engineering principles.
- Repository conventions.
- Toolchain expectations.
- AI-agent engineering discipline.

### Primary question

> What does an AI coding agent need to know about Kritva before making any change?

### Scope

Repository-wide.

It is not intended to replace detailed path-specific instructions.

---

# 5. Path-Specific Instructions

The files under `instructions/` define technical rules for particular parts
of the repository.

They are implementation guidance, not autonomous architectural authority.

---

## `instructions/core.instructions.md`

### Focus

Kritva Core Foundation.

Applies primarily to:

```text
core/**
```

Covers:

- Core Foundation boundaries.
- C++20 implementation.
- Lifecycle.
- Status.
- Health.
- Statistics.
- Errors.
- `Result<T>`.
- Events.
- Capabilities.
- Configuration.
- Time.
- Ownership/lifetime.
- Thread safety.
- Real-time considerations.
- Core API stability.
- Core testing.

### Primary question

> Is this implementation appropriate for a small, stable, platform-independent Kritva Core?

### Should avoid

- Robotics algorithms.
- Sensor drivers.
- Motor control.
- ROS2/DDS.
- EtherCAT implementation.
- SoC-specific code.
- Cloud/database dependencies.

---

## `instructions/cpp.instructions.md`

### Focus

General C++ engineering.

Applies to:

```text
**/*.{cpp,cc,cxx,h,hpp,hxx}
```

Covers:

- C++20.
- RAII.
- Ownership.
- Lifetime.
- Const correctness.
- Strong types.
- Error handling.
- Concurrency.
- Real-time C++.
- Undefined behavior.
- Initialization.
- Performance.
- API compatibility.
- Testing.
- Formatting and static analysis.

### Primary question

> Is this C++ implementation correct, safe, maintainable, and consistent with Kritva engineering standards?

### Relationship with Core instructions

```text
cpp.instructions.md
        +
core.instructions.md
        ↓
core/**
```

`cpp.instructions.md` defines general C++ rules.

`core.instructions.md` adds Core-specific architectural constraints.

---

## `instructions/rtl.instructions.md`

### Focus

RTL/SystemVerilog engineering.

Applies primarily to:

```text
RTL / SystemVerilog files
```

Covers:

- SystemVerilog baseline.
- Synthesizability.
- Sequential logic.
- Combinational logic.
- FSMs.
- Width/signedness.
- Parameters.
- CDC.
- Reset.
- Protocols.
- Register interfaces.
- Timing.
- Safety.
- Security.
- FPGA portability.
- RTL verification.

### Primary question

> Does this RTL implement the intended hardware behavior correctly, deterministically, and synthesizably?

---

## `instructions/soc.instructions.md`

### Focus

Kritva SoC engineering.

Applies primarily to:

```text
soc/**
```

Covers:

- Nexus architecture.
- Edge architecture.
- HW/SW boundaries.
- Register interfaces.
- Clock/reset architecture.
- CDC.
- DMA.
- Memory.
- Real-time requirements.
- Safety.
- Security.
- FPGA-to-silicon migration.
- SoC verification.

### Primary question

> Does this SoC implementation preserve the Kritva robotic computing architecture and its hardware/software contracts?

### Relationship with RTL

```text
soc.instructions.md
        │
        │ SoC architecture/context
        ▼
rtl.instructions.md
        │
        │ RTL implementation rules
        ▼
SystemVerilog
```

The SoC instructions define the platform context.

The RTL instructions define implementation discipline.

---

## `instructions/robotics.instructions.md`

### Focus

Robotics software and physical-system integration.

Applies primarily to:

```text
sense/**
mind/**
motion/**
skill/**
robots/**
hardware/**
drivers/**
examples/**
```

Covers:

- Sensor interfaces.
- Perception.
- AI/planning.
- Motion.
- Skills.
- Robot topology.
- Hardware abstraction.
- Distributed computing.
- EtherCAT integration.
- ROS2 integration.
- Coordinate frames.
- Units.
- Timestamps.
- Safety.
- Real-time boundaries.
- Simulation.
- Physical robot testing.

### Primary question

> Does this robotics implementation correctly connect intelligence, control, compute, and the physical world?

### Important boundary

AI/planning must not bypass deterministic control and safety boundaries.

Preferred flow:

```text
AI / Planning
      ↓
Bounded command interface
      ↓
Deterministic controller
      ↓
Edge
      ↓
Actuator
```

---

## `instructions/simulation.instructions.md`

### Focus

Simulation and digital-twin engineering.

Applies primarily to:

```text
sim/**
tests/**
tools/**
```

Covers:

- Simulation architecture.
- Functional/timing fidelity.
- Renode.
- RTL simulation.
- Verilator.
- SIL.
- HIL.
- Robot simulation.
- Deterministic testing.
- Fault injection.
- Distributed simulation.
- Simulation configuration.
- Regression.

### Primary question

> Does the simulation provide trustworthy evidence about the real system while preserving the same architectural contracts?

### Development path

```text
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
```

---

## `instructions/documentation.instructions.md`

### Focus

Technical documentation.

Applies primarily to:

```text
*.md
*.mdx
*.rst
*.txt
```

Covers:

- Documentation hierarchy.
- Source-of-truth rules.
- Architecture documentation.
- Requirements.
- APIs.
- Verification.
- ADRs.
- Diagrams.
- Tables.
- Version/status terminology.
- Technical claims.
- Safety/security language.
- Documentation consistency.

### Primary question

> Does this document accurately describe the intended Kritva architecture, requirements, implementation status, and engineering decisions?

### Key rule

Do not duplicate authoritative information across multiple documents.

Prefer:

```text
Requirement
    ↓
Architecture
    ↓
API
    ↓
Implementation
    ↓
Verification
```

---

# 6. Specialist AI Agents

Agents under `.github/agents/` represent specialist engineering roles.

They should not all make the same decisions.

---

## `agents/kritva-architect.agent.md`

### Role

**System Architect**

### Focus

- Kritva system architecture.
- Architectural boundaries.
- Software/hardware partitioning.
- Nexus/Edge architecture.
- Distributed computing.
- Interfaces.
- Architecture trade-offs.
- ADRs.
- Repository architecture.
- Long-term platform strategy.

### Primary question

> Is this the right architecture?

### Authority

Highest architectural authority among the current AI agents.

The agent should identify implementation requirements rather than independently
rewriting implementation code.

---

## `agents/kritva-core.agent.md`

### Role

**Kritva Core Specialist**

### Focus

- `core/**`
- Core Foundation APIs.
- Platform-independent primitives.
- Lifecycle.
- Status/health.
- Errors.
- Result types.
- Events.
- Capabilities.
- Configuration.
- Time.
- Core tests.

### Primary question

> Is the Kritva Core Foundation API and implementation correct, minimal, stable, and portable?

### Authority

Specialist authority for Core implementation.

Architectural changes outside Core remain subject to `kritva-architect`.

---

## `agents/kritva-rtl-reviewer.agent.md`

### Role

**RTL Design / Verification Reviewer**

### Focus

- SystemVerilog.
- RTL correctness.
- CDC.
- Reset.
- FSMs.
- Protocols.
- Register interfaces.
- Timing.
- Synthesizability.
- FPGA portability.
- Hardware safety/security.
- RTL verification.

### Primary question

> Is the RTL implementation technically correct and verifiable?

### Authority

RTL review authority.

It should not independently redefine the Nexus/Edge architecture.

---

## `agents/kritva-verification.agent.md`

### Role

**Verification Engineer**

### Focus

- Requirements traceability.
- Test planning.
- Unit tests.
- Contract tests.
- RTL verification.
- Integration testing.
- Renode.
- SIL/HIL.
- Regression.
- Coverage.
- Verification evidence.
- Release readiness.

### Primary question

> Do we have sufficient evidence that the requirement works as intended?

### Authority

Verification authority.

It should not weaken tests merely to make an implementation pass.

---

## `agents/kritva-code-reviewer.agent.md`

### Role

**Cross-Layer Code Reviewer**

### Focus

- General code review.
- Cross-module consistency.
- Architecture boundary violations.
- C/C++.
- Python.
- Robotics.
- Tools.
- Tests.
- Build/toolchain.
- Safety/security.
- Maintainability.

### Primary question

> Is this change correct, maintainable, testable, reproducible, and consistent with Kritva?

### Authority

Broad final review.

Specialist agents remain authoritative for their domains.

---

# 7. Agent Responsibility Matrix

| Area | Architect | Core | RTL Reviewer | Verification | Code Reviewer |
|---|:---:|:---:|:---:|:---:|:---:|
| System architecture | **Primary** | Consult | Consult | Consult | Review |
| Repository architecture | **Primary** | Consult | Consult | Consult | Review |
| Core Foundation | Review | **Primary** | — | Verify | Review |
| General C++ | Review | Primary for Core | — | Verify | **Review** |
| Robotics | **Architecture** | — | — | Verify | **Review** |
| SoC architecture | **Primary** | — | Review | Verify | Review |
| RTL implementation | Review | — | **Primary** | Verify | Review |
| Simulation | Architecture | — | Hardware aspects | **Primary** | Review |
| Test strategy | Consult | Consult | Consult | **Primary** | Review |
| Requirements traceability | **Primary** | Consult | Consult | **Primary** | Review |
| Safety | **Architecture** | Foundation | Hardware review | **Verification** | Review |
| Security | **Architecture** | Foundation | Hardware review | **Verification** | Review |
| PR/code quality | Review | Specialist | Specialist | Specialist | **Primary** |
| ADRs | **Primary** | Consult | Consult | Consult | Review |
| Release evidence | Consult | Consult | Consult | **Primary** | Review |

---

# 8. Agent Interaction Model

The intended engineering flow is:

```text
                    kritva-architect
                           │
                  Architecture / ADR
                           │
                           ▼
                    Implementation
                           │
          ┌────────────────┼────────────────┐
          │                │                │
          ▼                ▼                ▼
     kritva-core    kritva-rtl-reviewer   Robotics/
                                         Software
          │                │                │
          └────────────────┼────────────────┘
                           ▼
                  kritva-verification
                           │
                    Test / Evidence
                           │
                           ▼
                  kritva-code-reviewer
                           │
                           ▼
                         Merge
```

This is a logical responsibility model. Agents may be invoked independently
when appropriate.

---

# 9. Recommended Review Sequence

For a significant architectural feature:

```text
1. kritva-architect
       ↓
2. Specialist implementation agent
       ↓
3. kritva-verification
       ↓
4. kritva-code-reviewer
```

For an RTL feature:

```text
1. Architecture / SoC specification
       ↓
2. RTL implementation
       ↓
3. kritva-rtl-reviewer
       ↓
4. kritva-verification
       ↓
5. kritva-code-reviewer
```

For Kritva Core:

```text
1. Architecture
       ↓
2. kritva-core
       ↓
3. Unit / contract tests
       ↓
4. kritva-verification
       ↓
5. kritva-code-reviewer
```

---

# 10. CI Workflow

## `workflows/ci.yml`

### Focus

Automated repository validation.

The CI workflow should validate the repository using the defined Kritva
toolchain and remain aligned with the local development workflow.

Current primary responsibility:

- Checkout repository.
- Build the Kritva development container.
- Validate the CI toolchain.
- Run strict toolchain validation.
- Validate machine-readable toolchain output.

### Primary question

> Does the repository remain reproducible and consistent with the defined CI toolchain?

CI should eventually expand, as implementation grows, to include:

- Configure.
- Build.
- Unit tests.
- Static analysis.
- Formatting checks.
- Simulation regressions.
- RTL lint/simulation.
- Integration tests.

---

# 11. How the Files Work Together

```text
.github/
│
├── copilot-instructions.md
│       │
│       └── Global Kritva engineering context
│
├── instructions/
│       │
│       ├── core.instructions.md
│       ├── cpp.instructions.md
│       ├── documentation.instructions.md
│       ├── robotics.instructions.md
│       ├── rtl.instructions.md
│       ├── simulation.instructions.md
│       └── soc.instructions.md
│               │
│               └── Path-specific engineering rules
│
├── agents/
│       │
│       ├── kritva-architect.agent.md
│       ├── kritva-code-reviewer.agent.md
│       ├── kritva-core.agent.md
│       ├── kritva-rtl-reviewer.agent.md
│       └── kritva-verification.agent.md
│               │
│               └── Specialist AI engineering roles
│
└── workflows/
        │
        └── ci.yml
                │
                └── Automated validation
```

---

# 12. Important Rule: Instructions vs Agents

These are intentionally different.

### Instructions

Instructions answer:

> What rules should apply when working on this file/path?

They are primarily passive engineering guidance.

### Agents

Agents answer:

> What specialist role should the AI perform for this task?

They provide a focused engineering persona and review responsibility.

Therefore:

```text
Instructions = constraints
Agents       = expertise
CI            = automated enforcement
```

---

# 13. Current AI Engineering Coverage

The current configuration covers the major Kritva engineering domains:

```text
                         KRITVA
                           │
        ┌──────────────────┼──────────────────┐
        │                  │                  │
    Architecture        Software           Hardware
        │                  │                  │
   Architect          Core / C++          SoC / RTL
        │                  │                  │
        └──────────────┬───┴───────┬──────────┘
                       │           │
                   Robotics     Simulation
                       │           │
                       └─────┬─────┘
                             │
                       Verification
                             │
                       Code Review
                             │
                            CI
```

This is the intended foundation for AI-assisted Kritva development.

---

# 14. Future Agents

Additional specialist agents can be added when the corresponding engineering
work becomes substantial.

Potential future agents include:

```text
kritva-soc-architect.agent.md
kritva-robotics.agent.md
kritva-simulation.agent.md
kritva-safety-reviewer.agent.md
kritva-security-reviewer.agent.md
kritva-performance.agent.md
kritva-release.agent.md
kritva-toolchain.agent.md
kritva-documentation.agent.md
```

Do not create every possible agent prematurely.

A new agent should be introduced when:

1. The domain has meaningful complexity.
2. Existing agents have insufficient specialization.
3. The responsibility can be clearly bounded.
4. The agent has a distinct review question.

---

# 15. Design Principle

The `.github/` configuration follows one fundamental principle:

> AI should accelerate Kritva engineering without becoming an uncontrolled source of architectural decisions.

The architecture remains the foundation.

Specialist agents implement and review within that architecture.

Verification provides evidence.

Cross-layer review checks integration.

CI provides automated enforcement.

---

# 16. Summary

```text
copilot-instructions.md
    = Global Kritva context

instructions/
    = File/path-specific engineering rules

agents/
    = Specialist AI engineering roles

workflows/
    = Automated repository validation
```

Together they form the Kritva AI-assisted engineering framework.

> Architecture defines the system.
> Instructions constrain implementation.
> Agents provide specialist expertise.
> Verification provides evidence.
> Review protects quality.
> CI enforces reproducibility.
