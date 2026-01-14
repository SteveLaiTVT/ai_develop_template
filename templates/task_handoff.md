# TASK_HANDOFF Template - A → B Session

Copy this template, fill in the details, and pass it to B Session.

---

```yaml
# TASK_HANDOFF - A → B Session
handoff_id: "HO-XXX"
created_at: "YYYY-MM-DDTHH:MM:SSZ"
design_state_version: "X.X.X"

# ============================================================
# Objective
# ============================================================
objective: |
  [One or two sentences describing what this task should accomplish]

# ============================================================
# Context (Optional)
# ============================================================
context: |
  [Background information if necessary]
  [e.g., why this feature is needed, prerequisites]

# ============================================================
# Task List
# ============================================================
tasks:
  - id: "TASK-XXX"
    title: "[Task Title]"
    description: |
      [Detailed description of the task]
      [What to implement and how]
    
    files_to_create:
      - path: "[file path]"
        purpose: "[purpose of this file]"
    
    files_to_modify:
      - path: "[file path]"
        changes: "[what to change]"
    
    acceptance_criteria:
      - "[Criterion 1 - specific and verifiable]"
      - "[Criterion 2 - specific and verifiable]"
    
    constraints_to_follow:
      - "[Constraint 1]"
      - "[Constraint 2]"
    
    reference_files:
      - path: "[file path]"
        note: "[why reference this]"
    
    dependencies:
      - "TASK-YYY"
    
    estimated_effort: "low"  # low | medium | high

# ============================================================
# Skeleton Code (A Session provides structure with TODOs)
# ============================================================
skeleton_files:
  - path: "apps/backend/src/modules/xxx/xxx.service.ts"
    content: |
      /**
       * @file xxx.service.ts
       * @task TASK-XXX
       * @design_state_version X.X.X
       */
      
      // TODO(B): Import required dependencies
      
      export class XxxService {
        constructor(
          // TODO(B): Inject required dependencies
        ) {}
        
        // TODO(B): Implement method - should validate input and return result
        // Acceptance: Must handle edge case X
        // Constraint: Must not exceed 50 lines
        async doSomething(input: InputDto): Promise<OutputDto> {
          throw new Error('Not implemented');
        }
        
        // TODO(B): Implement helper method
        private helperMethod(): void {
          throw new Error('Not implemented');
        }
      }

# ============================================================
# Out of Scope
# ============================================================
out_of_scope:
  - "[What NOT to implement 1]"
  - "[What NOT to implement 2]"

# ============================================================
# Technical Constraints
# ============================================================
technical_constraints:
  general:
    - "No `any` type allowed"
    - "Single function < 50 lines"
    - "Single file < 300 lines"
  specific:
    - "[Task-specific constraint]"

# ============================================================
# API Specification (If applicable)
# ============================================================
api_spec:
  endpoints:
    - method: "POST"
      path: "/api/v1/xxx"
      request:
        body:
          field1: "string (required)"
          field2: "number (optional)"
      response:
        success: |
          { "success": true, "data": { ... } }
        error: |
          { "success": false, "error": { "code": "XXX_001", "message": "..." } }

# ============================================================
# Questions Contact
# ============================================================
questions_contact: |
  Return to A Session if:
  - Task description unclear
  - Technically impossible
  - Design flaw discovered
  - Changes needed beyond scope
```

---

## Template Usage Guide

### Skeleton Code Pattern

A Session provides the **structure** with TODO markers, B Session **fills in** the implementation:

```typescript
// A Session writes this skeleton:
export class AuthService {
  // TODO(B): Implement password hashing
  // - Use bcrypt with cost factor >= 12
  // - Handle empty password edge case
  async hashPassword(password: string): Promise<string> {
    throw new Error('Not implemented');
  }
  
  // TODO(B): Implement JWT token generation
  // - Algorithm: RS256
  // - Expiry: 15 minutes
  // - Include user.id and user.role in payload
  async generateToken(user: User): Promise<AuthTokens> {
    throw new Error('Not implemented');
  }
}

// B Session fills in:
export class AuthService {
  async hashPassword(password: string): Promise<string> {
    if (!password) {
      throw new BadRequestException('Password cannot be empty');
    }
    return bcrypt.hash(password, 12);
  }
  
  async generateToken(user: User): Promise<AuthTokens> {
    const payload = { sub: user.id, role: user.role };
    const accessToken = this.jwtService.sign(payload, {
      algorithm: 'RS256',
      expiresIn: '15m',
    });
    // ... rest of implementation
  }
}
```

### TODO Marker Format

```
// TODO(B): [Brief description]
// - [Requirement 1]
// - [Requirement 2]
// - Constraint: [Any constraint]
// - Acceptance: [What defines "done"]
```

### Field Descriptions

| Field | Description |
|-------|-------------|
| `objective` | What completing this task achieves |
| `skeleton_files` | Code structure with TODOs for B Session |
| `acceptance_criteria` | Specific, verifiable conditions |
| `out_of_scope` | Explicitly what NOT to do |
| `constraints_to_follow` | Rules B Session must follow |
