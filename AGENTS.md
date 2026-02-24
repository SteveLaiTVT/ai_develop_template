# AI Agent Team — Instructions for AI Coding Assistants

> This file is read by OpenAI Codex and other AI tools that look for `AGENTS.md`.
> For Claude Code, see `CLAUDE.md`. For other tools, see `.ai/README.md`.

## Agent Team System

This project uses a **pre-configured agent team** for AI-assisted development. Before writing any code, read the agent role definitions in `.ai/agents/` to understand your responsibilities.

### Agent Roles

| Role | Definition | Responsibility |
|------|-----------|----------------|
| **Coordinator** | [`.ai/agents/coordinator.md`](.ai/agents/coordinator.md) | Orchestrate workflow, manage project plan, enforce spec-first |
| **Architect** | [`.ai/agents/architect.md`](.ai/agents/architect.md) | Design, plan, create specs, skeleton code |
| **Developer** | [`.ai/agents/developer.md`](.ai/agents/developer.md) | Implement code per specs, self-test, create PRs |
| **Reviewer** | [`.ai/agents/reviewer.md`](.ai/agents/reviewer.md) | Review code against specs, validate quality |

### Getting Started

1. **Read your role** — Open the appropriate file from `.ai/agents/` based on what you're asked to do
2. **Read the project plan** — Check `.ai/project-plan.md` for current priorities and task status
3. **Read the project state** — Check `.claude/DESIGN_STATE.yaml` for architecture, constraints, and workflow state
4. **Read active specs** — Check `openspec/changes/` for feature specifications being worked on

### Key Rules

1. **Spec before code** — No implementation without an approved spec in `openspec/changes/`
2. **Follow your role** — Each agent has specific permissions and boundaries
3. **Self-test before commit** — The Developer must test code before creating PRs
4. **Project plan tracks everything** — The `.ai/project-plan.md` is the global task tracker

### Workflow

```
User Request
  → Coordinator: Updates project plan
  → Architect: Creates spec in openspec/changes/
  → User: Reviews and approves spec
  → Developer: Implements per spec, self-tests
  → Reviewer: Validates against spec
  → Coordinator: Archives spec, updates plan
```

### Project Structure

```
.ai/                     # Tool-agnostic agent team definitions
├── README.md            # Agent team overview
├── project-plan.md      # Global project plan
└── agents/              # Agent role definitions

.claude/                 # Detailed workflow state and templates
├── DESIGN_STATE.yaml    # Single source of truth (project state)
├── prompts/             # Detailed session prompts
├── templates/           # Workflow templates
├── skills/              # Agent skill specifications
└── handoffs/            # Task handoff documents

openspec/                # Spec-driven development
├── specs/               # Completed specifications
└── changes/             # Active proposals (specs in progress)
```

### For More Details

- **Agent team system**: [`.ai/README.md`](.ai/README.md)
- **Detailed workflow**: [`.claude/prompts/COORDINATOR.md`](.claude/prompts/COORDINATOR.md)
- **Project state**: [`.claude/DESIGN_STATE.yaml`](.claude/DESIGN_STATE.yaml)
- **OpenSpec integration**: [`openspec/AGENTS.md`](openspec/AGENTS.md)
