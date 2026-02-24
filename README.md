# AI-Assisted Development Template

A structured workflow template for building applications with multi-model AI pair programming. Features a multi-session architecture where AI handles design, implementation, and review in separate contexts.

**[中文文档](README_CN.md)**

## What This Is

A **project scaffold and workflow system** for developers who want to:

- Build full-stack applications with AI assistance
- Maintain code quality through structured AI collaboration
- Keep AI context focused and manageable
- Scale from solo development to team workflows
- Use spec-driven development with OpenSpec integration

## Key Features

| Feature | Description |
|---------|-------------|
| **Three-Session Model** | A (Architect) → B (Implementer) → C (Reviewer) |
| **Multi-Tool Agent Team** | Pre-configured roles for Claude, Codex, Copilot, Cursor, Windsurf |
| **Project Plan Management** | Global project plan with milestones, tasks, and tracking |
| **Discovery Framework** | A Session interviews users before designing |
| **State Tip System** | All sessions display workflow status on start |
| **Self-Test Workflow** | B Session runs servers and tests like a real developer |
| **MCP/agent Testing** | Prefer MCP/agent browser tools for frontend visual checks |
| **PR & Bugfix Mode** | B Session creates PR, then fixes issues until approved |
| **OpenSpec Integration** | Spec-driven development with detailed specifications |
| **Model-agnostic** | A/B/C roles are independent of any specific vendor |

## Architecture

```
┌─────────────────────────────────────────────────────────────────────┐
│                         DESIGN_STATE.yaml                           │
│                    (Single Source of Truth)                         │
│                                                                     │
│  Sections:                                                          │
│  • workflow_state    - Current phase & session status               │
│  • project_analysis  - Auto-detected frontend/backend/env           │
│  • discovery         - User interview results                       │
│  • architecture      - Tech stack decisions                         │
└───────────────────────────────┬─────────────────────────────────────┘
                                │
    ╔═══════════════════════════╧═══════════════════════════╗
    ║              PHASE 0: SESSION START                    ║
    ║  All sessions display STATE TIP on start               ║
    ╚═══════════════════════════╤═══════════════════════════╝
                                │
    ╔═══════════════════════════╧═══════════════════════════╗
    ║              PHASE 1: DISCOVERY (New Projects)         ║
    ║  A Session asks about: users, tech, vision, constraints║
    ╚═══════════════════════════╤═══════════════════════════╝
                                │
        ┌───────────────────────┼───────────────────────┐
        ▼                       ▼                       ▼
┌───────────────┐       ┌───────────────┐       ┌───────────────┐
│  A SESSION    │       │  B SESSION    │       │  C SESSION    │
│  (Reasoning)  │       │  (Execution) │       │  (Review)     │
│               │       │               │       │               │
│  • Discovery  │──────▶│  • Fill TODOs │──────▶│  • Review     │
│  • Design     │       │  • Self-Test  │       │  • Validate   │
│  • Skeleton   │◀──────│  • Create PR  │◀──────│  • Report     │
│               │       │  • Bugfix     │       │               │
└───────────────┘       └───────────────┘       └───────────────┘
```

### Three-Session Model

| Session | Model | Role | Responsibilities |
|---------|-------|------|------------------|
| **A** | Reasoning model | Architect | Discovery interview, design, skeleton code with TODOs, user-friendly error UX |
| **B** | Execution model | Implementer | Fill TODOs, self-test (incl. invalid inputs), create PR, bugfix mode |
| **C** | Review model | Reviewer | Validate constraints, find issues, approve PR |

---

## New Features

### 1. Discovery Framework (A Session)

Before designing, A Session conducts a **discovery interview** to understand:

```
📱 WHO WILL USE THIS APP?
   • Age range, tech level, accessibility needs

🔧 YOUR TECHNICAL BACKGROUND
   • Frontend/backend experience, existing resources

🎯 YOUR PRODUCT VISION
   • Core problem, MVP scope, design style

⚠️ CONSTRAINTS
   • Budget, compliance, integrations
```

Results are stored in `discovery` section of DESIGN_STATE.yaml.

### 2. State Tip System (All Sessions)

Every session displays **workflow status on start**:

```
╔══════════════════════════════════════════════════════════════════╗
║                    🚀 AI DEVELOPMENT WORKFLOW                     ║
╠══════════════════════════════════════════════════════════════════╣
║ Current Phase: implementation                                    ║
║ Project: My Awesome App                                          ║
╠══════════════════════════════════════════════════════════════════╣
║ SESSION STATUS                                                   ║
║ ┌────────────┬────────────┬────────────┐                        ║
║ │ A Session  │ B Session  │ C Session  │                        ║
║ │ waiting    │ ▶ ACTIVE   │ pending    │                        ║
║ └────────────┴────────────┴────────────┘                        ║
╠══════════════════════════════════════════════════════════════════╣
║ 👉 NEXT: B Session should implement and self-test                ║
╚══════════════════════════════════════════════════════════════════╝
```

### 3. B Session Self-Test Workflow

B Session works **like a real developer**:

```
┌─────────────────────────────────────────────────────────────────┐
│              COMPLETE B SESSION WORKFLOW                         │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  PHASE 0: Display State Tip                                      │
│     ↓                                                            │
│  PHASE 1: Environment Check (pause if env vars missing)          │
│     ↓                                                            │
│  Fill TODOs + Commit WIP                                         │
│     ↓                                                            │
│  PHASE 2: Backend Self-Test (run server, test APIs)              │
│     ↓                                                            │
│  PHASE 3: Frontend Self-Test (MCP/agent) [preferred]             │
│     ↓                                                            │
│  PHASE 4: Commit Working Code                                    │
│     ↓                                                            │
│  PHASE 5: Create Pull Request                                    │
│     ↓                                                            │
│  PHASE 6: Bugfix Mode ◄─────────────────────┐                    │
│     ↓    (wait for feedback)                │                    │
│  Fix Issue → Self-Test → Push ──────────────┘                    │
│     ↓                        (repeat until approved)             │
│  PR Approved & Merged → Task Complete                            │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### 4. Environment Pause Behavior

If B Session needs environment variables to run:

```
╔══════════════════════════════════════════════════════════════════╗
║  ⚠️  MISSING ENVIRONMENT VARIABLE - ACTION REQUIRED              ║
╠══════════════════════════════════════════════════════════════════╣
║                                                                  ║
║  Variable: DATABASE_URL                                          ║
║  Purpose:  PostgreSQL database connection                        ║
║  Example:  postgresql://user:password@localhost:5432/mydb        ║
║                                                                  ║
║  Please add this to your .env file.                              ║
║  Reply "ready" when done.                                        ║
║                                                                  ║
╚══════════════════════════════════════════════════════════════════╝

⏸️ PAUSED - Waiting for user to configure environment
```

### 5. MCP/Agent Testing (Required Preference)

**MCP (Model Context Protocol)** is a standard protocol that allows AI assistants to interact with external tools and services, including browser automation for testing.

For projects with frontend (`project_analysis.has_frontend: true`), B Session should prefer MCP-compatible browser tooling for visual testing:

- Navigate to pages
- Fill forms with mock data
- Click buttons and verify responses
- Test responsive design (mobile/tablet/desktop)

**Recommended tools:**
- **Playwright MCP Server** - Browser automation for testing
- Other MCP-compatible browser agents

**Fallback:** If MCP is unavailable, document the limitation and ask the user to verify manually

### 6. PR Creation & Bugfix Mode

After self-test passes, B Session:
1. **Creates PR** with detailed description and self-test results
2. **Enters bugfix mode** - waits for C Session review and user feedback
3. **Fixes issues** → Re-runs self-test → Pushes to PR
4. **Repeats** until PR is approved and merged

---

## Multi-Tool Agent Team Support

This template includes a **tool-agnostic agent team system** in `.ai/` that works with any AI coding assistant:

| AI Tool | Entry Point | Integration |
|---------|-------------|-------------|
| **Claude Code** | `CLAUDE.md` | Full workflow integration via `.claude/` + `.ai/` agents |
| **OpenAI Codex** | `AGENTS.md` | Reads `.ai/agents/` role definitions directly |
| **GitHub Copilot** | `.github/copilot-instructions.md` | References `.ai/agents/` for context |
| **Cursor** | `.cursorrules` | References `.ai/agents/` for guidance |
| **Windsurf** | `.windsurfrules` | References `.ai/agents/` for guidance |
| **Any other tool** | `.ai/README.md` | Reads `.ai/` directory directly |

### Agent Roles

| Role | File | Responsibility |
|------|------|----------------|
| **Coordinator** | `.ai/agents/coordinator.md` | Project management, workflow orchestration |
| **Architect** | `.ai/agents/architect.md` | Design, specs, skeleton code |
| **Developer** | `.ai/agents/developer.md` | Base implementation role |
| **↳ Frontend Developer** | `.ai/agents/frontend-developer.md` | UI, client-side, responsive, accessibility |
| **↳ Backend Developer** | `.ai/agents/backend-developer.md` | APIs, services, databases, security |
| **↳ Mobile Developer** | `.ai/agents/mobile-developer.md` | iOS, Android, cross-platform apps |
| **Reviewer** | `.ai/agents/reviewer.md` | Code review, quality validation |

### Skills Declaration

Each specialized developer declares which skills it requires from `.claude/skills/`:

| Skill | File | Required By |
|-------|------|-------------|
| **Git** | `.claude/skills/git_skills.md` | All developers (mandatory) |
| **Frontend** | `.claude/skills/frontend_skills.md` | Frontend Developer |
| **Backend** | `.claude/skills/backend_skills.md` | Backend Developer |
| **Testing** | `.claude/skills/testing_skills.md` | All developers (mandatory) |
| **Agent-Browser** | `.claude/skills/agent_browser_skills.md` | Frontend Developer |

### Project Plan

The global project plan (`.ai/project-plan.md`) serves as a project management hub:
- **Milestones** — High-level project goals and deadlines
- **Tasks** — Current iteration work items with assignments and status
- **Backlog** — Future tasks and ideas
- **Decisions Log** — Important decisions and rationale
- **Blockers** — Issues needing resolution

The Coordinator maintains the project plan. The Architect updates it during design. The Developer reads it to understand priorities.

---

## Project Structure

```
your-project/
├── .ai/                          # Tool-agnostic agent team (NEW)
│   ├── README.md                 # Agent team overview
│   ├── project-plan.md           # Global project plan
│   └── agents/                   # Agent role definitions
│       ├── coordinator.md        # Project Manager
│       ├── architect.md          # Architect
│       ├── developer.md          # Base Developer (references specializations)
│       ├── frontend-developer.md # Frontend specialist
│       ├── backend-developer.md  # Backend specialist
│       ├── mobile-developer.md   # Mobile specialist
│       └── reviewer.md           # Reviewer
│
├── .claude/                      # Claude-specific detailed workflow
│   ├── DESIGN_STATE.yaml         # Single source of truth
│   ├── prompts/                  # Session system prompts
│   │   ├── A_SESSION.md          # Discovery + Design
│   │   ├── B_SESSION.md          # Self-test + PR + Bugfix
│   │   └── C_SESSION.md          # Review
│   ├── templates/
│   │   ├── INDEX.md              # Template guide
│   │   ├── session_start.md      # State tip template (NEW)
│   │   ├── discovery_questions.md # Discovery interview (NEW)
│   │   ├── task_handoff.md       # A → B
│   │   ├── implementation_report.md
│   │   ├── review_report.md
│   │   └── ...
│   └── handoffs/                 # Active iteration docs
│
├── AGENTS.md                     # Entry point for Codex (NEW)
├── .github/copilot-instructions.md  # Entry point for Copilot (NEW)
├── .cursorrules                  # Entry point for Cursor (NEW)
├── .windsurfrules                # Entry point for Windsurf (NEW)
│
├── openspec/                     # Spec-driven development
│   ├── specs/                    # Completed specifications
│   └── changes/                  # Active proposals
│
├── apps/
│   ├── backend/                  # NestJS backend
│   ├── admin-web/                # Admin dashboard (React)
│   ├── user-web/                 # User web app (React)
│   └── mobile/                   # Android/iOS
│
└── packages/                     # Shared code
    ├── types/
    ├── utils/
    └── api-client/
```

---

## Quick Start

### 1. Clone and Setup

```bash
git clone https://github.com/yourusername/ai-develop-template.git my-project
cd my-project
pnpm install
```

### 2. Customize DESIGN_STATE.yaml

Edit `.claude/DESIGN_STATE.yaml` with your project details.

### 3. Install OpenSpec (Optional)

```bash
npm install -g @fission-ai/openspec@latest
```

### 4. Start with Any AI Tool

The agent team works with any AI coding assistant. Just open the project and the tool will read its entry point file:

| If you use... | It reads... | Then follows... |
|---------------|-------------|-----------------|
| Claude Code | `CLAUDE.md` | `.ai/agents/` + `.claude/prompts/` |
| OpenAI Codex | `AGENTS.md` | `.ai/agents/` |
| GitHub Copilot | `.github/copilot-instructions.md` | `.ai/agents/` |
| Cursor | `.cursorrules` | `.ai/agents/` |
| Windsurf | `.windsurfrules` | `.ai/agents/` |

Or manually start with the three-session model:

**A Session (Reasoning model):**
1. Open a new AI assistant session
2. Paste content from `.claude/prompts/A_SESSION.md`
3. A Session displays state tip, runs discovery interview
4. Creates skeleton with TODOs

**B Session (Execution model):**
1. Open new chat
2. Paste content from `.claude/prompts/B_SESSION.md`
3. B Session displays state tip, fills TODOs
4. Runs self-test (backend API, frontend visual)
5. Creates PR, enters bugfix mode

**C Session (Review model):**
1. Open new chat
2. Paste content from `.claude/prompts/C_SESSION.md`
3. Reviews code against constraints
4. Approves or requests changes

---

## Templates Included

| Template | Purpose |
|----------|---------|
| `session_start.md` | State tip display for all sessions (NEW) |
| `discovery_questions.md` | Discovery interview questions (NEW) |
| `task_handoff.md` | A → B: Assign tasks with skeleton code |
| `implementation_report.md` | B → C: Report completed work |
| `review_report.md` | C → A: Code review results |
| `question_feedback.md` | B → A: Questions and blockers |
| `bug_report.md` | Human → A: Testing feedback |
| `openspec_integration.md` | Spec-driven development guide |

---

## Core Principles

### 1. Single Source of Truth
Only `DESIGN_STATE.yaml` defines the project.

### 2. Role Separation
- A Session: Decisions only, no implementation
- B Session: Implementation only, no design changes
- C Session: Review only, no code modifications

### 3. Human-Like Workflow
B Session works like a real developer:
- Write code → Run server → Test → Fix → Commit working code

### 4. Environment Safety
B Session pauses and asks user when environment variables are missing.

### 5. Iterative Bugfix
Fix → Self-test → Push → Repeat until approved.

---

## Technology Selection & Maintenance

This template does not lock in any frontend/backend/mobile stack. Instead:

1. **Respect user preference** when specified.
2. **Recommend (don’t enforce)** 1–2 viable stacks if the user asks for guidance.
3. **Use latest stable/LTS versions** and follow official best practices.
4. **Maintain AGENTS.md/AGENT.md** in module directories so other models can collaborate cleanly.

---

## Deployment Expectations

When a feature spans frontend/backend:

1. **Minimum**: Provide Docker deployment guidance (Dockerfile/compose).
2. **Preferred**: Provide Vercel deployment guidance when a web frontend exists.
3. **Environment Variables**: Document required env vars for each runtime (local, Docker, Vercel).
4. **Code Review**: Verify env var list and deployment notes are present before merge.

---

## Tech Stack (Example)

| Layer | Technology |
|-------|------------|
| Backend | NestJS (TypeScript) |
| Frontend | React 18 + Tailwind |
| State | Zustand |
| API Client | TanStack Query |
| Mobile | Kotlin Compose / SwiftUI |
| Database | PostgreSQL + Prisma |
| Visual Testing | MCP/agent browser tools (preferred) |

*All choices are customizable in DESIGN_STATE.yaml.*

---

## Documentation

- [Agent Team Overview](.ai/README.md) - Multi-tool agent system
- [Project Plan](.ai/project-plan.md) - Global project management
- [Templates INDEX](.claude/templates/INDEX.md) - All templates explained
- [Session Start](.claude/templates/session_start.md) - State tip template
- [Discovery Questions](.claude/templates/discovery_questions.md) - Interview framework
- [OpenSpec Integration](.claude/templates/openspec_integration.md) - Spec-driven development

---

## Contributing

Contributions welcome! Please read the templates to understand the workflow before submitting PRs.

## License

MIT License - feel free to use for personal or commercial projects.

---

**Built for developers who want AI to enhance, not replace, their workflow.**
