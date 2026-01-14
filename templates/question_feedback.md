# QUESTION_FEEDBACK Template - B → A Session

Use when B Session has questions or encounters blockers during implementation.

---

## Question Template (Non-blocking)

```yaml
# QUESTION_FEEDBACK - B → A Session
feedback_id: "QF-XXX"
created_at: "YYYY-MM-DDTHH:MM:SSZ"
task_id: "TASK-XXX"
blocker: false

# ============================================================
# Questions
# ============================================================
questions:
  - id: "Q1"
    type: "clarification"  # clarification | technical | scope | design
    question: "[Your question]"
    context: |
      [Why you're asking this]
      [What part of the task is affected]
    my_assumption: |
      [What you assume the answer is]
      [How you'll proceed if no response]
    need_confirmation: true  # true = wait for answer, false = proceed with assumption
    
  - id: "Q2"
    type: "scope"
    question: "[Another question]"
    context: "[Context]"
    my_assumption: "[Your assumption]"
    need_confirmation: false

# ============================================================
# Current Progress
# ============================================================
progress:
  completed:
    - "[What's already done]"
  in_progress:
    - "[What you're working on]"
  blocked_by_questions:
    - "[What's waiting for answers]"
```

---

## Blocker Template (Blocking)

```yaml
# BLOCKER_REPORT - B → A Session
report_id: "BR-XXX"
created_at: "YYYY-MM-DDTHH:MM:SSZ"
task_id: "TASK-XXX"
blocker: true
severity: "blocking"  # blocking | degraded

# ============================================================
# Blocker Description
# ============================================================
blocker:
  title: "[Brief title]"
  description: |
    [Detailed description of the blocker]
    [Why you cannot proceed]
    
  category: "technical"  # technical | dependency | design | resource

# ============================================================
# What Was Tried
# ============================================================
attempted_solutions:
  - attempt: "[What you tried]"
    result: "[Why it didn't work]"
    
  - attempt: "[Another attempt]"
    result: "[Result]"

# ============================================================
# Suggested Resolutions
# ============================================================
suggested_resolutions:
  - option: "[Option 1]"
    pros: "[Advantages]"
    cons: "[Disadvantages]"
    effort: "low"
    
  - option: "[Option 2]"
    pros: "[Advantages]"
    cons: "[Disadvantages]"
    effort: "medium"

# ============================================================
# Current Progress
# ============================================================
partial_progress:
  completed:
    - "[What's done]"
  blocked:
    - "[What's blocked]"
  can_continue_with:
    - "[What can be done in parallel while waiting]"

# ============================================================
# Impact Assessment
# ============================================================
impact:
  blocked_items:
    - "[What else is blocked by this]"
  timeline_impact: "[How this affects the iteration timeline]"
  workaround_available: false
```

---

## Question Types

| Type | When to Use | Example |
|------|-------------|---------|
| `clarification` | Task description unclear | "Should registration validate email domain?" |
| `technical` | Technical decision needed | "Should we use Redis or in-memory cache?" |
| `scope` | Boundary unclear | "Should this include password reset?" |
| `design` | Design seems flawed | "This approach may cause N+1 queries" |

---

## Examples

### Non-blocking Question

```yaml
feedback_id: "QF-001"
task_id: "TASK-001"
blocker: false

questions:
  - id: "Q1"
    type: "clarification"
    question: "Should registration require email verification before login?"
    context: |
      The acceptance criteria says "POST /api/v1/auth/register creates user"
      but doesn't mention email verification.
    my_assumption: |
      I'll allow login without email verification for MVP.
      Email verification can be added in a future iteration.
    need_confirmation: false
    
  - id: "Q2"
    type: "scope"
    question: "Should I implement rate limiting for login attempts?"
    context: |
      This is a security best practice but not in the acceptance criteria.
    my_assumption: |
      I won't implement it now, but will add a TODO comment
      and note it in the implementation report.
    need_confirmation: false

progress:
  completed:
    - "AuthController structure"
    - "DTO validation"
  in_progress:
    - "AuthService implementation"
  blocked_by_questions: []
```

### Blocking Issue

```yaml
report_id: "BR-001"
task_id: "TASK-001"
blocker: true
severity: "blocking"

blocker:
  title: "Cannot implement RS256 JWT signing"
  description: |
    The task requires RS256 algorithm for JWT signing,
    but no RSA key pair is configured in the project.
    I cannot generate keys without knowing the deployment strategy.
  category: "technical"

attempted_solutions:
  - attempt: "Tried to use HS256 instead"
    result: "Unsure if this meets security requirements"
    
  - attempt: "Tried to auto-generate keys"
    result: "Concerned about key management and rotation"

suggested_resolutions:
  - option: "Use HS256 for MVP, migrate to RS256 later"
    pros: "Unblocks development immediately"
    cons: "Technical debt, may need token migration"
    effort: "low"
    
  - option: "A Session provides key generation guide"
    pros: "Correct solution from the start"
    cons: "Needs infrastructure decision"
    effort: "medium"

partial_progress:
  completed:
    - "AuthController"
    - "AuthRepository"
    - "DTO validation"
  blocked:
    - "Token generation in AuthService"
  can_continue_with:
    - "Password hashing implementation"
    - "User creation logic"

impact:
  blocked_items:
    - "Login functionality"
    - "Protected route middleware"
  timeline_impact: "May delay iteration by 1-2 days"
  workaround_available: true  # Can use HS256 temporarily
```
