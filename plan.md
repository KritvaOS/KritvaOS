####  14-Sep-2026 ###################################

1. REPOSITORY_ARCHITECTURE.md
2. ARCHITECTURE.md
3. Top-level folder tree
4. Decide initial submodule boundaries
5. Create independent repositories where justified
6. Add submodules to KritvaOS
7. Build the first vertical slice


KritvaOS/
├── AGENTS.md
│
├── .github/
│   ├── copilot-instructions.md
│   │
│   ├── instructions/
│   │   ├── core.instructions.md
│   │   ├── cpp.instructions.md
│   │   ├── soc.instructions.md
│   │   ├── rtl.instructions.md
│   │   ├── robotics.instructions.md
│   │   ├── simulation.instructions.md
│   │   └── documentation.instructions.md
│   │
│   ├── agents/
│   │   ├── kritva-architect.agent.md
│   │   ├── kritva-core.agent.md
│   │   ├── kritva-soc.agent.md
│   │   ├── kritva-rtl-reviewer.agent.md
│   │   ├── kritva-verification.agent.md
│   │   └── kritva-code-reviewer.agent.md
│   │
│   └── workflows/
│       └── ci.yml
│
├── ARCHITECTURE.md
├── core/
├── sense/
├── mind/
├── motion/
├── skill/
├── sim/
├── sdk/
├── hardware/
├── soc/
├── robots/
├── tests/
├── toolchain/
└── project/
