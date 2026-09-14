---
applyTo: "**/*.{md,mdx,rst,txt}"
---

# Kritva Documentation Engineering Instructions

## Purpose

These instructions apply to Kritva documentation, including architecture, requirements, API documentation, verification documentation, ADRs, project plans, READMEs, developer guides, and technical notes.

Documentation is part of the engineering system. It should make architecture, interfaces, decisions, requirements, and verification traceable and understandable.

## 1. Documentation Hierarchy

Use the repository documentation structure consistently:

- `README.md` — project introduction and public orientation.
- `ARCHITECTURE.md` — canonical system architecture.
- `docs/architecture/` — detailed architecture.
- `docs/requirements/` — requirements.
- `docs/api/` — API/interface documentation.
- `docs/verification/` — verification strategy and evidence.
- `docs/adr/` — architecture decisions.
- `project/` — roadmap, plans, milestones, tracking, releases, baselines.

Do not duplicate the same authoritative content across multiple documents.

## 2. Source of Truth

Each important topic should have one canonical source.

Examples:

- System architecture → `ARCHITECTURE.md`
- Repository structure → `docs/architecture/REPOSITORY_ARCHITECTURE.md`
- Tool versions → `toolchain/VERSIONS.yaml`
- Requirements → `docs/requirements/`
- Architecture decisions → `docs/adr/`
- Release status → `project/releases/`
- Engineering status → `project/tracking/`

Other documents should link/reference the source rather than silently maintaining a competing copy.

## 3. Kritva Terminology

Use canonical terminology:

- Kritva — Open Robotic Computing Platform.
- KritvaOS — robotic software platform.
- Kritva Core.
- Kritva Sense.
- Kritva Mind.
- Kritva Motion.
- Kritva Skill.
- Kritva Sim.
- Kritva SDK.
- Kritva Nexus SoC.
- Kritva Edge SoC.
- Subnode.
- Endpoint.

Use "Master" and "Node" as descriptive technical roles only where appropriate; do not use them as product names.

## 4. Architectural Positioning

Preserve the core architectural thesis:

> Kritva connects intelligence to physical action.

The platform should be described as connecting:

    Physical AI
        ↕
    Robotics Software
        ↕
    Real-Time Computing
        ↕
    Distributed Compute
        ↕
    Hardware
        ↕
    Physical Endpoints

Do not reduce Kritva to only an operating system, only a robotics middleware, or only a SoC.

## 5. Architecture vs Implementation

Architecture documents explain:

- Why.
- What.
- Boundaries.
- Responsibilities.
- Interfaces.
- Constraints.
- Trade-offs.

Implementation documents explain:

- How.

Do not turn an architecture document into a source-code walkthrough unless that detail is necessary to define an interface or constraint.

## 6. Requirements Traceability

Important technical claims should be traceable.

Prefer:

    Requirement → Architecture → API → Implementation → Test

Where identifiers exist, use them consistently.

Do not invent requirement IDs merely to make a document appear formal.

## 7. API Documentation

For public APIs document:

- Purpose.
- Inputs.
- Outputs.
- Ownership/lifetime.
- Error behavior.
- Thread-safety expectations.
- Real-time constraints where relevant.
- Version/compatibility expectations.
- Example usage when useful.

Avoid documenting implementation details that are not part of the contract.

## 8. Architecture Diagrams

Diagrams should clarify boundaries and data/control flow.

Prefer simple, stable diagrams using Mermaid or repository-supported formats where practical.

A canonical system flow is:

    Application
        ↓
    SDK
        ↓
    Skill
        ↓
    Mind / Motion
        ↓
    Sense
        ↓
    Core
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

Do not create diagrams that contradict the canonical architecture.

## 9. Tables

Use tables for structured information such as:

- Responsibilities.
- Interfaces.
- Requirements.
- Compatibility.
- Tool versions.
- Release matrices.
- Verification status.

Avoid excessively wide tables that become unreadable on GitHub.

## 10. Code Examples

Code examples should be:

- Minimal.
- Correct.
- Consistent with the current API.
- Clearly marked as illustrative when not production code.

Do not present pseudocode as compilable code.

Do not invent APIs merely to complete an example.

## 11. Versioning

When documenting version-specific behavior:

- State the applicable version.
- Avoid implying future functionality already exists.
- Distinguish planned, implemented, experimental, and deprecated features.

Use terms such as:

- Planned.
- Proposed.
- Experimental.
- Implemented.
- Verified.
- Deprecated.

Do not call an unimplemented architecture "production ready."

## 12. Status and Roadmap

Keep these concepts separate:

- Architecture — intended system structure.
- Roadmap — strategic direction.
- Milestone — engineering achievement.
- Plan — current work.
- Release — externally consumable version.
- Baseline — frozen reference point.

Do not mix roadmap aspirations with implemented capabilities.

## 13. ADRs

Architecture decisions should use ADRs when a decision:

- Changes a boundary.
- Introduces a major dependency.
- Selects a protocol/tool/platform.
- Creates a compatibility constraint.
- Has meaningful alternatives/trade-offs.

An ADR should normally capture:

1. Context.
2. Decision.
3. Alternatives considered.
4. Consequences.
5. Status.

Do not create ADRs for trivial implementation choices.

## 14. Technical Claims

Separate:

- Verified fact.
- Design decision.
- Proposal.
- Assumption.
- Future goal.

Avoid unsupported claims about:

- Performance.
- Safety.
- Market adoption.
- Silicon capability.
- AI capability.
- Determinism.
- Compatibility.

If a value is illustrative, label it as illustrative.

## 15. Safety and Security Language

Use precise language.

Prefer:

- "designed to support"
- "provides a mechanism for"
- "verified under the following conditions"

over unsupported claims such as:

- "completely safe"
- "secure by design" without evidence
- "deterministic" without a timing contract

Documentation must not overstate assurance.

## 16. External Ecosystem

When discussing ROS2, EtherCAT, Linux, PREEMPT_RT, Renode, FPGA vendors, AI frameworks, or other external technologies:

- State their role in the architecture.
- Avoid implying Kritva owns those technologies.
- Avoid unnecessary reimplementation.
- Identify integration boundaries.

ROS2 should be described as an ecosystem/integration layer, not as the definition of Kritva.

## 17. Change Discipline

When changing architecture or interfaces:

1. Update the canonical document.
2. Update affected detailed documentation.
3. Update APIs/specifications.
4. Update tests/verification documentation.
5. Add an ADR when appropriate.
6. Update release/changelog information when externally relevant.

Do not update only a README when a deeper architectural contract changed.

## 18. Documentation Style

Prefer:

- Clear headings.
- Short paragraphs.
- Precise terminology.
- Tables where useful.
- Diagrams for architecture.
- Examples for APIs.
- Explicit assumptions.

Avoid:

- Marketing language in engineering specifications.
- Repetition.
- Ambiguous adjectives.
- Excessive prose around simple facts.
- Unexplained acronyms.

## 19. AI-Agent Documentation Discipline

An AI agent must not silently:

- Change architectural intent.
- Invent requirements.
- Invent interfaces.
- Invent performance numbers.
- Upgrade status from planned to implemented.
- Resolve conflicting specifications by guessing.

If source material conflicts, identify the conflict and request or document the required decision.

## 20. Review Checklist

- [ ] Correct canonical terminology.
- [ ] Correct source-of-truth document identified.
- [ ] Architecture and implementation are not conflated.
- [ ] Claims are supported or clearly labeled.
- [ ] Requirements are traceable where applicable.
- [ ] APIs are documented as contracts.
- [ ] Diagrams match current architecture.
- [ ] Status/roadmap language is accurate.
- [ ] Safety/security claims are appropriately qualified.
- [ ] External dependencies are correctly positioned.
- [ ] Links/references are valid.
- [ ] No duplicate authoritative content introduced.
- [ ] Changed contracts have corresponding documentation updates.
