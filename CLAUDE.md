# AI Development Template - Assistant Instructions

This file is automatically read by the AI assistant when opening this project.

## Agent Team System

This project uses a **pre-configured agent team** that works across multiple AI tools. The shared agent role definitions are in `.ai/agents/`:

- **Coordinator** ([`.ai/agents/coordinator.md`](.ai/agents/coordinator.md)) — Manages the project plan and orchestrates workflow
- **Architect** ([`.ai/agents/architect.md`](.ai/agents/architect.md)) — Designs architecture, creates specs, provides skeleton code
- **Developer** ([`.ai/agents/developer.md`](.ai/agents/developer.md)) — Base implementation role with specializations:
  - **Frontend Developer** ([`.ai/agents/frontend-developer.md`](.ai/agents/frontend-developer.md)) — UI, client-side logic, accessibility
  - **Backend Developer** ([`.ai/agents/backend-developer.md`](.ai/agents/backend-developer.md)) — APIs, services, databases, security
  - **Mobile Developer** ([`.ai/agents/mobile-developer.md`](.ai/agents/mobile-developer.md)) — iOS, Android, cross-platform apps
- **Reviewer** ([`.ai/agents/reviewer.md`](.ai/agents/reviewer.md)) — Reviews code against specs and constraints

For an overview, see [`.ai/README.md`](.ai/README.md). For the global project plan, see [`.ai/project-plan.md`](.ai/project-plan.md).

> **Note**: The `.ai/` directory contains tool-agnostic agent definitions shared across Claude Code, Codex, Copilot, Cursor, and Windsurf. The `.claude/` directory below provides Claude-specific detailed workflow integration.

## Quick Start

When you open this project, follow these steps:

### Step 1: Check Initialization Status

Read `.claude/DESIGN_STATE.yaml` and check:

```yaml
initialization:
  is_template: true    # If true → Run initialization flow
  initialized: false   # If false → Initialization not complete
```

### Step 2: If `is_template: true` → Run Initialization

**Display welcome banner and run onboarding:**

```
╔══════════════════════════════════════════════════════════════════╗
║     🎉 WELCOME TO THE AI DEVELOPMENT TEMPLATE                    ║
╠══════════════════════════════════════════════════════════════════╣
║  I'm your AI development partner.                                ║
║  Let's set up your project! I'll ask a few questions first.      ║
╚══════════════════════════════════════════════════════════════════╝
```

**Ask these questions:**

1. **Project Name**: What should we call this project?
2. **Description**: Briefly describe what you want to build (1-2 sentences)
3. **Project Type**: web-app | mobile-app | api-service | full-stack | other
4. **Tech Stack**: Your preference or "recommend" for suggestions
5. **Tech Best Practices** (Recommended): Apply industry best practices for your stack?
   - "yes" - Apply all relevant best practices (code patterns, security, testing)
   - "selective" - Choose which practices to apply
   - "no" - Skip, use minimal patterns
6. **Git Remote** (OPTIONAL): Your repository URL or "skip" for local-only git
7. **Model Selection** (OPTIONAL): Which model for each session?
   - Default: A=Reasoning, B=Fast, C=Fast
   - Custom: A/B/C set per user's preference

**Then execute initialization:**

1. Update `DESIGN_STATE.yaml` with answers
2. Apply tech best practices if user opted in (update `tech_best_practices` section)
3. Clear template `.git`: `rm -rf .git`
4. Create `.gitignore` file
5. Initialize new repo: `git init && git branch -M main`
6. Add remote (OPTIONAL - only if user provided URL)
7. Make initial commit with user's project intent
8. Set `initialization.initialized: true` and `is_template: false`
9. Transition to discovery phase

**Git Remote is Optional:** The workflow works entirely locally. User can add a remote later when ready to share.

See `.claude/templates/project_init.md` for full initialization flow.

### Step 3: If Already Initialized → Start Coordinator

Read `workflow_state` and start the **Coordinator** session:

```
╔══════════════════════════════════════════════════════════════════╗
║               🎯 COORDINATOR - TASK ORCHESTRATION                 ║
╠══════════════════════════════════════════════════════════════════╣
║ Project: [project_name]                                          ║
║ Current Phase: [phase]                                           ║
║ Iteration: [current_iteration.id]                                ║
╠══════════════════════════════════════════════════════════════════╣
║ WORKFLOW PROGRESS                                                ║
║ ┌──────────────────────────────────────────────────────────────┐ ║
║ │ [✓/▶/ ] Initialization                                       │ ║
║ │ [✓/▶/ ] Discovery                                            │ ║
║ │ [✓/▶/ ] Design (OpenSpec)                                    │ ║
║ │ [✓/▶/ ] Implementation                                       │ ║
║ │ [✓/▶/ ] Review                                               │ ║
║ │ [✓/▶/ ] Merge                                                │ ║
║ └──────────────────────────────────────────────────────────────┘ ║
╠══════════════════════════════════════════════════════════════════╣
║ NEXT ACTION: [next_action.description]                           ║
╚══════════════════════════════════════════════════════════════════╝
```

---

## Coordinator-Based Workflow (NEW)

**The Coordinator orchestrates all sessions linearly and synchronously.**

### Key Principles

1. **Linear Execution**: One task at a time, blocking until complete
2. **OpenSpec Mandatory**: ALL features MUST have an OpenSpec before implementation
3. **Synchronous Flow**: Wait for each session to complete before proceeding
4. **State Awareness**: Always read current state on startup

### OpenSpec Enforcement

```
┌─────────────────────────────────────────────────────────────────┐
│                  MANDATORY OPENSPEC WORKFLOW                     │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ❌ BEFORE (Not Allowed)                                         │
│  User: "Add login feature"                                       │
│  → B Session starts coding immediately                           │
│                                                                  │
│  ✅ AFTER (Required)                                             │
│  User: "Add login feature"                                       │
│  → Coordinator: "Creating OpenSpec first..."                     │
│  → A Session: Creates openspec/changes/login/SPEC.md             │
│  → User: Reviews and approves spec                               │
│  → B Session: Implements per spec                                │
│  → C Session: Reviews against spec                               │
│  → Coordinator: Archives to openspec/specs/                      │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### Session Flow

```
Coordinator
    │
    ├──► A Session (Architect)
    │    └─→ Creates OpenSpec in openspec/changes/
    │    └─→ Updates DESIGN_STATE.yaml
    │
    ├──► User Approval
    │    └─→ Reviews spec
    │    └─→ Says "approve" or provides feedback
    │
    ├──► B Session (Implementer)
    │    └─→ Reads .env and confirms environment
    │    └─→ Implements per OpenSpec
    │    └─→ Self-tests, creates PR
    │
    ├──► C Session (Reviewer)
    │    └─→ Reviews against OpenSpec
    │    └─→ Pass/Fail verdict
    │
    └──► Coordinator
         └─→ Archives spec if passed
         └─→ Moves to next task
```

---

## Project Structure

```
.ai/                          # Tool-agnostic agent team definitions
├── README.md                 # Agent team overview and usage guide
├── project-plan.md           # Global project plan (project management)
└── agents/
    ├── coordinator.md        # Project Manager role
    ├── architect.md          # Architect/Designer role
    ├── developer.md          # Base Developer role (references specializations)
    ├── frontend-developer.md # Frontend specialist (skills: frontend, testing, agent-browser)
    ├── backend-developer.md  # Backend specialist (skills: backend, testing)
    ├── mobile-developer.md   # Mobile specialist (skills: testing)
    └── reviewer.md           # Reviewer/QA role

.claude/
├── DESIGN_STATE.yaml     # Single source of truth - configuration & state
├── prompts/
│   ├── COORDINATOR.md    # Coordinator session instructions (NEW)
│   ├── A_SESSION.md      # Architect session instructions
│   ├── B_SESSION.md      # Implementer session instructions
│   └── C_SESSION.md      # Reviewer session instructions
├── templates/            # Workflow templates
│   ├── project_init.md   # Initialization guide
│   ├── coordinator_task.md # Task orchestration template (NEW)
│   └── ...               # Other templates
└── handoffs/             # Task handoff documents

openspec/                 # Spec-driven development (MANDATORY)
├── specs/                # Completed specifications (archived)
├── changes/              # Active proposals (MUST exist before implementation)
│   └── {feature}/
│       ├── SPEC.md       # Feature specification
│       ├── TASKS.md      # Implementation tasks
│       └── ACCEPTANCE.md # Acceptance criteria
└── AGENTS.md             # AI agent instructions
```

---

## Four-Session Workflow

This template uses a coordinator-based model for AI-assisted development:

| Session | Role | Responsibilities |
|---------|------|------------------|
| **Coordinator** | Orchestrator | Linear task execution, state management, OpenSpec enforcement |
| **A Session** | Architect | Design, discovery, OpenSpec creation, DESIGN_STATE management |
| **B Session** | Implementer | Fill TODOs, self-test with .env check, create PRs |
| **C Session** | Reviewer | Code review against OpenSpec, constraint validation |

### Model Selection

During initialization, you can choose which model to use for each session:

- **Default (Recommended)**: A=Reasoning, B=Fast, C=Fast
- **Custom**: Choose any combination per session

Model preferences are stored in `initialization.model_preferences` in DESIGN_STATE.yaml.

### Workflow Phases

1. **Initialization** - First-time project setup
2. **Discovery** - A Session interviews user to understand requirements
3. **Design** - A Session creates OpenSpec in `openspec/changes/`
4. **Spec Review** - User approves spec before implementation (NEW)
5. **Implementation** - B Session implements per spec, checks .env first
6. **Review** - C Session validates against spec and constraints
7. **Merge** - After approval, merge to main
8. **Archive** - Move spec from `changes/` to `specs/`
9. **Completed** - Ready for next task

---

## Environment Check (NEW)

**B Session MUST read .env before self-test and inform user.**

```
╔══════════════════════════════════════════════════════════════════╗
║  🔐 ENVIRONMENT CHECK - Before Self-Test                         ║
╠══════════════════════════════════════════════════════════════════╣
║                                                                  ║
║  Reading .env file...                                            ║
║                                                                  ║
║  FOUND VARIABLES:                                                ║
║  ┌────────────────────────────────────────────────────────────┐  ║
║  │ DATABASE_URL      = postgresql://...                       │  ║
║  │ JWT_SECRET        = ********                               │  ║
║  └────────────────────────────────────────────────────────────┘  ║
║                                                                  ║
║  REQUIRED BUT MISSING:                                           ║
║  ┌────────────────────────────────────────────────────────────┐  ║
║  │ ⚠️  STRIPE_KEY - Required for payment processing           │  ║
║  └────────────────────────────────────────────────────────────┘  ║
║                                                                  ║
║  Please confirm environment is ready. Reply "ready" to continue. ║
║                                                                  ║
╚══════════════════════════════════════════════════════════════════╝
```

---

## Key Commands

| User Says | What Happens |
|-----------|--------------|
| "Initialize project" | Run initialization flow |
| "Show status" | Display coordinator status |
| "Start" or "Next" | Execute next pending action |
| "Approve [spec-name]" | Approve OpenSpec for implementation |
| "Create spec for [feature]" | A Session creates new OpenSpec |
| "Show tasks" | Display pending tasks |
| "Review the code" | Start C Session code review |
| "Report a bug" | Create bug report |
| "Pause" | Save state and pause orchestration |
| "Resume" | Resume from saved state |

---

## Important Files to Read

When starting work, read these files in order:

1. `.ai/README.md` - Agent team overview (tool-agnostic)
2. `.ai/project-plan.md` - Global project plan and task status
3. `.claude/DESIGN_STATE.yaml` - Current state and configuration
4. `.claude/prompts/COORDINATOR.md` - Coordinator instructions
5. `openspec/changes/` - Active specs being worked on
6. Relevant session prompt (A/B/C_SESSION.md based on current phase)
7. Current task handoff (in `.claude/handoffs/`)

---

## Rules

1. **Coordinator orchestrates all sessions linearly**
2. **OpenSpec REQUIRED before any implementation**
3. **Only A Session modifies DESIGN_STATE.yaml**
4. **B Session MUST check .env and inform user before self-test**
5. **B Session follows constraints strictly**
6. **C Session validates against OpenSpec and DESIGN_STATE constraints**
7. **All sessions display status on start**
8. **One task at a time - no parallel task execution**

---

## Getting Help

- See `.ai/README.md` for agent team overview (works with any AI tool)
- See `.ai/agents/` for agent role definitions
- See `.ai/project-plan.md` for the global project plan
- See `.claude/templates/INDEX.md` for all available templates
- See `README.md` for full documentation
- See `.claude/prompts/COORDINATOR.md` for coordinator details
- See `.claude/prompts/` for session-specific instructions
