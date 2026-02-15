# Testing Skills

测试技能规范 - 用来规范测试上的一些关键点和最佳实践。

This skill defines testing standards and best practices for ensuring code quality and reliability.

---

## Core Philosophy

```
测试的目的是建立对代码的信心，而不是追求覆盖率数字
The purpose of testing is to build confidence in the code, not to chase coverage numbers
```

### Testing Principles

1. **Test behavior, not implementation** - Focus on what the code does, not how it does it
2. **Write tests that provide value** - Every test should catch real bugs
3. **Keep tests maintainable** - Tests should be easy to understand and update
4. **Test at the right level** - Choose the appropriate testing level for each scenario

---

## Testing Pyramid

```
                         /\
                        /  \     E2E Tests (5-10%)
                       /----\    - Full user journeys
                      /      \   - Critical paths only
                     /--------\  
                    /          \  Integration Tests (20-30%)
                   /------------\ - Component interactions
                  /              \- API contracts
                 /----------------\
                /                  \ Unit Tests (60-70%)
               /--------------------\- Pure functions
              /                      \- Business logic
             /________________________\
            Static Analysis (100%)
            - TypeScript, ESLint, etc.
```

---

## Test Types

### 1. Unit Tests

**Purpose:** Test individual functions or methods in isolation

**When to use:**
- Pure functions with no side effects
- Business logic calculations
- Data transformations
- Validation functions

**Example:**

```typescript
// Function to test
function calculateDiscount(price: number, percentage: number): number {
  if (price < 0 || percentage < 0 || percentage > 100) {
    throw new Error('Invalid input');
  }
  return price * (1 - percentage / 100);
}

// Test
describe('calculateDiscount', () => {
  it('should apply percentage discount correctly', () => {
    expect(calculateDiscount(100, 20)).toBe(80);
    expect(calculateDiscount(50, 10)).toBe(45);
  });

  it('should handle zero discount', () => {
    expect(calculateDiscount(100, 0)).toBe(100);
  });

  it('should handle 100% discount', () => {
    expect(calculateDiscount(100, 100)).toBe(0);
  });

  it('should throw for negative price', () => {
    expect(() => calculateDiscount(-100, 20)).toThrow('Invalid input');
  });

  it('should throw for invalid percentage', () => {
    expect(() => calculateDiscount(100, 150)).toThrow('Invalid input');
  });
});
```

### 2. Integration Tests

**Purpose:** Test how components work together

**When to use:**
- Database operations
- API endpoints
- Service interactions
- External service integrations (with mocks)

**Example:**

```typescript
describe('UserService', () => {
  let userService: UserService;
  let userRepository: UserRepository;
  let db: TestDatabase;

  beforeEach(async () => {
    db = await createTestDatabase();
    userRepository = new UserRepository(db);
    userService = new UserService(userRepository);
  });

  afterEach(async () => {
    await db.cleanup();
  });

  describe('createUser', () => {
    it('should create user and return with id', async () => {
      const userData = {
        email: 'test@example.com',
        name: 'Test User',
      };

      const user = await userService.createUser(userData);

      expect(user.id).toBeDefined();
      expect(user.email).toBe(userData.email);
      
      // Verify in database
      const dbUser = await db.query('SELECT * FROM users WHERE id = $1', [user.id]);
      expect(dbUser.rows[0].email).toBe(userData.email);
    });

    it('should throw on duplicate email', async () => {
      const userData = { email: 'test@example.com', name: 'Test User' };
      
      await userService.createUser(userData);
      
      await expect(userService.createUser(userData))
        .rejects.toThrow('Email already exists');
    });
  });
});
```

### 3. End-to-End (E2E) Tests

**Purpose:** Test complete user journeys through the application

**When to use:**
- Critical user flows (login, checkout, signup)
- Features that are stable and unlikely to change
- Before major releases

**Example:**

```typescript
describe('User Registration Flow', () => {
  beforeEach(async () => {
    await page.goto('http://localhost:3000/register');
  });

  it('should complete registration successfully', async () => {
    // Fill registration form
    await page.fill('#name', 'John Doe');
    await page.fill('#email', 'john@example.com');
    await page.fill('#password', 'SecurePass123!');
    await page.fill('#confirmPassword', 'SecurePass123!');
    
    // Submit form
    await page.click('button[type="submit"]');
    
    // Verify redirect to dashboard
    await expect(page).toHaveURL('/dashboard');
    
    // Verify welcome message
    await expect(page.locator('.welcome-message'))
      .toContainText('Welcome, John');
  });

  it('should show validation errors for invalid input', async () => {
    await page.fill('#email', 'invalid-email');
    await page.click('button[type="submit"]');
    
    await expect(page.locator('.error-message'))
      .toContainText('Invalid email format');
  });
});
```

---

## Test Structure

### AAA Pattern (Arrange-Act-Assert)

```typescript
it('should add item to cart', () => {
  // Arrange - Set up test data and conditions
  const cart = new ShoppingCart();
  const product = { id: '1', name: 'Widget', price: 10 };
  
  // Act - Execute the code being tested
  cart.addItem(product, 2);
  
  // Assert - Verify the expected outcome
  expect(cart.items).toHaveLength(1);
  expect(cart.items[0].quantity).toBe(2);
  expect(cart.total).toBe(20);
});
```

### Given-When-Then (BDD Style)

```typescript
describe('Shopping Cart', () => {
  describe('given an empty cart', () => {
    let cart: ShoppingCart;
    
    beforeEach(() => {
      cart = new ShoppingCart();
    });

    describe('when adding a product', () => {
      const product = { id: '1', name: 'Widget', price: 10 };
      
      beforeEach(() => {
        cart.addItem(product, 2);
      });

      it('then the cart should contain one item', () => {
        expect(cart.items).toHaveLength(1);
      });

      it('then the total should be calculated correctly', () => {
        expect(cart.total).toBe(20);
      });
    });
  });
});
```

---

## What to Test

### High Priority (Always Test)

| Area | Why | Example |
|------|-----|---------|
| Authentication | Security critical | Login, logout, token refresh |
| Authorization | Security critical | Role-based access |
| Payment processing | Business critical | Checkout, refunds |
| Data validation | Data integrity | Form validation, API inputs |
| Core business logic | Core value | Price calculation, inventory |

### Medium Priority (Test When Stable)

| Area | When | Example |
|------|------|---------|
| API endpoints | After design stabilizes | CRUD operations |
| User workflows | After UI stabilizes | Multi-step forms |
| Data transformations | When logic is complex | Report generation |

### Low Priority (Test Selectively)

| Area | Considerations | Example |
|------|----------------|---------|
| UI components | Visual testing may be better | Button styles |
| Third-party integrations | Mock in unit tests | Payment gateways |
| Configuration loading | Rarely changes | Environment setup |

---

## Mocking Best Practices

### When to Mock

```
✅ DO mock:
- External services (APIs, databases in unit tests)
- Time-dependent code (dates, timers)
- Random values
- File system operations (in unit tests)
- Network requests

❌ DON'T mock:
- The code you're testing
- Simple data structures
- Pure utility functions
- Everything (leads to false confidence)
```

### Mocking Examples

```typescript
// Mock external service
const mockPaymentService = {
  processPayment: jest.fn(),
  refund: jest.fn(),
};

// Mock with implementation
mockPaymentService.processPayment.mockResolvedValue({
  success: true,
  transactionId: 'txn_123',
});

// Mock time
jest.useFakeTimers();
jest.setSystemTime(new Date('2024-01-15'));

// Mock module
jest.mock('./emailService', () => ({
  sendEmail: jest.fn().mockResolvedValue(true),
}));
```

### Mock Verification

```typescript
it('should send welcome email after registration', async () => {
  const mockSendEmail = jest.fn().mockResolvedValue(true);
  
  await userService.register({
    email: 'test@example.com',
    sendEmail: mockSendEmail,
  });
  
  expect(mockSendEmail).toHaveBeenCalledTimes(1);
  expect(mockSendEmail).toHaveBeenCalledWith(
    'test@example.com',
    expect.stringContaining('Welcome'),
  );
});
```

---

## Test Naming Conventions

### Descriptive Names

```typescript
// ✅ Good: Describes behavior and expected outcome
it('should return empty array when no users match the filter')
it('should throw ValidationError when email format is invalid')
it('should apply 10% discount for orders over $100')

// ❌ Bad: Vague or implementation-focused
it('test filter')
it('should work')
it('handles error')
```

### Naming Pattern

```
should [expected behavior] when [condition]
```

### Examples by Context

```typescript
describe('OrderService', () => {
  describe('calculateTotal', () => {
    it('should return sum of item prices')
    it('should apply discount code when valid')
    it('should throw when discount code is expired')
    it('should include shipping for orders under $50')
    it('should provide free shipping for orders over $50')
  });
});
```

---

## Test Data Management

### Test Fixtures

```typescript
// fixtures/users.ts
export const testUsers = {
  admin: {
    id: 'admin-1',
    email: 'admin@example.com',
    role: 'admin',
  },
  regularUser: {
    id: 'user-1',
    email: 'user@example.com',
    role: 'user',
  },
  premiumUser: {
    id: 'premium-1',
    email: 'premium@example.com',
    role: 'user',
    subscription: 'premium',
  },
};

// Usage in tests
import { testUsers } from './fixtures/users';

it('should allow admin to delete users', () => {
  const result = userService.canDelete(testUsers.admin, testUsers.regularUser);
  expect(result).toBe(true);
});
```

### Factory Functions

```typescript
// factories/user.ts
export function createTestUser(overrides: Partial<User> = {}): User {
  return {
    id: `user-${Math.random().toString(36).substr(2, 9)}`,
    email: `test-${Date.now()}@example.com`,
    name: 'Test User',
    role: 'user',
    createdAt: new Date(),
    ...overrides,
  };
}

// Usage
it('should allow premium users to access feature', () => {
  const premiumUser = createTestUser({ subscription: 'premium' });
  expect(canAccessFeature(premiumUser)).toBe(true);
});
```

---

## Async Testing

### Promises

```typescript
// Using async/await (preferred)
it('should fetch user data', async () => {
  const user = await userService.getUser('123');
  expect(user.name).toBe('John');
});

// Using .resolves/.rejects
it('should reject for invalid id', async () => {
  await expect(userService.getUser('invalid'))
    .rejects.toThrow('User not found');
});
```

### Timeouts and Timers

```typescript
// Testing debounced functions
it('should debounce search input', async () => {
  jest.useFakeTimers();
  
  const mockSearch = jest.fn();
  const debouncedSearch = debounce(mockSearch, 300);
  
  debouncedSearch('a');
  debouncedSearch('ab');
  debouncedSearch('abc');
  
  expect(mockSearch).not.toHaveBeenCalled();
  
  jest.advanceTimersByTime(300);
  
  expect(mockSearch).toHaveBeenCalledTimes(1);
  expect(mockSearch).toHaveBeenCalledWith('abc');
});
```

---

## Code Coverage

### Coverage Guidelines

| Metric | Target | Notes |
|--------|--------|-------|
| Line coverage | 70-80% | Don't aim for 100% |
| Branch coverage | 70-80% | Cover error paths |
| Function coverage | 80%+ | Test public APIs |

### What Coverage Numbers Mean

```
High coverage ≠ Good tests
Low coverage = Missing tests

Focus on:
- Testing critical paths thoroughly
- Covering edge cases
- Testing error handling
```

### Coverage Exclusions

```typescript
/* istanbul ignore next */
function debugLog(message: string) {
  if (process.env.DEBUG) {
    console.log(message);
  }
}
```

---

## Test Organization

### File Structure

```
src/
├── services/
│   ├── user.service.ts
│   └── user.service.test.ts      # Co-located tests
├── utils/
│   ├── format.ts
│   └── format.test.ts

# OR

src/
├── services/
│   └── user.service.ts
tests/
├── unit/
│   └── services/
│       └── user.service.test.ts
├── integration/
│   └── api/
│       └── users.test.ts
└── e2e/
    └── flows/
        └── registration.test.ts
```

### Test Configuration

```javascript
// jest.config.js
module.exports = {
  testMatch: ['**/*.test.ts', '**/*.spec.ts'],
  coverageThreshold: {
    global: {
      branches: 70,
      functions: 80,
      lines: 80,
      statements: 80,
    },
  },
  setupFilesAfterEnv: ['./jest.setup.ts'],
  collectCoverageFrom: [
    'src/**/*.ts',
    '!src/**/*.d.ts',
    '!src/**/index.ts',
  ],
};
```

---

## Common Anti-Patterns

### What to Avoid

| Anti-Pattern | Problem | Solution |
|--------------|---------|----------|
| Testing implementation | Breaks on refactor | Test behavior instead |
| Excessive mocking | False confidence | Use real dependencies when safe |
| Flaky tests | Unreliable CI | Fix timing issues, use proper waits |
| Large test files | Hard to maintain | Split by feature/behavior |
| Copy-paste tests | Maintenance burden | Use helpers and factories |
| Testing framework code | Waste of time | Trust the framework |

### Examples

```typescript
// ❌ Bad: Testing implementation details
it('should call repository.findById', async () => {
  await userService.getUser('123');
  expect(mockRepository.findById).toHaveBeenCalled();
});

// ✅ Good: Testing behavior
it('should return user data for valid id', async () => {
  const user = await userService.getUser('123');
  expect(user.id).toBe('123');
  expect(user.email).toBeDefined();
});
```

---

## CI/CD Integration

### Test Pipeline

```yaml
# .github/workflows/test.yml
name: Test
on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Install dependencies
        run: npm ci
      
      - name: Run linting
        run: npm run lint
      
      - name: Run type checking
        run: npm run typecheck
      
      - name: Run unit tests
        run: npm run test:unit
      
      - name: Run integration tests
        run: npm run test:integration
        env:
          DATABASE_URL: ${{ secrets.TEST_DATABASE_URL }}
      
      - name: Upload coverage
        uses: codecov/codecov-action@v3
```

---

## Quick Reference Checklist

Before considering tests complete:

```
✓ Critical paths have tests (auth, payments, core logic)
✓ Tests are readable and well-named
✓ Tests run quickly (< 30s for unit tests)
✓ Tests are deterministic (no random failures)
✓ Edge cases are covered
✓ Error handling is tested
✓ Mocks are used appropriately
✓ Test data is realistic
✓ No console.log or debugging code
✓ Tests can run in any order
```
