# Templates Index & Declaration

This document describes all templates used in the AI-assisted development workflow.

---

## Template Overview

| Template | File | From | To | Purpose |
|----------|------|------|-----|---------|
| **Project Init** | `project_init.md` | System | User | First-time project setup |
| **Coordinator Task** | `coordinator_task.md` | Coordinator | All | Linear task orchestration (NEW) |
| **Session Start** | `session_start.md` | System | All Sessions | Display workflow state on start |
| **Discovery Questions** | `discovery_questions.md` | A Session | User | Project discovery interview |
| **Tech Best Practices** | `tech_best_practices.md` | A Session | All | Apply industry best practices |
| Task Handoff | `task_handoff.md` | A Session | B Session | Assign implementation tasks |
| Implementation Report | `implementation_report.md` | B Session | C Session | Report completed work |
| Review Report | `review_report.md` | C Session | A Session | Code review results |
| Question Feedback | `question_feedback.md` | B Session | A Session | Questions and blockers |
| Bug Report | `bug_report.md` | Human | A Session | Manual testing feedback |
| Design State Changelog | `design_state_changelog.md` | A Session | Internal | Track design changes |
| Iteration Summary | `iteration_summary.md` | A Session | Stakeholders | Version release summary |
| External Review | `external_review.md` | Codex/Gemini | A Session | External AI audit |
| Git Workflow | `git_workflow.md` | - | All | Version control guide |
| OpenSpec Integration | `openspec_integration.md` | - | All | Spec-driven development guide (MANDATORY) |
| **Progressive Testing** | `progressive_testing.md` | - | B Session | Testing strategy guide |
| **API Request Export** | `api_request_export.md` | B Session | User | API testing scripts |
| **Agent Skills** | `../skills/INDEX.md` | - | All Sessions | Agent skill specifications (NEW) |

---

## Workflow Diagram

```
┌─────────────────────────────────────────────────────────────────────┐
│                         DESIGN_STATE.yaml                           │
│                    (Single Source of Truth)                         │
│                                                                     │
│  Sections:                                                          │
│  • workflow_state    - Current phase & session status               │
│  • coordinator       - Task queue & linear execution (NEW)          │
│  • openspec          - Active specs (MANDATORY)                     │
│  • skills            - Agent skills configuration (NEW)             │
│  • project_analysis  - Auto-detected frontend/backend/env           │
│  • discovery         - User interview results                       │
│  • architecture      - Tech stack decisions                         │
│  • current_iteration - Active tasks                                 │
└───────────────────────────────────────────────────────────────────┬─┘
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
│   (Reasoning)     │                                   │
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
        │              │   (Execution)    │          │
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
        │              │    (MCP/agent)    │          │
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
        │              │   (Review)       │         │
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

### -1. Project Initialization (`project_init.md`)

**Purpose**: First-time setup when the AI Development Template is loaded

**When to Run**: When `initialization.is_template: true` in DESIGN_STATE.yaml

**Key Steps**:
1. Display welcome banner
2. Ask onboarding questions (project name, description, type, stack, git remote)
3. Git setup (clear template .git, init new repo, add remote)
4. Update DESIGN_STATE.yaml with user's answers
5. Make initial commit
6. Transition to discovery phase
7. Start Coordinator for session orchestration

**Entry Point**: See `CLAUDE.md` in project root for how this is triggered.

**Onboarding Questions**:
```
Q1: What would you like to call this project?
Q2: Briefly describe what you want to build
Q3: What type of project? (web-app | mobile-app | api-service | full-stack)
Q4: Tech stack preference? (recommend | specific stack)
Q5: Git repository URL? (url | skip)
```

**After Initialization**:
- `initialization.is_template` becomes `false`
- `initialization.initialized` becomes `true`
- `workflow_state.current_phase` becomes `"discovery"`
- Coordinator begins orchestrating sessions

---

### -0.5. Coordinator Task (`coordinator_task.md`) - NEW

**Purpose**: Linear task orchestration with mandatory OpenSpec workflow

**When to Use**: After project initialization, Coordinator manages all tasks

**Key Features**:
- **Linear Execution**: One task at a time, blocking until complete
- **OpenSpec Mandatory**: ALL features must have OpenSpec before implementation
- **State Tracking**: Tracks each step of the workflow
- **Session Orchestration**: Calls A/B/C sessions in sequence

**Task Workflow**:
```
1. spec_creation   → A Session creates OpenSpec
2. spec_review     → User approves spec
3. implementation  → B Session implements (with .env check)
4. code_review     → C Session reviews
5. merge           → Merge to main
6. archive         → Move spec to openspec/specs/
```

**Task States**:
- `drafting` → `pending_review` → `approved` → `implementing` → `code_review` → `done`

**Key Rules**:
- No implementation without approved spec
- B Session must show .env contents before self-test
- One task at a time - no parallel execution
- Failed steps block progress until resolved

**Commands**:
| Command | Action |
|---------|--------|
| `start` | Begin from current state |
| `status` | Display current task status |
| `approve [spec]` | Approve spec for implementation |
| `next` | Execute next step |
| `pause` | Save state and pause |
| `resume` | Resume from checkpoint |

See `.claude/templates/coordinator_task.md` for full task record format

---

### 0. Session Start (`session_start.md`)

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

### 0.6 Tech Best Practices (`tech_best_practices.md`) - NEW

**Purpose**: Apply technology-specific best practices after stack selection

**When to Use**: After Q4 (tech stack selection) in onboarding or discovery

**Key Features**:
- Asks user if they want to apply best practices ("yes" | "selective" | "no")
- Matches selected technologies with best practice catalog in DESIGN_STATE.yaml
- Updates `tech_best_practices.applied_practices` with chosen patterns
- Integrates practices into skeleton code generation

**Priority Order** (Apply in this order):

| Priority | Category | Authority |
|----------|----------|-----------|
| **0 (FIRST)** | Claude Code Plugin | Anthropic (ALWAYS FIRST) |
| 1 | External Skills (skills.sh) | Vercel Labs, Anthropic, Remotion |
| 2 | AI Development | Anthropic > Google > Cursor > OpenAI |
| 2 | Other tech-specific | Creator/Authority |

**External Skills** (from skills.sh):
- Vercel React Best Practices: https://skills.sh/vercel-labs/agent-skills/vercel-react-best-practices
- Web Design Guidelines: https://skills.sh/vercel-labs/agent-skills/web-design-guidelines
- Anthropic Frontend Design: https://skills.sh/anthropics/skills/frontend-design
- Agent Browser: https://skills.sh/vercel-labs/agent-browser/agent-browser
- Remotion Best Practices: https://skills.sh/remotion-dev/skills/remotion-best-practices

**Available Practice Categories**:
| Category | Technologies | Authority |
|----------|--------------|-----------|
| Claude Code Plugin | CLAUDE.md, MCP, Three-Session | Anthropic |
| Frontend (SSR) | Next.js, Vercel Edge | Vercel, Vercel Labs Skills |
| Frontend (React) | React 18+ | Meta, Vercel Labs Skills |
| Web Design | UI/UX, Design Systems | Vercel Labs, Anthropic Skills |
| Video | Remotion | Remotion Dev Skills |
| Browser Automation | Agent Browser | Vercel Labs Skills |
| AI Development | Claude, Gemini, Cursor, OpenAI | Anthropic > Google > Cursor > OpenAI |
| Mobile (Android) | Jetpack Compose, Kotlin | Google |
| Mobile (Flutter) | Flutter, Dart | Google |
| Mobile (iOS) | SwiftUI, Swift | Apple |
| Java Ecosystem | Java, Spring Boot, Dubbo, Nacos, Sentinel | Oracle > Google > Alibaba > Spring |
| Kotlin Backend | Kotlin + Spring Boot, Ktor | Spring Team / JetBrains |
| Backend | NestJS, Express, FastAPI, Firebase | Framework teams, Google |
| Database | PostgreSQL, MongoDB | Official docs |
| Testing | Jest, Vitest, Playwright | Meta, Microsoft |
| Language | TypeScript, Kotlin, Swift | Microsoft, Google/JetBrains, Apple |
| Security | General practices | OWASP |

**User Options**:
- `"yes"`: Apply all relevant best practices automatically
- `"selective"`: Show options for each tech, user picks which to apply
- `"no"`: Skip, use minimal patterns

**Integration**:
- **A Session**: Asks the question, applies practices, incorporates into skeleton
- **B Session**: Follows applied practices when filling TODOs
- **C Session**: Validates code against applied practices

**Commands**:
- "Apply best practices" - Re-apply for current stack
- "Show applied practices" - Display what's applied

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

## Progressive Testing Strategy

### 11. Progressive Testing (`progressive_testing.md`) - NEW

**Purpose**: Guide for practical, staged testing as part of the development workflow

**Philosophy**: "Self-test is HOW you develop. Add automated tests progressively."

**Key Insight**: Self-test is a core development practice at ALL stages - not just early stages. E2E tests can wait until features stabilize.

**Testing Levels**:

| Level | Name | When | Purpose |
|-------|------|------|---------|
| 1 | Self-Test | ALWAYS (core dev) | Verify code works in real env |
| 2 | Unit Tests | Progressively | Prevent regressions |
| 3 | E2E Tests | Mature Stage | Validate user journeys |

**Self-Test - Core Development Practice**:
```bash
# Backend: Run server, test with curl
npm run start:dev
curl -X POST http://localhost:3000/api/auth/register -d '...'

# Frontend: Start dev server, verify no errors
npm run dev
# Check: build succeeds, page loads, no console errors
```

**Frontend Testing Options**:
- **Quick Smoke Test**: Build succeeds, main page loads
- **MCP/Agent Browser Test**: Playwright MCP or other MCP-compatible agents
- **Manual User Test**: User tests and reports issues
- **Detailed Visual**: Full MCP/agent browser testing (mature stage)

**When to Add More Tests**:
- **Unit Tests**: Security code, complex logic, after fixing bugs
- **E2E Tests**: Stable features, preparing for production

**Integration with B Session**:
- B Session follows progressive testing by default
- Documents self-test results in commits/PRs
- Can delegate frontend testing to external agents
- Adds unit tests for critical paths
- E2E tests are optional until feature matures

See the full guide at `.claude/templates/progressive_testing.md` for detailed decision guides and examples.

---

## API Request Export

### 12. API Request Export (`api_request_export.md`) - NEW

**Purpose**: Export API requests before C Session review for user testing

**CRITICAL**: B Session MUST export all tested API requests before handoff to C Session.

**Why This Matters**:
- Enables users to quickly test backend APIs themselves
- Provides rapid feedback loop for API behavior
- Documents expected request/response formats
- Reduces back-and-forth in review process

**Export Formats**:

| Format | Location | Required | Tools |
|--------|----------|----------|-------|
| Shell Scripts | `tests/http-requests/` | **YES** | bash, curl |
| Postman | `tests/postman/` | Optional | Postman app |
| Apifox | `tests/apifox/` | Optional | Apifox app |
| HTTP Files | `tests/http-requests/*.http` | Optional | VS Code REST Client |

**Shell Scripts (Required)**:
```
tests/http-requests/
├── env.sh                    # Environment variables
├── README.md                 # Usage instructions
├── run-all.sh                # Run all tests
├── auth/
│   ├── 01-register.sh
│   ├── 02-login.sh
│   └── ...
└── user/
    └── ...
```

**Integration with Mock Data**:
- API request scripts should reference mock data from `tests/mock-data/`
- Ensures consistency between test data and API tests

**Workflow Integration**:
- **PHASE 4.5**: Export API requests (after commit, before PR)
- User can run scripts immediately after PR is created
- Faster feedback loop, fewer review iterations

**User Commands**:
```bash
# Quick test all APIs
cd tests/http-requests
source env.sh
./run-all.sh

# Test specific endpoint
./auth/01-register.sh
./auth/02-login.sh
```

See the full guide at `.claude/templates/api_request_export.md` for templates and examples.

---

## Agent Skills

### 13. Agent Skills (`../skills/INDEX.md`) - NEW

**Purpose**: Define skill specifications to help agents perform tasks according to best practices

**Location**: `.claude/skills/` directory

**Available Skills**:

| Skill | File | Priority | Description |
|-------|------|----------|-------------|
| **Git Version Control** | `git_skills.md` | **MANDATORY** | Git 版本控制规范 (强制) |
| **Frontend Development** | `frontend_skills.md` | Conditional | 前端开发规范 |
| **Backend Development** | `backend_skills.md` | Conditional | 后端开发规范 |
| **Agent-Browser** | `agent_browser_skills.md` | Conditional | 浏览器自动化技能 |
| **Testing** | `testing_skills.md` | Conditional | 测试规范与最佳实践 |

**Skill Application by Project Type**:

| Project Type | Applied Skills |
|--------------|----------------|
| Web Frontend | Git (mandatory), Frontend, Agent-Browser, Testing |
| Web Backend | Git (mandatory), Backend, Testing |
| Full-Stack | Git (mandatory), Frontend, Backend, Agent-Browser, Testing |
| API Service | Git (mandatory), Backend, Testing |

**Integration with Sessions**:
- **A Session**: Determines which skills apply based on project type
- **B Session**: Follows skill guidelines when implementing code
- **C Session**: Validates code against applicable skills

See the full skills index at `.claude/skills/INDEX.md` for details.
