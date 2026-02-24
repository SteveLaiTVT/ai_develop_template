# GitHub Copilot Instructions

## Agent Team System

This project uses a pre-configured AI agent team for development. Read the role definitions in `.ai/agents/` to understand the workflow.

### Agent Roles

- **Coordinator** (`.ai/agents/coordinator.md`) — Manages project plan and workflow
- **Architect** (`.ai/agents/architect.md`) — Designs architecture and creates specs
- **Developer** (`.ai/agents/developer.md`) — Base implementation role with specializations:
  - **Frontend Developer** (`.ai/agents/frontend-developer.md`) — UI, client-side, accessibility
  - **Backend Developer** (`.ai/agents/backend-developer.md`) — APIs, services, databases
  - **Mobile Developer** (`.ai/agents/mobile-developer.md`) — iOS, Android, cross-platform
- **Reviewer** (`.ai/agents/reviewer.md`) — Reviews code against specs and constraints

### Skills

Each developer role declares required skills from `.claude/skills/`:
- **All developers**: `git_skills.md` (mandatory), `testing_skills.md` (mandatory)
- **Frontend Developer**: + `frontend_skills.md`, `agent_browser_skills.md`
- **Backend Developer**: + `backend_skills.md`

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
