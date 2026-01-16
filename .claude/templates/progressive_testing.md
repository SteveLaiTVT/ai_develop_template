# Progressive Testing Strategy

## Philosophy

**Self-test is core development practice. Add automated tests progressively as the project matures.**

Self-testing isn't just for "early stage" projects - it's HOW developers work at every stage:
- Write code → Run it → Verify it works → Iterate

Traditional approaches mandate E2E tests from day one, which leads to:
- Time spent on tests that break as requirements change
- False confidence from tests that don't catch real bugs
- Delayed delivery without proportional quality improvement

This template uses a **progressive testing approach**:
- Self-test: ALWAYS (it's part of development)
- Unit tests: Add progressively for critical logic
- E2E tests: Add when features stabilize

---

## Testing Levels

### Level 1: Self-Test (Core Development Practice)

**This is HOW you develop, not just how you test.**

Self-test happens at EVERY stage of every project. It's the fundamental development loop:

```
┌─────────────────────────────────────────────────────────────────┐
│ SELF-TEST CHECKLIST                                             │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│ Backend:                                                        │
│ ✓ Server starts without errors                                  │
│ ✓ API endpoints respond correctly (test with curl)              │
│ ✓ Database operations work (create, read, update, delete)       │
│ ✓ Error cases return proper error codes                         │
│                                                                 │
│ Frontend:                                                       │
│ ✓ Dev server starts without build errors                        │
│ ✓ Main pages load (no console errors)                           │
│ ✓ Core user flows work (login, main feature)                    │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

**Why this is essential**:
- Catches 80% of bugs with minimal effort
- Proves the code actually runs
- No test infrastructure required
- Can be done in minutes

**Example Backend Self-Test**:
```bash
# Start server
npm run start:dev

# Test endpoints
curl -X POST http://localhost:3000/api/auth/register \
  -H "Content-Type: application/json" \
  -d '{"email":"test@example.com","password":"Test123!"}'

# Expected: 201 with user data
# Document: "Registration endpoint returns 201, user created successfully"
```

---

### Level 2: Unit Tests (RECOMMENDED)

**When to add**:
- Critical business logic (password hashing, token validation)
- Complex calculations or transformations
- After fixing a bug (regression prevention)
- When requirements stabilize

**What to prioritize**:
```
HIGH PRIORITY (Test First)          LOW PRIORITY (Test Later)
─────────────────────────           ─────────────────────────
✓ Security functions                ○ Simple CRUD operations
✓ Business rule validation          ○ Straightforward mappings
✓ Error handling edge cases         ○ Configuration loading
✓ State transitions                 ○ Logging calls
```

**Example - Critical Path Test**:
```typescript
describe('AuthService', () => {
  describe('hashPassword', () => {
    it('should create unique hashes for same password', async () => {
      const hash1 = await authService.hashPassword('password');
      const hash2 = await authService.hashPassword('password');
      expect(hash1).not.toBe(hash2); // Salt makes them different
    });

    it('should verify correct password', async () => {
      const hash = await authService.hashPassword('password');
      const result = await authService.comparePassword('password', hash);
      expect(result).toBe(true);
    });
  });
});
```

---

### Level 3: Integration/E2E Tests (MATURE STAGE)

**When to add**:
- Feature requirements are stable (not changing weekly)
- Core functionality is complete
- Preparing for production release
- After major refactoring

**Why defer E2E tests**:
- They're expensive to write and maintain
- They break frequently during active development
- They test flows that might change
- Self-tests already cover the happy path

**When E2E Tests ARE valuable**:
```
GOOD USE OF E2E TESTS              BAD USE OF E2E TESTS
─────────────────────              ────────────────────
✓ Stable authentication flow        ✗ New feature still being designed
✓ Payment processing                ✗ UI that changes frequently
✓ Critical user journeys            ✗ Internal admin tools
✓ Regression suite before release   ✗ Prototype/MVP stage
```

---

## What Changes By Project Stage

Self-test is ALWAYS part of development. What changes is the automated test coverage:

```
                        Early         Mid           Mature
────────────────────────────────────────────────────────────
Self-Test:              ALWAYS        ALWAYS        ALWAYS
Unit Tests:             Critical      Growing       Comprehensive
E2E Tests:              Skip          Optional      Required
────────────────────────────────────────────────────────────
```

### Stage 1: MVP/Prototype

```
Development Flow:
├── Self-Test: Part of every coding cycle
├── Unit Tests: Add for security-critical code only
└── E2E Tests: Skip (requirements still changing)
```

**Focus**: Ship fast, iterate on feedback, prove the concept works.

### Stage 2: Early Product

```
Development Flow:
├── Self-Test: Part of every coding cycle
├── Unit Tests: Growing coverage for business logic
└── E2E Tests: Optional (add for stable core flows)
```

**Focus**: Build confidence in core features, catch regressions.

### Stage 3: Mature Product

```
Development Flow:
├── Self-Test: Part of every coding cycle
├── Unit Tests: Comprehensive (80%+ coverage target)
└── E2E Tests: Required for critical user journeys
```

**Focus**: Prevent regressions, ensure reliability at scale.

---

## Frontend Testing Options

B Session has flexibility for frontend testing:

### Option A: Quick Smoke Test (Default)
```bash
npm run dev
# Verify: No build errors, page loads, main elements visible
```

### Option B: Delegate to External Agent
- Vercel Browser Agent
- Playwright MCP Server
- Other browser automation tools

B Session documents: "Frontend testing delegated to [agent]"

### Option C: Manual User Testing
- Notify user frontend is ready
- User tests and reports issues
- B Session fixes reported bugs

### Option D: Detailed Visual Testing (Mature Stage)
- Full agent-browser testing
- All pages and interactions covered
- Comprehensive documentation

---

## Self-Test Documentation Template

Include in commit messages and PRs:

```yaml
self_test_results:
  backend:
    server_started: true
    endpoints_tested:
      - "POST /api/auth/register → 201 ✓"
      - "POST /api/auth/login → 200 ✓"
      - "POST /api/auth/login (wrong password) → 401 ✓"
    database: "Operations verified"

  frontend:
    method: "quick_smoke_test"  # or "delegated", "manual", "detailed"
    build_errors: none
    status: "pass"

  issues_found: []

  notes: "All endpoints working as expected"
```

---

## Decision Guide

```
Should I write tests for this?
│
├── Is the code security-critical? (auth, payments, permissions)
│   └── YES → Write unit tests NOW
│
├── Is this fixing a bug?
│   └── YES → Write regression test NOW
│
├── Is the feature stable? (requirements unlikely to change)
│   └── YES → Consider unit tests
│   └── NO → Self-test is sufficient
│
├── Am I preparing for production release?
│   └── YES → Add E2E tests for critical flows
│
└── Otherwise → Self-test is sufficient
```

---

## Summary

| Level | When | Effort | Purpose |
|-------|------|--------|---------|
| Self-Test | ALWAYS (core dev practice) | Low | Verify code works in real environment |
| Unit Tests | Add progressively | Medium | Prevent regressions, test edge cases |
| E2E Tests | Mature products | High | Validate complete user journeys |

**Key Principle**: Self-test is HOW you develop - it's part of the coding cycle at every stage. Unit and E2E tests are automated safety nets that grow as the project matures.

```
Development Cycle (Every Stage):
    Write Code → Self-Test → Fix → Commit
                    ↑
            This never goes away
```
