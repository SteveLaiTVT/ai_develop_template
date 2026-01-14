# EXTERNAL_REVIEW Template - Codex/Gemini → A Session

Template for external AI review (GPT Codex, Gemini, etc.) at version milestones.

---

## Input for External Reviewer

When requesting external review, provide:

```yaml
# EXTERNAL_REVIEW_REQUEST
request_id: "ER-XXX"
requested_at: "YYYY-MM-DDTHH:MM:SSZ"
version: "X.X.X"
iteration: "iter-XXX"

# What to review
scope:
  type: "version_candidate"  # version_candidate | security_audit | architecture_review
  modules:
    - "[Module 1]"
    - "[Module 2]"
  focus_areas:
    - "Global consistency"
    - "Engineering maintainability"
    - "Hidden risks"
    - "Security concerns"

# Context
context:
  design_state_url: "[Link to DESIGN_STATE.yaml]"
  codebase_url: "[Link to repository]"
  key_decisions:
    - "[Important design decision 1]"
    - "[Important design decision 2]"

# Constraints (External reviewer cannot change these)
immutable:
  - "Architecture pattern: [pattern]"
  - "Tech stack: [stack]"
  - "[Other fixed decisions]"

# Questions for reviewer
specific_questions:
  - "[Specific concern 1]"
  - "[Specific concern 2]"
```

---

## External Review Report Template

```yaml
# EXTERNAL_REVIEW_REPORT - External → A Session
report_id: "ERR-XXX"
reviewed_by: "GPT-5.2 Codex"  # or "Gemini 2.0"
reviewed_at: "YYYY-MM-DDTHH:MM:SSZ"
request_id: "ER-XXX"

# ============================================================
# Executive Summary
# ============================================================
summary:
  overall_assessment: "acceptable"  # excellent | acceptable | concerns | critical
  
  key_findings:
    - "[Finding 1]"
    - "[Finding 2]"
    
  recommendation: |
    [Overall recommendation]

# ============================================================
# Global Consistency Check
# ============================================================
consistency:
  status: "pass"  # pass | issues_found
  
  findings:
    - area: "[Area checked]"
      status: "consistent"
      notes: "[Notes]"
      
    - area: "[Another area]"
      status: "inconsistent"
      issue: "[What's inconsistent]"
      suggestion: "[How to fix]"

# ============================================================
# Engineering Maintainability
# ============================================================
maintainability:
  score: "B"  # A | B | C | D | F
  
  strengths:
    - "[What's good for maintainability]"
    
  concerns:
    - area: "[Area of concern]"
      issue: "[The issue]"
      impact: "[Why it matters]"
      suggestion: "[How to improve]"
      priority: "medium"

# ============================================================
# Hidden Risks
# ============================================================
risks:
  - id: "RISK-XXX"
    category: "technical"  # technical | security | scalability | operational
    severity: "medium"  # low | medium | high | critical
    
    description: |
      [Description of the risk]
      
    potential_impact: |
      [What could happen if not addressed]
      
    recommendation: |
      [How to mitigate]
      
    timeline: "before_production"  # immediate | next_iteration | before_production | backlog

# ============================================================
# Security Concerns
# ============================================================
security:
  overall: "acceptable"  # excellent | acceptable | concerns | critical
  
  findings:
    - category: "[Security category]"
      severity: "low"
      finding: "[What was found]"
      recommendation: "[How to address]"

# ============================================================
# Architecture Feedback
# ============================================================
architecture:
  # Note: External reviewer cannot change architecture, only suggest improvements
  
  alignment_with_design: "good"  # excellent | good | partial | poor
  
  observations:
    - "[Observation about architecture implementation]"
    
  suggestions:  # Non-binding suggestions
    - suggestion: "[Suggestion]"
      rationale: "[Why]"
      note: "A Session to decide if applicable"

# ============================================================
# Specific Questions Responses
# ============================================================
question_responses:
  - question: "[Original question]"
    answer: |
      [Answer to the question]
    recommendation: "[If any]"

# ============================================================
# Action Items for A Session
# ============================================================
action_items:
  must_address:
    - "[Critical item that must be addressed]"
    
  should_address:
    - "[Important item to address]"
    
  consider:
    - "[Optional improvement to consider]"

# ============================================================
# Appendix
# ============================================================
appendix:
  files_reviewed:
    - "[File 1]"
    - "[File 2]"
  tools_used:
    - "[Static analysis tool]"
  time_spent: "X hours"
```

---

## A Session Response Template

After receiving external review:

```yaml
# EXTERNAL_REVIEW_RESPONSE - A Session Decision
response_id: "ERR-XXX-RESP"
review_report_id: "ERR-XXX"
responded_at: "YYYY-MM-DDTHH:MM:SSZ"

# ============================================================
# Findings Assessment
# ============================================================
assessment:
  - finding_id: "RISK-001"
    accept: true
    action: "Create TASK-XXX to address"
    timeline: "iter-003"
    
  - finding_id: "RISK-002"
    accept: false
    reason: |
      [Why we're not accepting this finding]
      [e.g., Already addressed in another way]
    
  - finding_id: "SUGGEST-001"
    accept: "partial"
    action: |
      [What we'll do differently]

# ============================================================
# Design State Updates
# ============================================================
design_state_changes:
  - "[Change 1 to DESIGN_STATE based on review]"
  - "[Change 2]"

# ============================================================
# New Tasks Created
# ============================================================
new_tasks:
  - task_id: "TASK-XXX"
    title: "[Task to address finding]"
    priority: "P1"
    iteration: "iter-003"

# ============================================================
# Decision Log Entry
# ============================================================
decision_log:
  id: "ADR-XXX"
  title: "Response to External Review ERR-XXX"
  summary: |
    [Summary of decisions made based on external review]
```

---

## External Reviewer Guidelines

When using external AI (Codex/Gemini) for review:

### DO:
- Provide complete context (DESIGN_STATE + code)
- Ask specific questions
- Request structured output
- Focus on global/systemic issues

### DON'T:
- Let external reviewer change core architecture
- Accept suggestions without A Session evaluation
- Skip internal C Session review
- Use external review for every task

### When to Use External Review:
- Version milestone (feature complete)
- Before production release
- After major refactoring
- Security-sensitive changes
