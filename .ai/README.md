# AI Agent Team System

> **Tool-agnostic agent role definitions** — works with Claude Code, OpenAI Codex, GitHub Copilot, Cursor, Windsurf, and any AI coding assistant.

## Overview

This directory defines **pre-configured agent roles** that any AI tool can read and follow. Each role has specific responsibilities, permissions, and workflow rules. The agent team operates like a software development team:

```
┌─────────────────────────────────────────────────────────────────┐
│                    AI AGENT TEAM WORKFLOW                         │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│   👔 Coordinator (Project Manager)                               │
│   │  Orchestrates workflow, manages project plan                 │
│   │                                                              │
│   ├──► 🏗️ Architect (Design & Planning)                          │
│   │    Creates specs, designs architecture, decomposes tasks     │
│   │                                                              │
│   ├──► 💻 Developer (Implementation)                             │
│   │    Implements code per specs, self-tests, creates PRs        │
│   │                                                              │
│   └──► 🔍 Reviewer (Quality Assurance)                           │
│        Reviews code against specs, validates constraints         │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

## Agent Roles

| Role | File | Responsibility |
|------|------|----------------|
| **Coordinator** | [`agents/coordinator.md`](agents/coordinator.md) | Orchestrates workflow, manages the global project plan, enforces spec-first development |
| **Architect** | [`agents/architect.md`](agents/architect.md) | Discovery, design, architecture decisions, creates specs and skeleton code |
| **Developer** | [`agents/developer.md`](agents/developer.md) | Base implementation role — see specialized roles below |
| **↳ Frontend Developer** | [`agents/frontend-developer.md`](agents/frontend-developer.md) | UI components, client-side logic, responsive design, accessibility |
| **↳ Backend Developer** | [`agents/backend-developer.md`](agents/backend-developer.md) | APIs, services, databases, security, server-side logic |
| **↳ Mobile Developer** | [`agents/mobile-developer.md`](agents/mobile-developer.md) | iOS, Android, cross-platform mobile apps |
| **Reviewer** | [`agents/reviewer.md`](agents/reviewer.md) | Reviews code against specs and constraints, validates quality |

### Choosing a Developer Role

The Coordinator or Architect assigns tasks to the appropriate specialized developer:

| Project Type | Developer Role | Skills Applied |
|--------------|----------------|----------------|
| Web Frontend | Frontend Developer | git, frontend, testing, agent-browser |
| Web Backend / API | Backend Developer | git, backend, testing |
| Full-Stack Web | Frontend + Backend Developer | git, frontend, backend, testing, agent-browser |
| Mobile App | Mobile Developer | git, testing |

## Skills System

Each agent role declares which **skills** it requires. Skills are detailed specifications that define coding standards and best practices:

| Skill | File | Used By |
|-------|------|---------|
| **Git Version Control** | `.claude/skills/git_skills.md` | All developers (MANDATORY) |
| **Frontend Development** | `.claude/skills/frontend_skills.md` | Frontend Developer |
| **Backend Development** | `.claude/skills/backend_skills.md` | Backend Developer |
| **Testing** | `.claude/skills/testing_skills.md` | All developers (MANDATORY) |
| **Agent-Browser** | `.claude/skills/agent_browser_skills.md` | Frontend Developer |

See [`.claude/skills/INDEX.md`](.claude/../.claude/skills/INDEX.md) for the full skill catalog and guidelines on adding new skills.

## How It Works

### 1. Read Your Role

When an AI tool opens this project, it should:
1. Read this file (`.ai/README.md`) for an overview
2. Read the appropriate agent role file from `.ai/agents/`
3. Read the project state from `.claude/DESIGN_STATE.yaml`
4. Follow the role's workflow rules

### 2. Follow the Workflow

```
User Request
    │
    ▼
Coordinator: Creates/updates project plan
    │
    ▼
Architect: Creates spec (openspec/changes/<feature>/)
    │
    ▼
User: Reviews and approves spec
    │
    ▼
Developer: Implements per spec, self-tests
    │
    ▼
Reviewer: Validates against spec
    │
    ▼
Coordinator: Archives spec, updates plan
```

### 3. Key Principle: Spec Before Code

**No implementation without an approved specification.** This ensures:
- Clear requirements before coding starts
- A review baseline for the reviewer
- A shared understanding between all agents

## Project Plan

The [`project-plan.md`](project-plan.md) file serves as the global project management document. It tracks:
- High-level project goals and milestones
- Current iteration tasks
- Task status and assignments
- Dependencies and blockers

The Coordinator maintains this file. The Architect updates it during design. The Developer reads it to understand priorities.

## Supported AI Tools

This agent team system is designed to work with any AI coding tool:

| AI Tool | Entry Point | How It Reads Agents |
|---------|-------------|---------------------|
| **Claude Code** | `CLAUDE.md` | Reads `.ai/agents/` roles, uses `.claude/` for detailed workflow |
| **OpenAI Codex** | `AGENTS.md` | Reads `.ai/agents/` roles directly |
| **GitHub Copilot** | `.github/copilot-instructions.md` | Reads `.ai/agents/` roles for context |
| **Cursor** | `.cursorrules` | Reads `.ai/agents/` roles for guidance |
| **Windsurf** | `.windsurfrules` | Reads `.ai/agents/` roles for guidance |
| **Other tools** | `.ai/README.md` | Any tool can read this directory directly |

## Directory Structure

```
.ai/
├── README.md              # This file — overview and usage guide
├── project-plan.md        # Global project plan (project management)
└── agents/
    ├── coordinator.md     # Project Manager role
    ├── architect.md       # Architect/Designer role
    ├── developer.md       # Base Developer role (references specializations)
    ├── frontend-developer.md  # Frontend specialist (skills: frontend, testing, agent-browser)
    ├── backend-developer.md   # Backend specialist (skills: backend, testing)
    ├── mobile-developer.md    # Mobile specialist (skills: testing)
    └── reviewer.md        # Reviewer/QA role
```

## Integration with Existing Workflow

The `.ai/` directory works alongside:
- **`.claude/`** — Detailed Claude-specific workflow state and templates
- **`openspec/`** — Spec-driven development specs and proposals
- **`.claude/DESIGN_STATE.yaml`** — Single source of truth for project state

The `.ai/` agent definitions are the **generic, tool-agnostic layer**. Tool-specific configurations (like `.claude/prompts/`) provide deeper integration for tools that support it.
