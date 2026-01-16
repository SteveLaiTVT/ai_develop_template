# Templates Index & Declaration

This document describes all templates used in the AI-assisted development workflow.

---

## Template Overview

| Template | File | From | To | Purpose |
|----------|------|------|-----|---------|
| **Session Start** | `session_start.md` | System | All Sessions | Display workflow state on start (NEW) |
| **Discovery Questions** | `discovery_questions.md` | A Session | User | Project discovery interview (NEW) |
| Task Handoff | `task_handoff.md` | A Session | B Session | Assign implementation tasks |
| Implementation Report | `implementation_report.md` | B Session | C Session | Report completed work |
| Review Report | `review_report.md` | C Session | A Session | Code review results |
| Question Feedback | `question_feedback.md` | B Session | A Session | Questions and blockers |
| Bug Report | `bug_report.md` | Human | A Session | Manual testing feedback |
| Design State Changelog | `design_state_changelog.md` | A Session | Internal | Track design changes |
| Iteration Summary | `iteration_summary.md` | A Session | Stakeholders | Version release summary |
| External Review | `external_review.md` | Codex/Gemini | A Session | External AI audit |
| Git Workflow | `git_workflow.md` | - | All | Version control guide |
| OpenSpec Integration | `openspec_integration.md` | - | All | Spec-driven development guide |
| **Cowork Integration** | `cowork_integration.md` | - | All | Claude Cowork setup guide (NEW) |

---

## Workflow Diagram

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
│  • current_iteration - Active tasks                                 │
└───────────────────────────────┬─────────────────────────────────────┘
                                │
                                │
    ╔═══════════════════════════╧═══════════════════════════╗
    ║              PHASE 0: SESSION START                    ║
    ║  All sessions display STATE TIP on start               ║
    ║  (shows current phase, pending tasks, next action)     ║
    ╚═══════════════════════════╤═══════════════════════════╝
                                │
    ╔═══════════════════════════╧═══════════════════════════╗
    ║              PHASE 1: DISCOVERY (New Projects)         ║
    ║  A Session asks questions about:                       ║
    ║  • Target users & audience                             ║
    ║  • Technical context & resources                       ║
    ║  • Product vision & MVP                                ║
    ║  • Constraints & compliance                            ║
    ╚═══════════════════════════╤═══════════════════════════╝
                                │
        ┌───────────────────────┴───────────────────────┐
        │                                               │
        ▼                                               │
┌───────────────────┐                                   │
│   A SESSION       │                                   │
│   (Opus 4.5)      │                                   │
│                   │                                   │
│ Reads:            │                                   │
│ - DESIGN_STATE    │                                   │
│ - Bug Reports     │                                   │
│ - Review Reports  │                                   │
│ - Question        │                                   │
│   Feedback        │                                   │
│                   │                                   │
│ Outputs:          │                                   │
│ - Discovery Q&A   │                                   │
│ - Project Analysis│                                   │
│ - Task Handoff ───┼──────────┐                       │
│ - Design State    │          │                       │
│   Changelog       │          │                       │
└───────────────────┘          │                       │
        ▲                      │                       │
        │                      ▼                       │
        │              ┌───────────────────┐          │
        │              │   B SESSION       │          │
        │              │   (Sonnet 4.5)    │          │
        │              │                   │          │
        │              │ Reads:            │          │
        │              │ - Task Handoff    │          │
        │              │ - DESIGN_STATE    │          │
        │              │ - project_analysis│          │
        │              │                   │          │
        │              │ NEW Workflow:     │          │
        │              │ 1. Fill TODOs     │          │
        │              │ 2. Check env vars │          │
        │              │ 3. Self-test API  │          │
        │              │ 4. Self-test UI   │          │
        │              │    (agent-browser)│          │
        │              │ 5. Commit working │          │
        │              │                   │          │
        │              │ Outputs:          │          │
        │◄─────────────┤ - Question        │          │
        │              │   Feedback        │          │
        │              │ - Implementation ─┼────┐     │
        │              │   Report          │    │     │
        │              │ - Self-test       │    │     │
        │              │   Results         │    │     │
        │              └───────────────────┘    │     │
        │                                       │     │
        │                                       ▼     │
        │              ┌───────────────────┐         │
        │              │   C SESSION       │         │
        │              │   (Sonnet 4.5)    │         │
        │              │                   │         │
        │              │ Reads:            │         │
        │              │ - Implementation  │         │
        │              │   Report          │         │
        │              │ - Self-test       │         │
        │              │   Results         │         │
        │              │ - Code Files      │         │
        │              │ - DESIGN_STATE    │         │
        │              │                   │         │
        │◄─────────────┤ Outputs:          │         │
        │              │ - Review Report   │         │
        │              └───────────────────┘         │
        │                                            │
        │              ┌───────────────────┐         │
        │              │  HUMAN TESTING    │         │
        │              │                   │         │
        │◄─────────────┤ Outputs:          │         │
        │              │ - Bug Report      │         │
        │              └───────────────────┘         │
        │                                            │
        │              ┌───────────────────┐         │
        │              │ EXTERNAL REVIEW   │         │
        │              │ (Codex/Gemini)    │         │
        │              │                   │         │
        │◄─────────────┤ Outputs:          │         │
                       │ - External Review │         │
                       │   Report          │         │
                       └───────────────────┘         │
```

---

## Template Declarations

### 0. Session Start (`session_start.md`) - NEW

**Purpose**: Display workflow state when any session starts

**Key Sections**:
- Current phase indicator (discovery | design | implementation | review)
- Session status table (A, B, C with their states)
- Pending tasks list
- Next action recommendation
- Environment warnings (if blocked)

**When to Use**: Every session should display this state tip immediately on start.

**Example Output**:
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

---

### 0.5 Discovery Questions (`discovery_questions.md`) - NEW

**Purpose**: A Session conducts discovery interview for new projects

**Key Sections**:
- Target Users & Audience questions
- Technical Context questions
- Product Vision questions
- Constraints questions
- Summary template
- Recording answers to DESIGN_STATE.yaml

**When to Use**: When `discovery.completed: false` in DESIGN_STATE.yaml

**Question Categories**:
1. Who will use this app? (age, tech level, device, accessibility)
2. Your technical background? (stack preference, experience)
3. Product vision? (problem, MVP scope, design style)
4. Constraints? (budget, compliance, integrations)

---

### 1. Task Handoff (`task_handoff.md`)

**Purpose**: A Session assigns implementation work to B Session

**Key Sections**:
- `objective`: What the task should achieve
- `tasks[]`: List of specific tasks with acceptance criteria
- `skeleton_files[]`: **Code structure with TODO markers** for B Session to fill
- `out_of_scope`: What NOT to implement
- `constraints_to_follow`: Rules B Session must follow

**Skeleton Pattern**:
```typescript
// A Session provides:
export class AuthService {
  // TODO(B): Implement password hashing
  // - Use bcrypt with cost factor >= 12
  // - Handle empty password edge case
  async hashPassword(password: string): Promise<string> {
    throw new Error('Not implemented');
  }
}

// B Session fills in the implementation
```

---

### 2. Implementation Report (`implementation_report.md`)

**Purpose**: B Session reports completed work to C Session

**Key Sections**:
- `summary`: What was implemented
- `files_changed[]`: List of created/modified files
- `acceptance_checklist[]`: Self-check against criteria
- `todos_completed[]`: Which TODOs were filled in
- `known_issues[]`: Technical debt acknowledged
- `questions_for_a[]`: Questions for next iteration

---

### 3. Review Report (`review_report.md`)

**Purpose**: C Session provides code review results to A Session

**Key Sections**:
- `summary.verdict`: `pass` | `conditional_pass` | `fail`
- `constraints_check[]`: Validation against DESIGN_STATE constraints
- `acceptance_check[]`: Verification of acceptance criteria
- `issues[]`: Problems found, sorted by severity
- `conclusion.can_merge`: Whether code can be merged

**Severity Levels**:
- `critical`: Security/data loss - blocks release
- `error`: Must fix before merge
- `warning`: Should fix
- `suggestion`: Optional improvement

---

### 4. Question Feedback (`question_feedback.md`)

**Purpose**: B Session asks questions or reports blockers

**Two Variants**:
1. **Non-blocking questions**: B Session continues with assumptions
2. **Blockers**: B Session cannot proceed

**Key Fields**:
- `blocker`: `true` | `false`
- `questions[].need_confirmation`: Whether to wait for answer
- `my_assumption`: What B Session will do if no answer

---

### 5. Bug Report (`bug_report.md`)

**Purpose**: Human testers report issues found during testing

**Key Sections**:
- `severity`: `critical` | `high` | `medium` | `low`
- `steps_to_reproduce`: Exact steps to recreate
- `environment`: Browser, OS, device info
- `attachments`: Screenshots, logs, videos

---

### 6. Design State Changelog (`design_state_changelog.md`)

**Purpose**: A Session documents changes to DESIGN_STATE.yaml

**Key Sections**:
- `version_change`: Version before and after
- `changes[]`: What changed and why
- `impact`: What's affected by the change
- `rollback`: How to undo if needed

---

### 7. Iteration Summary (`iteration_summary.md`)

**Purpose**: Summary for stakeholders when iteration completes

**Language**: Contains **Chinese summary** (`summary_zh`, `todo_list_zh`) for stakeholders

**Key Sections**:
- `features_completed[]`: What was delivered
- `bugs_fixed[]`: What was fixed
- `metrics`: Statistics about the iteration
- `todo_list_zh`: Chinese TODO list

---

### 8. External Review (`external_review.md`)

**Purpose**: Template for external AI (Codex/Gemini) review

**Two Parts**:
1. **Request**: What to send to external reviewer
2. **Report**: Expected response format

**Key Rules**:
- External reviewer **cannot** change architecture
- A Session evaluates and decides on findings

---

### 9. Git Workflow (`git_workflow.md`)

**Purpose**: Version control conventions

**Key Elements**:
- Branch strategy: `main` → `develop` → `iter-XXX` → `task-XXX`
- Commit message format: `<type>(<scope>): <subject>`
- Tag format: `vX.Y.Z`
- References: Link to TASK, BUG, ADR in commits

---

## How to Use Templates

### Starting a New Task (A Session)

1. Read current `DESIGN_STATE.yaml`
2. Copy `task_handoff.md` template
3. Fill in objective, tasks, skeleton code with TODOs
4. Update `DESIGN_STATE.yaml` version
5. Commit: `git commit -m "design(iter-XXX): assign TASK-YYY"`

### Implementing a Task (B Session)

1. Read `DESIGN_STATE.yaml` constraints
2. Read task handoff document
3. Fill in TODOs in skeleton code
4. Copy `implementation_report.md` template
5. Fill in report
6. Commit: `git commit -m "feat(module): implement [feature]"`

### Reviewing Code (C Session)

1. Read implementation report
2. Review code against constraints
3. Copy `review_report.md` template
4. Fill in findings
5. Set verdict and can_merge

### Handling Questions (B Session)

1. Copy `question_feedback.md` template
2. Fill in questions with assumptions
3. If `need_confirmation: false`, continue working
4. If blocker, stop and wait

### Completing Iteration (A Session)

1. Copy `iteration_summary.md` template
2. Fill in Chinese summary for stakeholders
3. Tag version: `git tag -a vX.Y.Z`
4. Merge to develop

---

## OpenSpec Integration

### 10. OpenSpec Integration (`openspec_integration.md`)

**Purpose**: Guide for using OpenSpec with the three-session workflow

**What is OpenSpec**: A spec-driven development framework that separates current specifications (`openspec/specs/`) from proposed updates (`openspec/changes/`). It complements DESIGN_STATE.yaml by providing detailed specifications for features.

**Key Features**:
- Lightweight, no-code specification management
- Native slash command support for AI tools
- Structured proposal → review → implement → archive workflow
- Keeps specs and code in sync

**Integration with Sessions**:
- **A Session**: Creates OpenSpec change proposals with detailed specs
- **B Session**: Implements according to OpenSpec specifications
- **C Session**: Validates implementation against specs

**Common Commands**:
```bash
openspec list                # List active changes
openspec view                # Interactive dashboard
openspec show <name>         # Show change details
openspec archive <name>      # Archive completed work
```

**When to Use**:
- **DESIGN_STATE.yaml**: High-level architecture, constraints, module status
- **OpenSpec**: Detailed API contracts, implementation tasks, acceptance criteria

**Quick Start**:
1. Install: `npm install -g @fission-ai/openspec@latest`
2. A Session creates proposal: "Create an OpenSpec change proposal for [feature]"
3. Review and refine the spec
4. B Session implements: "Implement according to openspec/changes/[name]/"
5. C Session validates against spec
6. Archive: `openspec archive [name] --yes`

See the full guide at `.claude/templates/openspec_integration.md` for detailed workflows and examples.

---

## Claude Cowork Integration

### 11. Cowork Integration (`cowork_integration.md`) - NEW

**Purpose**: Guide for using Claude Cowork with the AI Development Workflow

**What is Claude Cowork**: Anthropic's feature that brings Claude Code's agentic capabilities to non-developers through the Claude Desktop app (macOS). It provides:
- Direct file access without terminal commands
- Browser navigation (with Chrome extension)
- Third-party integrations via Connectors (Asana, Notion, Canva, etc.)

**Availability** (as of January 2026):
- Claude Max subscribers ($100-200/month)
- macOS only via Claude Desktop app
- Research preview (other plans can join waitlist)

**Key Features**:
- Simplified file operations for non-developers
- Visual frontend testing via browser
- Project management tool integration
- Design file access

**Integration with Sessions**:
- **A Session**: Browse reference apps, access project management tools, view design files
- **B Session**: Simplified file editing, browser-based UI testing
- **C Session**: Visual inspection, design mockup comparison

**Detection**: At session start, check `cowork.current_session.is_cowork` in DESIGN_STATE.yaml

**When to Use Cowork vs Claude Code**:
| Use Cowork For | Use Claude Code For |
|----------------|---------------------|
| File read/write | npm/yarn commands |
| Visual testing | Database operations |
| Design reference | Docker/CI-CD |
| Project mgmt access | Build processes |

**Quick Start**:
1. Open Claude Desktop on macOS
2. Enable Cowork feature
3. Grant folder access to project
4. (Optional) Install Chrome extension
5. (Optional) Connect third-party apps

See the full guide at `.claude/templates/cowork_integration.md` for setup and workflow details.
