# Changelog

All notable changes to Kritva are documented in this file.

The format is based on the principles of
Keep a Changelog, with Kritva-specific additions for
architecture, hardware, verification, and breaking changes.

## [Unreleased]

### Added

#### Platform Architecture

- Established Kritva as an Open Robotic Computing Platform.
- Added system-level architecture covering KritvaOS and the Kritva
  Hardware Platform.
- Defined Nexus / Edge / Subnode / Endpoint computing hierarchy.
- Added repository architecture definition.
- Added project lifecycle structure for roadmap, milestones,
  tracking, releases, plans, and baselines.

#### Development Environment

- Established the Kritva v0.1 development toolchain baseline.
- Added centralized toolchain definition through
  `toolchain/VERSIONS.yaml`.
- Added containerized KritvaOS development environment using
  `kritvaos-dev:0.1`.
- Added development-container configuration under `.devcontainer/`.
- Added automated toolchain validation through
  `scripts/check_toolchain.py`.
- Added Makefile targets for toolchain validation and CI execution.
- Added CI toolchain profile for reproducible validation.

#### Toolchain Profiles

- Added `default` profile for core development.
- Added `ci` profile for continuous integration.
- Added `simulation` profile for Renode and Verilator workflows.
- Added `soc` profile for FPGA and SoC development tools.
- Added `robotics` profile for ROS2, ros2_control, micro-ROS,
  and EtherCAT environments.
- Added `full` profile combining SoC, robotics, and AI toolchains.

### Changed

#### Platform Positioning

- Expanded Kritva positioning from a humanoid robotics operating
  system to an open robotic computing platform.
- Defined KritvaOS as the software platform within the broader
  Kritva architecture.
- Clarified Kritva Core as a platform foundation rather than a
  container for all runtime, driver, and hardware functionality.

#### Repository

- Defined a modular monorepo as the initial Kritva repository
  strategy.
- Established separation between architecture documentation,
  source implementation, project management, and CI/development
  infrastructure.

### Architecture

- Defined the common Kritva computing stack:

  `Application → SDK → Skill → Mind / Motion → Sense → Core →
  Hardware Abstraction → Nexus → Edge → Subnode → Endpoint`

- Defined Nexus and Edge as compute platform families rather than
  single fixed SoCs.
- Established the boundary between KritvaOS software and the
  Kritva Hardware Platform.
- Established the initial repository architecture for future
  software, hardware, simulation, FPGA, and silicon development.
- Defined the intended software-to-hardware development path from
  simulation through physical robotic systems.

### Toolchain

- Established the v0.1 validated core development environment:
  - CMake 3.28.3
  - Ninja 1.11.1
  - Clang 18.1.3
  - clang-format 18.1.3
  - Python 3.12.3
  - Git 2.43.0
- Validated `clang-tidy` 18.1.3 as part of the CI profile.
- Established minimum and recommended tool versions through
  `toolchain/VERSIONS.yaml`.
- Established versioned development-container identity:
  `kritvaos-dev:0.1`.
- Established the principle that builds, tests, formatting,
  simulation, and releases should use the Kritva-defined toolchain.

### Verification

- Established the intended verification path:

  `Unit → Contract → Integration → Simulation → Renode → FPGA →
  HIL → Silicon → Robot`

- Added machine-readable JSON output for toolchain validation.
- Added strict toolchain validation for CI.
- Validated the default toolchain profile successfully.
- Validated the CI toolchain profile successfully.
- Validated `make ci` successfully.
- Confirmed zero toolchain warnings and failures for the validated
  core and CI profiles.

### Breaking Changes

None.

### Deprecated

None.

### Fixed

- Improved tool version detection so Clang-family tools report
  normalized installed versions rather than truncated command output.
- Corrected Makefile profile invocation to use
  `TOOLCHAIN_PROFILE=<profile>`.

### Removed

None.

### Security

No security changes.

---

## [0.1.0] - TBD

### Added

- Initial Kritva Core Foundation.
- Initial hardware abstraction.
- Initial Nexus / Edge interfaces.
- Initial simulation and verification infrastructure.
- Initial SDK interfaces.

### Architecture

- First validated Kritva vertical slice.

### Verification

- Initial unit and contract test coverage.
- Initial system integration validation.

### Breaking Changes

Document breaking API or architecture changes here.

### Security

Document security-related changes here.

---

## Release Format

Each release should document changes under:

- Added
- Changed
- Architecture
- Verification
- Fixed
- Deprecated
- Removed
- Breaking Changes
- Security

Hardware-related releases may additionally include:

- Nexus
- Edge
- FPGA
- Firmware
- Silicon

[Unreleased]: TBD
