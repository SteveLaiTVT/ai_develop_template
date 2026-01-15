# AI-Assisted Development Template

A structured workflow template for building applications with AI pair programming (Claude Opus/Sonnet). Features a multi-session architecture where AI handles design, implementation, and review in separate contexts.

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
| **Discovery Framework** | A Session interviews users before designing |
| **State Tip System** | All sessions display workflow status on start |
| **Self-Test Workflow** | B Session runs servers and tests like a real developer |
| **agent-browser Integration** | Optional visual testing for frontend (vercel-labs/agent-browser) |
| **PR & Bugfix Mode** | B Session creates PR, then fixes issues until approved |
| **OpenSpec Integration** | Spec-driven development with detailed specifications |

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
│  (Opus 4.5)   │       │  (Sonnet 4.5) │       │  (Sonnet 4.5) │
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
| **A** | Opus 4.5 | Architect | Discovery interview, design, skeleton code with TODOs |
| **B** | Sonnet 4.5 | Implementer | Fill TODOs, self-test, create PR, bugfix mode |
| **C** | Sonnet 4.5 | Reviewer | Validate constraints, find issues, approve PR |

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
│  PHASE 3: Frontend Self-Test (agent-browser) [optional]          │
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

### 5. agent-browser Integration (Optional)

For projects with frontend (`project_analysis.has_frontend: true`), B Session can use [vercel-labs/agent-browser](https://github.com/vercel-labs/agent-browser) for visual testing:

- Navigate to pages
- Fill forms with mock data
- Click buttons and verify responses
- Test responsive design (mobile/tablet/desktop)

### 6. PR Creation & Bugfix Mode

After self-test passes, B Session:
1. **Creates PR** with detailed description and self-test results
2. **Enters bugfix mode** - waits for C Session review and user feedback
3. **Fixes issues** → Re-runs self-test → Pushes to PR
4. **Repeats** until PR is approved and merged

---

## Project Structure

```
your-project/
├── .claude/                      # AI Collaboration Hub
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

### 4. Start First Iteration

**A Session (Opus 4.5):**
1. Open new Claude chat
2. Paste content from `.claude/prompts/A_SESSION.md`
3. A Session displays state tip, runs discovery interview
4. Creates skeleton with TODOs

**B Session (Sonnet 4.5):**
1. Open new chat
2. Paste content from `.claude/prompts/B_SESSION.md`
3. B Session displays state tip, fills TODOs
4. Runs self-test (backend API, frontend visual)
5. Creates PR, enters bugfix mode

**C Session (Sonnet 4.5):**
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

## Tech Stack (Default)

| Layer | Technology |
|-------|------------|
| Backend | NestJS (TypeScript) |
| Frontend | React 18 + Tailwind |
| State | Zustand |
| API Client | TanStack Query |
| Mobile | Kotlin Compose / SwiftUI |
| Database | PostgreSQL + Prisma |
| Visual Testing | agent-browser (optional) |

*All choices are customizable in DESIGN_STATE.yaml*

---

## Documentation

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
