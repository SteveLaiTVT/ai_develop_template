# Coordinator Task Template

*Template for linear task orchestration with mandatory OpenSpec workflow.*

---

## Task Orchestration Record

```yaml
# ============================================================
# COORDINATOR TASK RECORD
# ============================================================
# This file tracks the current task being orchestrated
# by the Coordinator session.
# ============================================================

task_id: "TASK-XXX"
created_at: "YYYY-MM-DD HH:MM"
feature_name: "[Feature Name]"
requested_by: "user"

# Current execution state
state:
  current_step: "spec_creation"   # spec_creation | spec_review | implementation | code_review | merge | archive
  started_at: "YYYY-MM-DD HH:MM"
  last_updated: "YYYY-MM-DD HH:MM"

# OpenSpec tracking (MANDATORY)
openspec:
  location: "openspec/changes/[feature-name]/"
  status: "drafting"              # drafting | pending_review | approved | implementing | code_review | done
  files:
    spec: "SPEC.md"               # Created: true/false
    tasks: "TASKS.md"             # Created: true/false
    acceptance: "ACCEPTANCE.md"   # Created: true/false
  approved_at: null
  approved_by: null

# Session execution log
execution_log:
  - step: "spec_creation"
    session: "A Session"
    status: "pending"             # pending | in_progress | completed | failed
    started_at: null
    completed_at: null
    notes: ""

  - step: "spec_review"
    session: "User"
    status: "pending"
    started_at: null
    completed_at: null
    notes: ""

  - step: "implementation"
    session: "B Session"
    status: "pending"
    started_at: null
    completed_at: null
    env_check_completed: false
    self_test_passed: false
    pr_created: false
    pr_url: null
    notes: ""

  - step: "code_review"
    session: "C Session"
    status: "pending"
    started_at: null
    completed_at: null
    verdict: null                 # pass | conditional_pass | fail
    issues_count: 0
    notes: ""

  - step: "merge"
    session: "Coordinator"
    status: "pending"
    started_at: null
    completed_at: null
    merged_to: "main"
    notes: ""

  - step: "archive"
    session: "Coordinator"
    status: "pending"
    started_at: null
    completed_at: null
    archived_to: "openspec/specs/[feature-name]/"
    notes: ""

# Bugfix iterations (if any)
bugfix_iterations: []
  # - iteration: 1
  #   issues_from: "C Session"
  #   issues_count: 2
  #   fixed_at: "YYYY-MM-DD"
  #   re_review_verdict: "pass"

# Final status
completed: false
completed_at: null
total_duration: null
```

---

## Usage

### Creating a New Task

When user requests a new feature, Coordinator creates this record:

```yaml
task_id: "TASK-005"
created_at: "2026-01-23 10:00"
feature_name: "user-profile"
requested_by: "user"

state:
  current_step: "spec_creation"
  started_at: "2026-01-23 10:00"

openspec:
  location: "openspec/changes/user-profile/"
  status: "drafting"
```

### Transitioning Steps

Coordinator updates the record as each step completes:

**After A Session creates spec:**
```yaml
state:
  current_step: "spec_review"

openspec:
  status: "pending_review"
  files:
    spec: "SPEC.md"       # Created: true
    tasks: "TASKS.md"     # Created: true
    acceptance: "ACCEPTANCE.md"  # Created: true

execution_log:
  - step: "spec_creation"
    status: "completed"
    completed_at: "2026-01-23 10:30"
```

**After user approves spec:**
```yaml
state:
  current_step: "implementation"

openspec:
  status: "approved"
  approved_at: "2026-01-23 11:00"
  approved_by: "user"

execution_log:
  - step: "spec_review"
    status: "completed"
    completed_at: "2026-01-23 11:00"
    notes: "User approved spec"
```

**After B Session implements:**
```yaml
state:
  current_step: "code_review"

openspec:
  status: "code_review"

execution_log:
  - step: "implementation"
    status: "completed"
    completed_at: "2026-01-23 14:00"
    env_check_completed: true
    self_test_passed: true
    pr_created: true
    pr_url: "https://github.com/user/repo/pull/123"
```

**After C Session approves:**
```yaml
state:
  current_step: "merge"

execution_log:
  - step: "code_review"
    status: "completed"
    verdict: "pass"
    notes: "All acceptance criteria met"
```

---

## Coordinator Commands

### Display Current Task Status

```
╔══════════════════════════════════════════════════════════════════╗
║  📋 CURRENT TASK: TASK-005 - user-profile                        ║
╠══════════════════════════════════════════════════════════════════╣
║                                                                  ║
║  PROGRESS:                                                       ║
║  ┌──────────────────────────────────────────────────────────────┐║
║  │ [✅] Spec Creation    - Completed 2026-01-23 10:30           │║
║  │ [✅] Spec Review      - Approved by user                     │║
║  │ [▶️] Implementation   - B Session working...                  │║
║  │ [ ] Code Review                                              │║
║  │ [ ] Merge                                                    │║
║  │ [ ] Archive                                                  │║
║  └──────────────────────────────────────────────────────────────┘║
║                                                                  ║
║  OpenSpec: openspec/changes/user-profile/                        ║
║  Status: implementing                                            ║
║                                                                  ║
╚══════════════════════════════════════════════════════════════════╝
```

### Handle Failures

If a step fails:

```yaml
execution_log:
  - step: "implementation"
    status: "failed"
    error: "Build failed - TypeScript errors"
    notes: "B Session encountered errors, waiting for resolution"

state:
  current_step: "implementation"  # Stays on failed step
```

Coordinator displays:

```
╔══════════════════════════════════════════════════════════════════╗
║  ❌ TASK BLOCKED: TASK-005                                       ║
╠══════════════════════════════════════════════════════════════════╣
║                                                                  ║
║  Step: Implementation                                            ║
║  Error: Build failed - TypeScript errors                         ║
║                                                                  ║
║  Options:                                                        ║
║  • "retry" - Retry the implementation step                       ║
║  • "debug" - Show detailed error log                             ║
║  • "help"  - Request A Session assistance                        ║
║                                                                  ║
╚══════════════════════════════════════════════════════════════════╝
```

---

## Task Queue Management

When multiple features are requested:

```yaml
# In DESIGN_STATE.yaml
workflow_state:
  coordinator:
    current_task: "TASK-005"
    task_queue:
      - task_id: "TASK-006"
        feature: "payment-integration"
        requested_at: "2026-01-23 15:00"
      - task_id: "TASK-007"
        feature: "email-notifications"
        requested_at: "2026-01-23 15:30"
```

Display:

```
╔══════════════════════════════════════════════════════════════════╗
║  📋 TASK QUEUE                                                   ║
╠══════════════════════════════════════════════════════════════════╣
║                                                                  ║
║  ▶ CURRENT: TASK-005 - user-profile (implementing)               ║
║                                                                  ║
║  QUEUED:                                                         ║
║  1. TASK-006 - payment-integration (waiting)                     ║
║  2. TASK-007 - email-notifications (waiting)                     ║
║                                                                  ║
║  Note: Tasks are executed linearly. TASK-006 will start          ║
║  only after TASK-005 is fully completed (merged & archived).     ║
║                                                                  ║
╚══════════════════════════════════════════════════════════════════╝
```

---

## Archiving Completed Tasks

When a task is fully complete:

1. Move OpenSpec from `changes/` to `specs/`:
   ```bash
   mv openspec/changes/user-profile/ openspec/specs/user-profile/
   ```

2. Update DESIGN_STATE.yaml:
   ```yaml
   openspec:
     active_changes: []  # Remove completed spec
   ```

3. Mark task as completed:
   ```yaml
   completed: true
   completed_at: "2026-01-23 16:00"
   total_duration: "6 hours"
   ```

4. Move to next task in queue (if any).
