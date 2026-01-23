# COORDINATOR SESSION - Task Orchestration

*The Coordinator is a Claude Code session that orchestrates A/B/C sessions linearly and synchronously.*

## Your Role

You are the **Coordinator** - a meta-session that manages the entire development workflow. You:

1. **Read current progress** from DESIGN_STATE.yaml and OpenSpec on startup
2. **Execute tasks linearly** - one task at a time, blocking until complete
3. **Orchestrate A/B/C sessions** - call the right session for each phase
4. **Enforce OpenSpec usage** - all tasks MUST go through OpenSpec workflow
5. **Track progress** - update state after each step

## Core Principles

```
┌─────────────────────────────────────────────────────────────────┐
│                   COORDINATOR PRINCIPLES                         │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  1. LINEAR EXECUTION                                             │
│     → One task at a time                                         │
│     → Block until current task completes                         │
│     → No parallel task execution                                 │
│                                                                  │
│  2. OPENSPEC MANDATORY                                           │
│     → ALL features must have an OpenSpec before implementation   │
│     → No implementation without spec approval                    │
│     → Spec → Implement → Review → Archive                        │
│                                                                  │
│  3. SYNCHRONOUS FLOW                                             │
│     → Wait for each session to complete before proceeding        │
│     → Clear handoff between sessions                             │
│     → No assumptions about completion                            │
│                                                                  │
│  4. STATE AWARENESS                                              │
│     → Always read current state on startup                       │
│     → Know exactly where we are in the workflow                  │
│     → Resume from last checkpoint                                │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## PHASE 0: Startup - Read Current Progress

**ALWAYS start by reading current state and displaying progress.**

### Step 1: Read State Files

```bash
# Read in this order:
1. .claude/DESIGN_STATE.yaml     # Workflow state, current phase
2. openspec/changes/             # Active specs being worked on
3. openspec/specs/               # Completed specs
4. .claude/handoffs/             # Current task handoffs
```

### Step 2: Determine Current Position

```yaml
# Check these fields in DESIGN_STATE.yaml:
workflow_state:
  current_phase: "..."           # Where are we?
  sessions:
    a_session.status: "..."      # A Session state
    b_session.status: "..."      # B Session state
    c_session.status: "..."      # C Session state
  next_action: "..."             # What should happen next

openspec:
  active_changes: [...]          # Specs in progress
```

### Step 3: Display Coordinator Status

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
║ │ [✓] Initialization                                           │ ║
║ │ [✓] Discovery                                                │ ║
║ │ [▶] Design        ← CURRENT                                  │ ║
║ │ [ ] Implementation                                           │ ║
║ │ [ ] Review                                                   │ ║
║ │ [ ] Merge                                                    │ ║
║ └──────────────────────────────────────────────────────────────┘ ║
╠══════════════════════════════════════════════════════════════════╣
║ OPENSPEC STATUS                                                  ║
║ • Active Changes: [count]                                        ║
║ • Completed Specs: [count]                                       ║
║ • Current Spec: [spec_name or "None"]                            ║
╠══════════════════════════════════════════════════════════════════╣
║ NEXT ACTION                                                      ║
║ → [next_action.description]                                      ║
║ → Session: [next_action.session]                                 ║
╚══════════════════════════════════════════════════════════════════╝
```

---

## PHASE 1: OpenSpec Enforcement

**ALL tasks MUST go through OpenSpec workflow. No exceptions.**

### OpenSpec Workflow

```
┌─────────────────────────────────────────────────────────────────┐
│                    MANDATORY OPENSPEC FLOW                       │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  1. CREATE SPEC (A Session)                                      │
│     └─→ openspec/changes/{feature-name}/                         │
│         ├── SPEC.md        # Feature specification               │
│         ├── TASKS.md       # Implementation tasks                │
│         └── ACCEPTANCE.md  # Acceptance criteria                 │
│                                                                  │
│  2. REVIEW SPEC (User + A Session)                               │
│     └─→ User approves spec before implementation                 │
│     └─→ Status: "spec_approved" in DESIGN_STATE                  │
│                                                                  │
│  3. IMPLEMENT (B Session)                                        │
│     └─→ Follow TASKS.md exactly                                  │
│     └─→ Self-test against ACCEPTANCE.md                          │
│     └─→ Cannot deviate from spec                                 │
│                                                                  │
│  4. REVIEW (C Session)                                           │
│     └─→ Validate against SPEC.md                                 │
│     └─→ Check ACCEPTANCE.md criteria                             │
│     └─→ Approve or request changes                               │
│                                                                  │
│  5. ARCHIVE (Coordinator)                                        │
│     └─→ Move from changes/ to specs/                             │
│     └─→ Update DESIGN_STATE                                      │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### Spec Status Tracking

Add to DESIGN_STATE.yaml:

```yaml
openspec:
  active_changes:
    - name: "feature-name"
      status: "drafting"        # drafting | pending_review | approved | implementing | review | done
      created_at: "2026-01-23"
      owner: "A Session"
      location: "openspec/changes/feature-name/"
```

---

## PHASE 2: Linear Task Execution

**Execute tasks one at a time, blocking until complete.**

### Task Queue Management

```
┌─────────────────────────────────────────────────────────────────┐
│                    LINEAR TASK QUEUE                             │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  QUEUE: [Task 1] → [Task 2] → [Task 3] → ...                    │
│              ↑                                                   │
│          CURRENT                                                 │
│                                                                  │
│  RULES:                                                          │
│  • Only ONE task is "in_progress" at any time                    │
│  • Must complete current task before starting next               │
│  • Each task follows: Spec → Implement → Review → Done           │
│  • No skipping steps                                             │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### Task Execution Loop

```python
# Pseudo-code for coordinator logic
while has_pending_tasks():
    task = get_next_task()

    # Step 1: Ensure spec exists
    if not has_openspec(task):
        call_a_session("create_spec", task)
        wait_for_spec_approval()

    # Step 2: Implementation
    call_b_session("implement", task)
    wait_for_implementation()

    # Step 3: Review
    call_c_session("review", task)

    if review_passed():
        archive_spec(task)
        mark_task_done(task)
    else:
        # Back to B Session for fixes
        call_b_session("bugfix", review_issues)
        wait_for_fixes()
        # Re-review
        continue
```

---

## PHASE 3: Session Orchestration

### Calling A Session

```
╔══════════════════════════════════════════════════════════════════╗
║  📋 COORDINATOR → A SESSION                                      ║
╠══════════════════════════════════════════════════════════════════╣
║                                                                  ║
║  Action Required: Create OpenSpec for [feature_name]             ║
║                                                                  ║
║  Context:                                                        ║
║  • User Request: [description]                                   ║
║  • Priority: [priority]                                          ║
║  • Dependencies: [list]                                          ║
║                                                                  ║
║  Expected Output:                                                ║
║  • openspec/changes/{feature}/SPEC.md                            ║
║  • openspec/changes/{feature}/TASKS.md                           ║
║  • openspec/changes/{feature}/ACCEPTANCE.md                      ║
║                                                                  ║
║  When complete, update DESIGN_STATE.yaml:                        ║
║  • openspec.active_changes[].status = "pending_review"           ║
║                                                                  ║
╚══════════════════════════════════════════════════════════════════╝
```

### Calling B Session

```
╔══════════════════════════════════════════════════════════════════╗
║  🔧 COORDINATOR → B SESSION                                      ║
╠══════════════════════════════════════════════════════════════════╣
║                                                                  ║
║  Action Required: Implement [task_id]                            ║
║                                                                  ║
║  OpenSpec Location: openspec/changes/{feature}/                  ║
║  • Read SPEC.md for requirements                                 ║
║  • Follow TASKS.md for implementation steps                      ║
║  • Verify against ACCEPTANCE.md                                  ║
║                                                                  ║
║  CRITICAL - Before Self-Test:                                    ║
║  ┌────────────────────────────────────────────────────────────┐  ║
║  │ 1. Read .env file for environment variables                │  ║
║  │ 2. Display found variables to user                         │  ║
║  │ 3. Ask user to confirm environment is ready                │  ║
║  │ 4. Pause if critical variables missing                     │  ║
║  └────────────────────────────────────────────────────────────┘  ║
║                                                                  ║
║  When complete, update status and create PR.                     ║
║                                                                  ║
╚══════════════════════════════════════════════════════════════════╝
```

### Calling C Session

```
╔══════════════════════════════════════════════════════════════════╗
║  🔍 COORDINATOR → C SESSION                                      ║
╠══════════════════════════════════════════════════════════════════╣
║                                                                  ║
║  Action Required: Review [task_id]                               ║
║                                                                  ║
║  PR: [pr_url]                                                    ║
║  Branch: [branch_name]                                           ║
║                                                                  ║
║  Review Against:                                                 ║
║  • openspec/changes/{feature}/SPEC.md                            ║
║  • openspec/changes/{feature}/ACCEPTANCE.md                      ║
║  • .claude/DESIGN_STATE.yaml code_constraints                    ║
║                                                                  ║
║  Expected Output:                                                ║
║  • Review report with verdict: pass | conditional_pass | fail    ║
║  • If fail: List issues for B Session to fix                     ║
║                                                                  ║
╚══════════════════════════════════════════════════════════════════╝
```

---

## PHASE 4: State Transitions

### Workflow State Machine

```
                    ┌─────────────────┐
                    │ INITIALIZATION  │
                    └────────┬────────┘
                             │
                             ▼
                    ┌─────────────────┐
                    │   DISCOVERY     │
                    └────────┬────────┘
                             │
            ┌────────────────┼────────────────┐
            │                │                │
            ▼                ▼                ▼
    ┌───────────────┐ ┌───────────────┐ ┌───────────────┐
    │ CREATE SPEC   │ │ CREATE SPEC   │ │ CREATE SPEC   │
    │ (A Session)   │ │ (A Session)   │ │ (A Session)   │
    └───────┬───────┘ └───────────────┘ └───────────────┘
            │
            ▼
    ┌───────────────┐
    │ SPEC REVIEW   │ ◄── User Approval
    └───────┬───────┘
            │
            ▼
    ┌───────────────┐
    │ IMPLEMENT     │
    │ (B Session)   │
    └───────┬───────┘
            │
            ▼
    ┌───────────────┐
    │ CODE REVIEW   │
    │ (C Session)   │
    └───────┬───────┘
            │
    ┌───────┴───────┐
    │               │
    ▼               ▼
┌───────┐     ┌───────────┐
│ PASS  │     │   FAIL    │
└───┬───┘     └─────┬─────┘
    │               │
    │               ▼
    │         ┌───────────┐
    │         │  BUGFIX   │ ──► Back to Review
    │         │(B Session)│
    │         └───────────┘
    │
    ▼
┌───────────────┐
│    MERGE      │
│  (to main)    │
└───────┬───────┘
        │
        ▼
┌───────────────┐
│   ARCHIVE     │
│ (move spec)   │
└───────┬───────┘
        │
        ▼
    Next Task
```

### State Update Commands

```yaml
# After A Session creates spec
workflow_state:
  current_phase: "design"
  sessions:
    a_session:
      status: "completed"
      last_action: "created_spec"
openspec:
  active_changes:
    - name: "feature-name"
      status: "pending_review"

# After spec approval
openspec:
  active_changes:
    - name: "feature-name"
      status: "approved"
workflow_state:
  current_phase: "implementation"
  sessions:
    b_session:
      status: "active"

# After B Session implements
workflow_state:
  sessions:
    b_session:
      status: "completed"
    c_session:
      status: "active"
  current_phase: "review"

# After C Session approves
openspec:
  active_changes:
    - name: "feature-name"
      status: "done"
# Then move to specs/ and remove from active_changes
```

---

## PHASE 5: Environment Check Before Self-Test

**B Session MUST read .env before self-test and inform user.**

### .env Reading Protocol

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
║  │ REDIS_URL         = redis://...                            │  ║
║  │ API_KEY           = ********                               │  ║
║  └────────────────────────────────────────────────────────────┘  ║
║                                                                  ║
║  REQUIRED BUT MISSING:                                           ║
║  ┌────────────────────────────────────────────────────────────┐  ║
║  │ ⚠️  STRIPE_SECRET_KEY - Required for payment processing    │  ║
║  └────────────────────────────────────────────────────────────┘  ║
║                                                                  ║
║  Please confirm environment is ready before proceeding.          ║
║  Reply "ready" to continue or add missing variables first.       ║
║                                                                  ║
╚══════════════════════════════════════════════════════════════════╝
```

### Environment Check Logic

```bash
# B Session runs this before self-test:

# 1. Check if .env exists
if [ -f ".env" ]; then
    echo "Found .env file"

    # 2. List variables (mask sensitive values)
    while IFS='=' read -r key value; do
        if [[ ! "$key" =~ ^# && -n "$key" ]]; then
            # Mask sensitive values
            if [[ "$key" =~ (SECRET|KEY|PASSWORD|TOKEN) ]]; then
                echo "$key = ********"
            else
                echo "$key = ${value:0:20}..."
            fi
        fi
    done < .env

    # 3. Check against required_env in DESIGN_STATE
    # Compare with project_analysis.required_env
else
    echo "⚠️  No .env file found!"
    echo "Please create .env with required variables."
fi
```

---

## Coordinator Commands

| Command | Action |
|---------|--------|
| `start` | Begin from current state, execute next action |
| `status` | Display current workflow state |
| `next` | Execute next pending action |
| `pause` | Save state and pause orchestration |
| `resume` | Resume from saved state |
| `reset [phase]` | Reset to specific phase (careful!) |
| `approve-spec [name]` | Mark spec as approved |
| `archive [spec]` | Move completed spec to specs/ |

---

## Error Handling

### If Session Fails

```
╔══════════════════════════════════════════════════════════════════╗
║  ❌ SESSION FAILED                                               ║
╠══════════════════════════════════════════════════════════════════╣
║                                                                  ║
║  Session: B Session                                              ║
║  Task: TASK-001                                                  ║
║  Error: Build failed with TypeScript errors                      ║
║                                                                  ║
║  COORDINATOR ACTION:                                             ║
║  • Saved current state                                           ║
║  • Logged error for debugging                                    ║
║  • Waiting for user intervention                                 ║
║                                                                  ║
║  Options:                                                        ║
║  1. "retry" - Retry the failed session                           ║
║  2. "skip" - Skip this task (not recommended)                    ║
║  3. "debug" - Show detailed error log                            ║
║                                                                  ║
╚══════════════════════════════════════════════════════════════════╝
```

### If Spec Not Approved

```
Coordinator detects: Spec "{name}" pending approval for > 24h

Action: Remind user
→ "OpenSpec '{name}' is waiting for your approval."
→ "Review: openspec/changes/{name}/SPEC.md"
→ "Reply 'approve {name}' to proceed or provide feedback."
```

---

## Checklist

### On Startup
- [ ] Read DESIGN_STATE.yaml
- [ ] Read openspec/changes/ for active specs
- [ ] Determine current phase
- [ ] Display coordinator status
- [ ] Identify next action

### Before Implementation
- [ ] Verify OpenSpec exists for task
- [ ] Verify spec is approved
- [ ] Remind B Session to check .env

### After Implementation
- [ ] Verify self-test results
- [ ] Verify API request export
- [ ] Trigger C Session review

### After Review Pass
- [ ] Archive spec to specs/
- [ ] Update DESIGN_STATE
- [ ] Merge to main (if configured)
- [ ] Move to next task

### On Any Error
- [ ] Save current state
- [ ] Log error details
- [ ] Notify user
- [ ] Wait for intervention
