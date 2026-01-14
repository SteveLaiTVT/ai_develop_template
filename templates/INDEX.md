# Templates Index & Declaration

This document describes all templates used in the AI-assisted development workflow.

---

## Template Overview

| Template | File | From | To | Purpose |
|----------|------|------|-----|---------|
| Task Handoff | `task_handoff.md` | A Session | B Session | Assign implementation tasks |
| Implementation Report | `implementation_report.md` | B Session | C Session | Report completed work |
| Review Report | `review_report.md` | C Session | A Session | Code review results |
| Question Feedback | `question_feedback.md` | B Session | A Session | Questions and blockers |
| Bug Report | `bug_report.md` | Human | A Session | Manual testing feedback |
| Design State Changelog | `design_state_changelog.md` | A Session | Internal | Track design changes |
| Iteration Summary | `iteration_summary.md` | A Session | Stakeholders | Version release summary |
| External Review | `external_review.md` | Codex/Gemini | A Session | External AI audit |
| Git Workflow | `git_workflow.md` | - | All | Version control guide |

---

## Workflow Diagram

```
┌─────────────────────────────────────────────────────────────────────┐
│                         DESIGN_STATE.yaml                           │
│                    (Single Source of Truth)                         │
└───────────────────────────────┬─────────────────────────────────────┘
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
│ - Task Handoff ───┼──────────┐                       │
│ - Design State    │          │                       │
│   Changelog       │          │                       │
│ - Iteration       │          │                       │
│   Summary         │          │                       │
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
        │              │   (constraints)   │          │
        │              │                   │          │
        │              │ Outputs:          │          │
        │◄─────────────┤ - Question        │          │
        │              │   Feedback        │          │
        │              │ - Implementation ─┼────┐     │
        │              │   Report          │    │     │
        │              │ - Code Files      │    │     │
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
        │              │ - Code Files      │         │
        │              │ - DESIGN_STATE    │         │
        │              │   (constraints)   │         │
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
