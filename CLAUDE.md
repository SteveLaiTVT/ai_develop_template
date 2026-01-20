# AI Development Template - Assistant Instructions

This file is automatically read by the AI assistant when opening this project.

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
5. **Git Remote** (OPTIONAL): Your repository URL or "skip" for local-only git
6. **Model Selection** (OPTIONAL): Which model for each session?
   - Default: A=Reasoning, B=Fast, C=Fast
   - Custom: A/B/C set per user's preference

**Then execute initialization:**

1. Update `DESIGN_STATE.yaml` with answers
2. Clear template `.git`: `rm -rf .git`
3. Create `.gitignore` file
4. Initialize new repo: `git init && git branch -M main`
5. Add remote (OPTIONAL - only if user provided URL)
6. Make initial commit with user's project intent
7. Set `initialization.initialized: true` and `is_template: false`
8. Transition to discovery phase

**Git Remote is Optional:** The workflow works entirely locally. User can add a remote later when ready to share.

See `.claude/templates/project_init.md` for full initialization flow.

### Step 3: If Already Initialized → Display Status

Read `workflow_state` and display current status:

```
╔══════════════════════════════════════════════════════════════════╗
║                    🚀 AI DEVELOPMENT WORKFLOW                     ║
╠══════════════════════════════════════════════════════════════════╣
║ Current Phase: [phase]                                           ║
║ Project: [project_name]                                          ║
╠══════════════════════════════════════════════════════════════════╣
║ SESSION STATUS                                                   ║
║ ┌────────────┬────────────┬────────────┐                        ║
║ │ A Session  │ B Session  │ C Session  │                        ║
║ │ [status]   │ [status]   │ [status]   │                        ║
║ └────────────┴────────────┴────────────┘                        ║
╠══════════════════════════════════════════════════════════════════╣
║ 👉 NEXT: [next_action.description]                               ║
╚══════════════════════════════════════════════════════════════════╝
```

---

## Project Structure

```
.claude/
├── DESIGN_STATE.yaml     # Single source of truth - configuration & state
├── prompts/
│   ├── A_SESSION.md      # Architect session instructions
│   ├── B_SESSION.md      # Implementer session instructions
│   └── C_SESSION.md      # Reviewer session instructions
├── templates/            # Workflow templates
│   ├── project_init.md   # Initialization guide
│   └── ...               # Other templates
└── handoffs/             # Task handoff documents

openspec/                 # Spec-driven development
├── specs/                # Completed specifications
└── changes/              # Active proposals
```

---

## Three-Session Workflow

This template uses a three-session model for AI-assisted development:

| Session | Default Model | Role | Responsibilities |
|---------|---------------|------|------------------|
| **A Session** | Reasoning | Architect | Design, discovery, task decomposition |
| **B Session** | Execution | Implementer | Fill TODOs, self-test, create PRs |
| **C Session** | Review | Reviewer | Code review, constraint validation |

### Model Selection

During initialization, you can choose which model to use for each session:

- **Default (Recommended)**: A=Reasoning, B=Fast, C=Fast
  - Balance of capability and speed
  - A handles complex architecture decisions
  - B/C handle implementation and review efficiently

- **Custom**: Choose any combination of models per session
  - Use a stronger model for A when decisions are complex
  - Use faster models for routine implementation/review

Model preferences are stored in `initialization.model_preferences` in DESIGN_STATE.yaml.

### Workflow Phases

1. **Initialization** - First-time project setup (this file guides this)
2. **Discovery** - A Session interviews user to understand requirements
3. **Design** - A Session creates architecture and skeleton code
4. **Implementation** - B Session fills in TODOs, tests on feature branch
5. **Review** - C Session validates code against constraints
6. **Merge** - After C Session approves, merge to main branch locally
7. **Completed** - Iteration done, ready for user testing

### Git Workflow (Local-First)

```
main ─────────────────────────────────────────► (stable)
      │                              │
      └── feature/task-001 ──────────┘
          (B implements → C reviews → merge to main)
```

- B Session creates feature branch: `git checkout -b feature/task-xxx`
- After C Session approves: `git checkout main && git merge feature/task-xxx`
- No remote required - push when user is ready

---

## Key Commands

| User Says | What Happens |
|-----------|--------------|
| "Initialize project" | Run initialization flow |
| "Show status" | Display current workflow state |
| "Start discovery" | Begin A Session discovery interview |
| "Show my tasks" | Display pending tasks for B Session |
| "Review the code" | Start C Session code review |
| "Report a bug" | Create bug report for A Session |
| "Request MCP test" | Trigger MCP/agent-based frontend testing |

---

## Important Files to Read

When starting work, read these files in order:

1. `.claude/DESIGN_STATE.yaml` - Current state and configuration
2. Relevant session prompt (A/B/C_SESSION.md based on current phase)
3. Current task handoff (in `.claude/handoffs/`)

---

## Rules

1. **Only A Session modifies DESIGN_STATE.yaml**
2. **B Session follows constraints strictly**
3. **C Session validates against DESIGN_STATE constraints**
4. **All sessions display status tip on start**
5. **B Session must self-test before creating PRs**
6. **Frontend testing should prefer MCP/agent browser tools**
6. **Notify user when iteration is ready for testing**

---

## Getting Help

- See `.claude/templates/INDEX.md` for all available templates
- See `README.md` for full documentation
- See `.claude/prompts/` for detailed session instructions
