# Session Start - State Tip Display

This template defines how each session should display the current workflow state when starting.

---

## Purpose

When a user starts any AI session (A, B, or C), the AI should **immediately display the current workflow state** to help the user understand:
1. Which phase the project is in
2. Which session should be active
3. What pending tasks exist
4. What the next action should be

---

## State Tip Template

```
╔══════════════════════════════════════════════════════════════════╗
║                    🚀 AI DEVELOPMENT WORKFLOW                     ║
╠══════════════════════════════════════════════════════════════════╣
║ Current Phase: {workflow_state.current_phase}                    ║
║ Project: {meta.project_name}                                     ║
║ Design State: v{meta.version}                                    ║
╠══════════════════════════════════════════════════════════════════╣
║ SESSION STATUS                                                   ║
║ ┌────────────┬────────────┬────────────┐                        ║
║ │ A Session  │ B Session  │ C Session  │                        ║
║ │ {a_status} │ {b_status} │ {c_status} │                        ║
║ └────────────┴────────────┴────────────┘                        ║
╠══════════════════════════════════════════════════════════════════╣
║ PROJECT TYPE                                                     ║
║ • Frontend: {has_frontend}  Backend: {has_backend}               ║
║ • Mobile: {has_mobile}                                           ║
╠══════════════════════════════════════════════════════════════════╣
║ PENDING TASKS                                                    ║
║ {pending_tasks_list}                                             ║
╠══════════════════════════════════════════════════════════════════╣
║ 👉 NEXT ACTION: {next_action.session} Session                    ║
║    {next_action.action}                                          ║
║    {next_action.description}                                     ║
╚══════════════════════════════════════════════════════════════════╝
```

---

## Status Indicators

| Status | Display | Meaning |
|--------|---------|---------|
| `pending` | `pending` | Not started, waiting for previous phase |
| `active` | `▶ ACTIVE` | Currently executing |
| `waiting` | `waiting` | Completed work, waiting for next session |
| `blocked` | `⚠ BLOCKED` | Cannot proceed (missing env, blocker) |
| `completed` | `✓ done` | All tasks completed |

---

## Phase-Specific Tips

### Discovery Phase

```
╔══════════════════════════════════════════════════════════════════╗
║ Current Phase: discovery                                         ║
╠══════════════════════════════════════════════════════════════════╣
║ 📋 DISCOVERY NOT COMPLETE                                        ║
║                                                                  ║
║ Before designing, A Session should ask questions to understand:  ║
║ • Who are the target users?                                      ║
║ • What's the technical context?                                  ║
║ • What's the product vision?                                     ║
║ • What are the constraints?                                      ║
╠══════════════════════════════════════════════════════════════════╣
║ 👉 NEXT: A Session should run discovery interview                ║
╚══════════════════════════════════════════════════════════════════╝
```

### Design Phase

```
╔══════════════════════════════════════════════════════════════════╗
║ Current Phase: design                                            ║
╠══════════════════════════════════════════════════════════════════╣
║ ✓ Discovery completed                                            ║
║                                                                  ║
║ A Session should now:                                            ║
║ • Analyze requirements                                           ║
║ • Create skeleton code with TODOs                                ║
║ • Prepare task handoff for B Session                             ║
╠══════════════════════════════════════════════════════════════════╣
║ 👉 NEXT: A Session should create task handoff                    ║
╚══════════════════════════════════════════════════════════════════╝
```

### Implementation Phase

```
╔══════════════════════════════════════════════════════════════════╗
║ Current Phase: implementation                                    ║
╠══════════════════════════════════════════════════════════════════╣
║ B SESSION TASKS:                                                 ║
║ • TASK-001: [description] - [status]                             ║
║ • TASK-002: [description] - [status]                             ║
║                                                                  ║
║ Remember to:                                                     ║
║ 1. Fill all TODOs                                                ║
║ 2. Self-test (run server, test API/UI)                           ║
║ 3. Only commit working code                                      ║
╠══════════════════════════════════════════════════════════════════╣
║ 👉 NEXT: B Session should implement and self-test                ║
╚══════════════════════════════════════════════════════════════════╝
```

### Review Phase

```
╔══════════════════════════════════════════════════════════════════╗
║ Current Phase: review                                            ║
╠══════════════════════════════════════════════════════════════════╣
║ C SESSION REVIEW QUEUE:                                          ║
║ • TASK-001: Ready for review                                     ║
║                                                                  ║
║ Implementation report available at:                              ║
║ .claude/handoffs/iter-XXX/IR-XXX.yaml                            ║
║                                                                  ║
║ Review against:                                                  ║
║ • DESIGN_STATE.yaml constraints                                  ║
║ • Acceptance criteria in task handoff                            ║
╠══════════════════════════════════════════════════════════════════╣
║ 👉 NEXT: C Session should review implementation                  ║
╚══════════════════════════════════════════════════════════════════╝
```

---

## Environment Warning

If B Session is blocked by missing environment:

```
╔══════════════════════════════════════════════════════════════════╗
║  ⚠️  ENVIRONMENT CONFIGURATION REQUIRED                          ║
╠══════════════════════════════════════════════════════════════════╣
║                                                                  ║
║  B Session is blocked. Missing environment variables:            ║
║                                                                  ║
║  • DATABASE_URL - PostgreSQL connection string                   ║
║  • JWT_SECRET - Secret for JWT signing                           ║
║                                                                  ║
║  Please configure these in .env file before continuing.          ║
║                                                                  ║
╚══════════════════════════════════════════════════════════════════╝
```

---

## How to Read State

Sessions should read from `DESIGN_STATE.yaml`:

```yaml
# Read these sections:
workflow_state:
  current_phase: "implementation"  # What phase we're in

  sessions:
    a_session:
      status: "waiting"
      pending_tasks: []
    b_session:
      status: "active"
      pending_tasks: ["TASK-001", "TASK-002"]
      blocked_by_env: false
    c_session:
      status: "pending"
      pending_tasks: []

  next_action:
    session: "B"
    action: "Implement TODOs and self-test"
    description: "Fill in skeleton code, then run backend and frontend tests"

project_analysis:
  has_frontend: true
  has_backend: true
  has_mobile: false
```

---

## Session-Specific Display

### A Session Start

```
╔══════════════════════════════════════════════════════════════════╗
║  🅐 A SESSION - ARCHITECT                                        ║
╠══════════════════════════════════════════════════════════════════╣
║ Your role: Design, decompose tasks, maintain DESIGN_STATE        ║
║                                                                  ║
║ {standard_state_tip}                                             ║
║                                                                  ║
║ Available commands:                                              ║
║ • "start discovery" - Begin discovery interview                  ║
║ • "create task" - Create task handoff for B Session              ║
║ • "process review" - Handle C Session review feedback            ║
╚══════════════════════════════════════════════════════════════════╝
```

### B Session Start

```
╔══════════════════════════════════════════════════════════════════╗
║  🅱 B SESSION - IMPLEMENTER                                      ║
╠══════════════════════════════════════════════════════════════════╣
║ Your role: Fill TODOs, self-test, commit working code            ║
║                                                                  ║
║ {standard_state_tip}                                             ║
║                                                                  ║
║ Workflow reminder:                                               ║
║ 1. Read task handoff                                             ║
║ 2. Fill TODOs                                                    ║
║ 3. Check environment (pause if missing)                          ║
║ 4. Self-test backend (API calls)                                 ║
║ 5. Self-test frontend (agent-browser) if applicable              ║
║ 6. Commit working code                                           ║
║ 7. Create implementation report                                  ║
╚══════════════════════════════════════════════════════════════════╝
```

### C Session Start

```
╔══════════════════════════════════════════════════════════════════╗
║  🅲 C SESSION - REVIEWER                                         ║
╠══════════════════════════════════════════════════════════════════╣
║ Your role: Review code, validate constraints, provide feedback   ║
║                                                                  ║
║ {standard_state_tip}                                             ║
║                                                                  ║
║ Review checklist:                                                ║
║ • Code follows DESIGN_STATE constraints                          ║
║ • Acceptance criteria met                                        ║
║ • No security vulnerabilities                                    ║
║ • Self-test results verified                                     ║
╚══════════════════════════════════════════════════════════════════╝
```

---

## Implementation Notes

1. **Read DESIGN_STATE.yaml first** - Always read workflow_state section
2. **Display immediately** - Show state tip before doing anything else
3. **Update after actions** - Update workflow_state after completing actions
4. **Highlight blockers** - Make environment/blocker warnings prominent
