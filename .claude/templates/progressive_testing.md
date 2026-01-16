# Progressive Testing Strategy

## Philosophy

**Test what matters NOW, add comprehensive testing as the project matures.**

Traditional approaches mandate E2E tests from day one, which leads to:
- Time spent on tests that break as requirements change
- False confidence from tests that don't catch real bugs
- Delayed delivery without proportional quality improvement

This template uses a **progressive testing approach** that matches testing effort to project maturity.

---

## Testing Levels

### Level 1: Self-Test (ALWAYS REQUIRED)

**What it means**: Actually run the code and verify it works.

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

## Testing by Project Stage

### Stage 1: MVP/Prototype (0-2 months)

```
TESTING REQUIREMENTS:
├── Self-Test: REQUIRED for every PR
├── Unit Tests: Optional (add for critical security code)
└── E2E Tests: Skip entirely
```

**Focus**: Ship fast, iterate on feedback, prove the concept works.

### Stage 2: Early Product (2-6 months)

```
TESTING REQUIREMENTS:
├── Self-Test: REQUIRED for every PR
├── Unit Tests: REQUIRED for business logic
└── E2E Tests: Optional (add for stable flows)
```

**Focus**: Build confidence in core features, catch regressions.

### Stage 3: Mature Product (6+ months)

```
TESTING REQUIREMENTS:
├── Self-Test: REQUIRED for every PR
├── Unit Tests: REQUIRED (80%+ coverage target)
└── E2E Tests: REQUIRED for critical user journeys
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

| Level | When Required | Effort | Value |
|-------|---------------|--------|-------|
| Self-Test | Always | Low | High |
| Unit Tests | Stable features | Medium | High |
| E2E Tests | Mature products | High | Medium |

**Key Principle**: The best test is one that catches real bugs with minimal maintenance burden. Self-testing catches most bugs. Add automated tests as the project matures and requirements stabilize.
