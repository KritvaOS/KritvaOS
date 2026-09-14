# Kritva Toolchain Foundation Proposal

This bundle proposes the initial reproducible toolchain infrastructure:

- `toolchain/VERSIONS.yaml`
- `toolchain/README.md`
- `scripts/check_toolchain.py`
- `.devcontainer/Dockerfile`
- `.devcontainer/devcontainer.json`
- `toolchain/docker/Dockerfile`
- `.github/workflows/ci.yml`

The intended policy is that developers may choose their editor, while build,
test, formatting, simulation, and release environments are controlled by the
Kritva toolchain definition.

## Important implementation note

The CI image reference is intentionally versioned:

`ghcr.io/kritvaos/kritva-dev:0.1`

Before enabling this workflow in the real repository, publish that image and
make the `.devcontainer` build consume the same canonical image/build
definition. For release-grade reproducibility, freeze exact package versions
and eventually pin the CI image by digest.

# Kritva model
Developer Machine
────────────────────────────────────────────
Host OS
  │
  ├── Git
  ├── Docker
  └── Make
        │
        │ make check-toolchain
        │ make build
        ▼
┌───────────────────────────────────────────┐
│        Kritva Development Container       │
│                                           │
│  Ubuntu 24.04                             │
│  ├── Clang 20                             │
│  ├── CMake 3.31                           │
│  ├── Ninja 1.12                           │
│  ├── Python 3.12                          │
│  ├── clang-format                         │
│  ├── clang-tidy                           │
│  ├── Git                                  │
│  ├── Renode                               │
│  ├── Verilator                            │
│  └── future tools                         │
│                                           │
│  /workspaces/KritvaOS                     │
│          ▲                                │
└──────────┼────────────────────────────────┘
           │
       Kritva source
       mounted from host

# Tool outside Docker
| Tool         |        Host? |                      Container? |
| ------------ | -----------: | ------------------------------: |
| Git          |            ✅ |                               ✅ |
| Docker       |            ✅ |                               — |
| Make         |            ✅ |                        optional |
| CMake        |            ❌ |                               ✅ |
| Ninja        |            ❌ |                               ✅ |
| Clang        |            ❌ |                               ✅ |
| Python       |            ❌ |                               ✅ |
| clang-format |            ❌ |                               ✅ |
| clang-tidy   |            ❌ |                               ✅ |
| Renode       |            ❌ |                               ✅ |
| Verilator    |            ❌ |                               ✅ |
| ROS 2        |            ❌ |                               ✅ |
| AI tools     |            ❌ |                               ✅ |
| FPGA tools   | special case | usually host/container strategy |

# Tool Dependency 

                 toolchain/VERSIONS.yaml
                          │
                          │ defines
                          ▼
                 .devcontainer/Dockerfile
                          │
                          │ provides
                          ▼
                  Kritva Dev Environment
                          │
                          ├── CMake
                          ├── Ninja
                          ├── Clang
                          ├── clang-format
                          ├── clang-tidy
                          ├── Python
                          ├── Git
                          └── build/debug tools
                          │
                          ▼
                  scripts/check_toolchain.py
                          │
                          ▼
                       Makefile
                          │
              ┌───────────┼───────────┐
              ▼           ▼           ▼
            build        test       format

