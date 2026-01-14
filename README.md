# AI-Assisted Development Template

A structured workflow template for building applications with AI pair programming (Claude Opus/Sonnet). Features a
multi-session architecture where AI handles design, implementation, and review in separate contexts.

## 🎯 What This Is

A **project scaffold and workflow system** for developers who want to:

- Build full-stack applications with AI assistance
- Maintain code quality through structured AI collaboration
- Keep AI context focused and manageable
- Scale from solo development to team workflows

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    DESIGN_STATE.yaml                        │
│                 (Single Source of Truth)                    │
└───────────────────────────┬─────────────────────────────────┘
                            │
        ┌───────────────────┼───────────────────┐
        ▼                   ▼                   ▼
┌───────────────┐   ┌───────────────┐   ┌───────────────┐
│  A SESSION    │   │  B SESSION    │   │  C SESSION    │
│  (Opus 4.5)   │   │  (Sonnet 4.5) │   │  (Sonnet 4.5) │
│               │   │               │   │               │
│  • Analyze    │──▶│  • Implement  │──▶│  • Review     │
│  • Design     │   │  • Fill TODOs │   │  • Validate   │
│  • Decide     │◀──│  • Test       │◀──│  • Report     │
└───────────────┘   └───────────────┘   └───────────────┘
```

### Three-Session Model

| Session | Model      | Role        | Responsibilities                            |
|---------|------------|-------------|---------------------------------------------|
| **A**   | Opus 4.5   | Architect   | Design, decisions, skeleton code with TODOs |
| **B**   | Sonnet 4.5 | Implementer | Fill TODOs, write code, tests               |
| **C**   | Sonnet 4.5 | Reviewer    | Validate constraints, find issues           |

### Key Innovation: Skeleton + TODO Pattern

A Session provides **structure**, B Session provides **implementation**:

```typescript
// A Session writes:
export class AuthService {
    /**
     * TODO(B): Implement user registration
     * Requirements:
     * - Validate email uniqueness
     * - Hash password with bcrypt (cost 12)
     * - Generate JWT tokens
     * Acceptance: POST /api/v1/auth/register works
     * Constraints: Under 50 lines
     */
    async register(dto: RegisterDto): Promise<AuthResult> {
        throw new Error('Not implemented - TODO(B)');
    }
}

// B Session fills in the implementation
```

## 📁 Project Structure

```
your-project/
├── .claude/                      # AI Collaboration Hub
│   ├── DESIGN_STATE.yaml         # Single source of truth
│   ├── FILE_STRUCTURE.md         # Complete location guide
│   ├── prompts/                  # Session system prompts
│   │   ├── A_SESSION.md
│   │   ├── B_SESSION.md
│   │   └── C_SESSION.md
│   ├── templates/                # All workflow templates
│   │   ├── INDEX.md              # Template guide
│   │   ├── task_handoff.md       # A → B
│   │   ├── implementation_report.md
│   │   ├── review_report.md
│   │   └── ...
│   └── handoffs/                 # Active iteration docs
│
├── packages/                     # Shared code (Monorepo)
│   ├── types/                    # TypeScript types
│   ├── utils/                    # Utilities
│   └── api-client/               # API client
│
├── apps/
│   ├── backend/                  # NestJS backend
│   ├── admin-web/                # Admin dashboard (React)
│   ├── user-web/                 # User web app (React)
│   └── mobile/
│       ├── android/              # Kotlin + Compose
│       └── ios/                  # Swift + SwiftUI (planned)
│
└── infrastructure/               # Docker, K8s configs
```

## 🚀 Quick Start

### 1. Clone and Setup

```bash
git clone https://github.com/yourusername/ai-develop-template.git my-project
cd my-project
pnpm install
```

### 2. Customize DESIGN_STATE.yaml

Edit `.claude/DESIGN_STATE.yaml` with your project details:

```yaml
meta:
  project_name: "My Awesome App"
  description: "What your app does"

architecture:
  backend:
    framework: "NestJS"  # or FastAPI, Express...
  # ... customize tech stack
```

### 3. Start First Iteration

1. Open new Claude chat (Opus 4.5)
2. Paste content from `.claude/prompts/A_SESSION.md`
3. Describe your first feature
4. A Session creates skeleton with TODOs
5. Open new chat (Sonnet 4.5) for B Session
6. B Session implements TODOs
7. Open new chat (Sonnet 4.5) for C Session
8. C Session reviews code

## 📋 Templates Included

| Template                   | Purpose                                  |
|----------------------------|------------------------------------------|
| `task_handoff.md`          | A → B: Assign tasks with skeleton code   |
| `implementation_report.md` | B → C: Report completed work             |
| `review_report.md`         | C → A: Code review results               |
| `question_feedback.md`     | B → A: Questions and blockers            |
| `bug_report.md`            | Human → A: Testing feedback              |
| `iteration_summary.md`     | Version release notes (supports Chinese) |
| `external_review.md`       | External AI (Codex/Gemini) audit         |
| `git_workflow.md`          | Git conventions and branching            |

## 🔑 Core Principles

### 1. Single Source of Truth

Only `DESIGN_STATE.yaml` defines the project. All sessions reference it.

### 2. Role Separation

- A Session: Decisions only, no implementation
- B Session: Implementation only, no design changes
- C Session: Review only, no code modifications

### 3. Small Iterations

- 1 core decision per design change
- 1-2 modules per task
- Frequent commits and reviews

### 4. Constraint-First

Define rules before coding. Constraints must be specific and verifiable.

## 🛠️ Tech Stack (Default)

| Layer      | Technology               |
|------------|--------------------------|
| Backend    | NestJS (TypeScript)      |
| Frontend   | React 18 + Tailwind      |
| State      | Zustand                  |
| API Client | TanStack Query           |
| Mobile     | Kotlin Compose / SwiftUI |
| Database   | PostgreSQL + Prisma      |
| Cache      | Redis                    |

*All choices are customizable in DESIGN_STATE.yaml*

## 📖 Documentation

- [FILE_STRUCTURE.md](.claude/FILE_STRUCTURE.md) - Complete file location guide
- [Templates INDEX](.claude/templates/INDEX.md) - All templates explained
- [Git Workflow](.claude/templates/git_workflow.md) - Version control conventions

## 🤝 Contributing

Contributions welcome! Please read the templates to understand the workflow before submitting PRs.

## 📄 License

MIT License - feel free to use for personal or commercial projects.

---

**Built for developers who want AI to enhance, not replace, their workflow.**