# REVIEW_REPORT Template - C → A Session

C Session fills this after reviewing B Session's implementation.

---

```yaml
# REVIEW_REPORT - C → A Session
report_id: "RR-XXX"
created_at: "YYYY-MM-DDTHH:MM:SSZ"
task_id: "TASK-XXX"
implementation_report_id: "IR-XXX"

# ============================================================
# Summary
# ============================================================
summary:
  verdict: "pass"  # pass | conditional_pass | fail
  
  statistics:
    files_reviewed: 0
    total_lines: 0
    issues_found: 0
    critical_issues: 0
    errors: 0
    warnings: 0
    suggestions: 0
    
  brief: |
    [One or two sentences summarizing the review result]

# ============================================================
# Constraints Check
# ============================================================
constraints_check:
  - constraint: "[Constraint from DESIGN_STATE]"
    status: "pass"  # pass | fail | warning | not_applicable
    
  - constraint: "[Another constraint]"
    status: "fail"
    violation:
      file: "[file path]"
      location: "[line number or function name]"
      detail: "[what specifically violated the constraint]"

# ============================================================
# Acceptance Criteria Verification
# ============================================================
acceptance_check:
  - criterion: "[Criterion from task]"
    status: "pass"  # pass | fail | partial
    verification: "[how it was verified]"
    
  - criterion: "[Another criterion]"
    status: "fail"
    reason: "[why it failed]"

# ============================================================
# Issues List (Sorted by severity)
# ============================================================
issues:
  # --- Critical (Blocks release) ---
  - id: "ISS-XXX"
    severity: "critical"
    type: "security"  # See issue types below
    title: "[Issue title]"
    
    location:
      file: "[file path]"
      lines: "[line range]"
      function: "[function name]"
      
    description: |
      [Detailed description of the issue]
      
    evidence: |
      ```typescript
      [Code snippet showing the problem]
      ```
      
    recommendation: |
      [How to fix it]
      ```typescript
      [Fixed code example]
      ```
      
    effort: "low"  # low | medium | high
    related_constraint: "[if constraint violation]"

  # --- Error (Must fix) ---
  - id: "ISS-XXX"
    severity: "error"
    type: "constraint_violation"
    title: "[Issue title]"
    location:
      file: "[file path]"
      lines: "[line range]"
    description: "[Description]"
    recommendation: "[How to fix]"
    effort: "low"

  # --- Warning (Should fix) ---
  - id: "ISS-XXX"
    severity: "warning"
    type: "quality"
    title: "[Issue title]"
    location:
      file: "[file path]"
      lines: "[line range]"
    description: "[Description]"
    recommendation: "[How to fix]"
    effort: "low"

  # --- Suggestion (Optional) ---
  - id: "ISS-XXX"
    severity: "suggestion"
    type: "performance"
    title: "[Issue title]"
    description: "[Description]"
    recommendation: "[How to improve]"
    effort: "low"

# ============================================================
# Positive Feedback
# ============================================================
positives:
  - "[What was done well 1]"
  - "[What was done well 2]"

# ============================================================
# Responses to B Session Questions
# ============================================================
responses_to_b:
  - question: "[Question B Session asked]"
    answer: "[Answer or redirect to A Session]"

# ============================================================
# Recommendations for A Session
# ============================================================
recommendations_for_a:
  - type: "design_gap"  # design_gap | scope | process
    description: "[Recommendation details]"

# ============================================================
# Conclusion
# ============================================================
conclusion:
  can_merge: true  # true | false
  
  blocking_issues:
    - "ISS-XXX: [Brief description]"
  
  required_before_merge:
    - "[Must do before merge]"
  
  recommended_before_merge:
    - "[Should do before merge]"
  
  next_steps:
    - "[Next step 1]"
    - "[Next step 2]"
```

---

## Issue Types Reference

| Type | Description | Example |
|------|-------------|---------|
| `constraint_violation` | Violates DESIGN_STATE constraint | Function > 50 lines |
| `acceptance_fail` | Doesn't meet acceptance criteria | API missing required field |
| `security` | Security vulnerability | SQL injection, XSS |
| `performance` | Performance problem | N+1 query, memory leak |
| `error_handling` | Error handling issue | Uncaught exception |
| `quality` | Code quality problem | Duplicate code, unclear naming |
| `consistency` | Inconsistency issue | Style mismatch |
| `logic` | Logic error | Unhandled edge case |

## Severity Guide

| Severity | Definition | Action |
|----------|------------|--------|
| **critical** | Security hole, data loss risk, system crash | Block merge |
| **error** | Constraint violation, missing functionality | Must fix |
| **warning** | Potential issue, maintainability concern | Should fix |
| **suggestion** | Nice to have, optimization | Optional |

## Verdict Definitions

| Verdict | Meaning | Condition |
|---------|---------|-----------|
| `pass` | Ready to merge | No issues or only suggestions |
| `conditional_pass` | Merge after fixes | Has warnings/errors but fixable |
| `fail` | Needs rework | Has critical issues or design flaws |
