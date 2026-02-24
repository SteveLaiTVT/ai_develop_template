# GitHub Copilot Instructions

## Agent Team System

This project uses a pre-configured AI agent team for development. Read the role definitions in `.ai/agents/` to understand the workflow.

### Agent Roles

- **Coordinator** (`.ai/agents/coordinator.md`) — Manages project plan and workflow
- **Architect** (`.ai/agents/architect.md`) — Designs architecture and creates specs
- **Developer** (`.ai/agents/developer.md`) — Implements code per specs, self-tests
- **Reviewer** (`.ai/agents/reviewer.md`) — Reviews code against specs and constraints

### Key Rules

1. **Read your role first** — Check `.ai/agents/` for your responsibilities and permissions
2. **Spec before code** — Features need an approved spec in `openspec/changes/` before implementation
3. **Follow constraints** — Check `.claude/DESIGN_STATE.yaml` for architecture decisions and code constraints
4. **Self-test** — Always test code before committing (run servers, test APIs, verify builds)
5. **Project plan** — Check `.ai/project-plan.md` for current priorities and task status

### Project State

- **Project plan**: `.ai/project-plan.md`
- **Design state**: `.claude/DESIGN_STATE.yaml`
- **Active specs**: `openspec/changes/`
- **Completed specs**: `openspec/specs/`

### Workflow

```
User Request
  → Coordinator: Updates project plan
  → Architect: Creates spec
  → User: Approves spec
  → Developer: Implements and tests
  → Reviewer: Validates
  → Coordinator: Archives and updates plan
```

### Commit Messages

Use Conventional Commits: `<type>(<scope>): <subject>`
Types: feat, fix, docs, style, refactor, test, chore, perf
