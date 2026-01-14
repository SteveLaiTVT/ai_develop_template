# DESIGN_STATE_CHANGELOG Template - A Session Internal

A Session uses this to document changes to DESIGN_STATE.yaml.

---

```yaml
# DESIGN_STATE CHANGELOG
changelog_id: "CL-XXX"
version_change: "X.X.X → X.X.Y"
changed_at: "YYYY-MM-DDTHH:MM:SSZ"
changed_by: "A Session"
trigger: "review_feedback"  # review_feedback | bug_report | new_requirement | design_decision

# ============================================================
# Summary
# ============================================================
summary: |
  [Brief description of what changed and why]

# ============================================================
# Changes Made
# ============================================================
changes:
  - section: "[DESIGN_STATE section path]"
    action: "added"  # added | modified | removed
    
    before: |
      [Previous content, if modified/removed]
      
    after: |
      [New content]
      
    rationale: |
      [Why this change was made]

  - section: "[Another section]"
    action: "modified"
    before: "[Previous value]"
    after: "[New value]"
    rationale: "[Reason]"

# ============================================================
# Impact Assessment
# ============================================================
impact:
  affected_modules:
    - "[Module 1]"
    - "[Module 2]"
    
  affected_tasks:
    - task_id: "TASK-XXX"
      impact: "[How this task is affected]"
      action_required: "[What needs to be done]"
      
  breaking_changes:
    - "[Any breaking changes]"
    
  migration_required: false
  migration_steps: []

# ============================================================
# Related Items
# ============================================================
related:
  review_report: "RR-XXX"  # If triggered by review
  bug_report: "BUG-XXX"    # If triggered by bug
  decision_record: "ADR-XXX"  # If new ADR created

# ============================================================
# Rollback Plan
# ============================================================
rollback:
  can_rollback: true
  steps:
    - "[How to rollback if needed]"
```

---

## Example: Change After Review Feedback

```yaml
changelog_id: "CL-003"
version_change: "0.1.0 → 0.1.1"
changed_at: "2026-01-14T14:30:00Z"
changed_by: "A Session"
trigger: "review_feedback"

summary: |
  Added constraint for Repository return types based on C Session review.
  Repository must return domain objects, not ORM entities.

changes:
  - section: "code_constraints.backend"
    action: "added"
    after: |
      - "Repository must return domain objects, not ORM entities"
    rationale: |
      C Session review (RR-001) identified that returning ORM entities
      from Repository leaks implementation details to Service layer.

impact:
  affected_modules:
    - "auth"
  affected_tasks:
    - task_id: "TASK-001"
      impact: "auth.repository.ts needs to map entities to domain objects"
      action_required: "B Session fix in next iteration"
  breaking_changes:
    - "Existing Repository methods need to be updated"
  migration_required: false

related:
  review_report: "RR-001"
  decision_record: "ADR-003"

rollback:
  can_rollback: true
  steps:
    - "Remove the constraint from code_constraints.backend"
    - "Revert Repository implementations"
```
