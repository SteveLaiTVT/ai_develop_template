# IMPLEMENTATION_REPORT Template - B → C Session

B Session fills this after completing implementation, passes to C Session for review.

---

```yaml
# IMPLEMENTATION_REPORT - B → C Session
report_id: "IR-XXX"
created_at: "YYYY-MM-DDTHH:MM:SSZ"
task_id: "TASK-XXX"
design_state_version: "X.X.X"

# ============================================================
# Implementation Summary
# ============================================================
summary: |
  [Brief description of what was implemented]
  [Key decisions made during implementation]

# ============================================================
# Files Changed
# ============================================================
files_changed:
  - path: "[file path]"
    action: "created"  # created | modified | deleted
    lines: 0
    description: "[what this file does]"
    
  - path: "[file path]"
    action: "modified"
    lines_added: 0
    lines_removed: 0
    description: "[what was changed]"

# ============================================================
# Acceptance Criteria Self-Check
# ============================================================
acceptance_checklist:
  - criterion: "[Acceptance criterion from task]"
    status: "pass"  # pass | fail | partial
    evidence: "[where/how this is implemented]"
    
  - criterion: "[Another criterion]"
    status: "pass"
    evidence: "[location in code]"

# ============================================================
# Constraints Compliance
# ============================================================
constraints_compliance:
  - constraint: "[Constraint from task]"
    status: "compliant"  # compliant | violation | partial
    note: "[explanation if needed]"

# ============================================================
# TODOs Completed
# ============================================================
todos_completed:
  - file: "[file path]"
    todo: "TODO(B): [original TODO text]"
    implementation: "[brief description of how it was implemented]"
    lines: "23-45"

# ============================================================
# Implementation Decisions
# ============================================================
implementation_decisions:
  - topic: "[Decision topic]"
    choice: "[What was chosen]"
    reason: "[Why this choice was made]"
    alternatives_considered:
      - "[Alternative 1]"
      - "[Alternative 2]"

# ============================================================
# Known Issues / Technical Debt
# ============================================================
known_issues:
  - severity: "low"  # low | medium | high
    description: "[Issue description]"
    suggestion: "[How to address in future]"
    
# ============================================================
# Questions for A Session
# ============================================================
questions_for_a:
  - "[Question about design or scope]"
  - "[Suggestion for future improvement]"

# ============================================================
# Test Results (If tests were written)
# ============================================================
test_results:
  unit_tests:
    total: 0
    passed: 0
    failed: 0
    coverage: "0%"
  notes: "[Any notes about testing]"

# ============================================================
# Diff Summary (For modified files)
# ============================================================
diff_summary:
  - file: "[file path]"
    changes: "+XX -YY"
    key_modifications:
      - "[What was added/changed]"
```

---

## How to Fill This Template

### summary
Brief overview of what was done:
```yaml
summary: |
  Implemented user authentication service with:
  - Password hashing using bcrypt (cost factor 12)
  - JWT token generation with RS256
  - Refresh token rotation
```

### acceptance_checklist
Reference the original task's acceptance criteria:
```yaml
acceptance_checklist:
  - criterion: "POST /api/v1/auth/login returns JWT token"
    status: "pass"
    evidence: "See auth.service.ts:67-89, generateTokens() method"
```

### todos_completed
Map completed TODOs from the skeleton:
```yaml
todos_completed:
  - file: "apps/backend/src/modules/auth/auth.service.ts"
    todo: "TODO(B): Implement password hashing using bcrypt"
    implementation: "Used bcrypt.hash() with cost factor 12"
    lines: "34-38"
```

### implementation_decisions
Document choices made within the allowed scope:
```yaml
implementation_decisions:
  - topic: "Error message format"
    choice: "Use error codes with human-readable messages"
    reason: "Easier for frontend to handle and i18n ready"
    alternatives_considered:
      - "Plain text messages only"
      - "Numeric error codes only"
```

### known_issues
Be honest about technical debt:
```yaml
known_issues:
  - severity: "low"
    description: "Error messages are hardcoded strings"
    suggestion: "Future: Extract to constants file for i18n"
```
