# Implementation Tasks - Authentication API

## Overview

This document breaks down the authentication API implementation into specific, sequential tasks for B Session.

---

## Task 1: Setup Module Structure ✅

**Priority**: P0 - Must complete first

**Description**: Create the NestJS auth module with proper directory structure

**Files to Create**:
- `apps/backend/src/modules/auth/auth.module.ts`
- `apps/backend/src/modules/auth/auth.controller.ts`
- `apps/backend/src/modules/auth/auth.service.ts`
- `apps/backend/src/modules/auth/auth.repository.ts`

**Acceptance Criteria**:
- [ ] Auth module registered in app.module.ts
- [ ] Controller, Service, Repository connected via dependency injection
- [ ] Basic endpoints defined (no implementation yet)

**Estimated Complexity**: Low

---

## Task 2: Create DTOs and Validation ✅

**Priority**: P0

**Description**: Define Data Transfer Objects with validation rules

**Files to Create**:
- `apps/backend/src/modules/auth/dto/register.dto.ts`
- `apps/backend/src/modules/auth/dto/login.dto.ts`
- `apps/backend/src/modules/auth/dto/refresh.dto.ts`
- `apps/backend/src/modules/auth/dto/auth-response.dto.ts`

**Validation Rules**:
```typescript
// register.dto.ts
export class RegisterDto {
  @IsEmail()
  @IsNotEmpty()
  email: string;

  @IsString()
  @MinLength(8)
  @Matches(/^(?=.*[A-Z])(?=.*\d)/, {
    message: 'Password must contain uppercase and number'
  })
  password: string;
}
```

**Acceptance Criteria**:
- [ ] All DTOs have proper validation decorators
- [ ] Email format validated
- [ ] Password strength enforced (min 8, 1 uppercase, 1 number)
- [ ] Class-validator installed

---

## Task 3: Database Schema and Entity ✅

**Priority**: P0

**Description**: Create Prisma schema and generate migrations

**Files to Create/Modify**:
- `apps/backend/prisma/schema.prisma` (add User, RefreshToken models)
- `apps/backend/src/modules/auth/entities/user.entity.ts`

**Prisma Schema**:
```prisma
model User {
  id            String         @id @default(uuid())
  email         String         @unique
  passwordHash  String         @map("password_hash")
  createdAt     DateTime       @default(now()) @map("created_at")
  updatedAt     DateTime       @updatedAt @map("updated_at")
  refreshTokens RefreshToken[]

  @@map("users")
}

model RefreshToken {
  id        String   @id @default(uuid())
  userId    String   @map("user_id")
  tokenHash String   @map("token_hash")
  expiresAt DateTime @map("expires_at")
  createdAt DateTime @default(now()) @map("created_at")
  user      User     @relation(fields: [userId], references: [id], onDelete: Cascade)

  @@index([userId])
  @@index([expiresAt])
  @@map("refresh_tokens")
}
```

**Acceptance Criteria**:
- [ ] Prisma schema updated
- [ ] Migration generated: `npx prisma migrate dev --name add-auth-tables`
- [ ] Migration applied to dev database
- [ ] Entity types generated

---

## Task 4: Implement Password Hashing ✅

**Priority**: P0

**Description**: Add password hashing utility in auth.service.ts

**Dependencies**: `bcrypt` package

**Implementation**:
```typescript
async hashPassword(password: string): Promise<string> {
  const salt = await bcrypt.genSalt(12);
  return bcrypt.hash(password, salt);
}

async comparePassword(password: string, hash: string): Promise<boolean> {
  return bcrypt.compare(password, hash);
}
```

**Acceptance Criteria**:
- [ ] bcrypt installed
- [ ] Cost factor is 12
- [ ] Password never logged
- [ ] Unit tests for hashing/comparison

---

## Task 5: Implement JWT Token Generation ✅

**Priority**: P0

**Description**: Add JWT token generation and validation

**Dependencies**:
- `@nestjs/jwt`
- `@nestjs/passport`
- `passport-jwt`

**Implementation**:
```typescript
async generateTokens(userId: string): Promise<TokenPair> {
  const payload = { sub: userId };

  const accessToken = await this.jwtService.signAsync(payload, {
    expiresIn: '15m',
  });

  const refreshToken = await this.jwtService.signAsync(payload, {
    expiresIn: '7d',
  });

  return { accessToken, refreshToken, expiresIn: 900 };
}
```

**Acceptance Criteria**:
- [ ] JWT secret from environment variable
- [ ] Access token expires in 15 minutes
- [ ] Refresh token expires in 7 days
- [ ] Token payload includes user ID

---

## Task 6: Implement Registration Endpoint ✅

**Priority**: P0

**Description**: Implement POST /api/v1/auth/register

**Flow**:
1. Validate DTO
2. Check if email exists (return 409 if yes)
3. Hash password
4. Create user in database (transaction)
5. Generate tokens
6. Store refresh token hash
7. Return user + tokens

**Files to Modify**:
- `auth.controller.ts`: Add @Post('register') endpoint
- `auth.service.ts`: Add register() method
- `auth.repository.ts`: Add createUser(), findByEmail()

**Acceptance Criteria**:
- [ ] Returns 201 on success
- [ ] Returns 409 if email exists
- [ ] Returns 400 on validation error
- [ ] Password stored as hash
- [ ] Tokens returned in response

---

## Task 7: Implement Login Endpoint ✅

**Priority**: P0

**Description**: Implement POST /api/v1/auth/login

**Flow**:
1. Validate DTO
2. Find user by email
3. Compare password with hash
4. Generate tokens if valid
5. Store refresh token hash
6. Return user + tokens

**Files to Modify**:
- `auth.controller.ts`: Add @Post('login') endpoint
- `auth.service.ts`: Add login() method

**Acceptance Criteria**:
- [ ] Returns 200 on success
- [ ] Returns 401 on invalid credentials
- [ ] Returns 400 on validation error
- [ ] Logs authentication attempts

---

## Task 8: Implement Token Refresh Endpoint ✅

**Priority**: P0

**Description**: Implement POST /api/v1/auth/refresh

**Flow**:
1. Validate refresh token signature
2. Check if token is in database and not expired
3. Generate new access token
4. Generate new refresh token (rotation)
5. Invalidate old refresh token
6. Store new refresh token hash
7. Return new tokens

**Files to Modify**:
- `auth.controller.ts`: Add @Post('refresh') endpoint
- `auth.service.ts`: Add refreshTokens() method
- `auth.repository.ts`: Add findRefreshToken(), invalidateRefreshToken()

**Acceptance Criteria**:
- [ ] Returns 200 on success
- [ ] Returns 401 on invalid/expired token
- [ ] Old refresh token invalidated
- [ ] New tokens returned

---

## Task 9: Add Rate Limiting ✅

**Priority**: P1

**Description**: Implement rate limiting on auth endpoints

**Dependencies**:
- `@nestjs/throttler`
- Redis (for distributed rate limiting)

**Configuration**:
```typescript
// auth.module.ts
ThrottlerModule.forRoot({
  ttl: 3600, // 1 hour
  limit: 10, // 10 attempts
})
```

**Acceptance Criteria**:
- [ ] Rate limiting applied to all auth endpoints
- [ ] Returns 429 when limit exceeded
- [ ] Uses Redis for distributed limiting

---

## Task 10: Add Authentication Guard ✅

**Priority**: P0

**Description**: Create JWT authentication guard for protected routes

**Files to Create**:
- `apps/backend/src/modules/auth/guards/jwt-auth.guard.ts`
- `apps/backend/src/modules/auth/strategies/jwt.strategy.ts`

**Acceptance Criteria**:
- [ ] Guard validates JWT from Authorization header
- [ ] Guard rejects invalid/expired tokens
- [ ] User object attached to request

---

## Task 11: Add Logging ✅

**Priority**: P1

**Description**: Log all authentication events

**Events to Log**:
- Registration attempts (success/failure)
- Login attempts (success/failure)
- Token refresh attempts
- Rate limit violations

**Acceptance Criteria**:
- [ ] Use NestJS Logger
- [ ] Never log passwords
- [ ] Include IP address and user agent
- [ ] Include timestamp and user ID (if available)

---

## Task 12: Write Unit Tests ✅

**Priority**: P0

**Description**: Write unit tests for service and repository

**Files to Create**:
- `auth.service.spec.ts`
- `auth.repository.spec.ts`
- `auth.controller.spec.ts`

**Test Cases**:
- Registration: success, duplicate email, invalid email, weak password
- Login: success, invalid email, wrong password
- Refresh: success, expired token, invalid token
- Password hashing: correct hash, comparison
- Token generation: valid tokens, expiration

**Acceptance Criteria**:
- [ ] Minimum 80% code coverage
- [ ] All error paths tested
- [ ] Mock database calls

---

## Task 13: Write E2E Tests ✅

**Priority**: P0

**Description**: Write end-to-end API tests

**File to Create**:
- `test/auth.e2e.spec.ts`

**Test Scenarios**:
1. Full registration flow
2. Full login flow
3. Token refresh flow
4. Invalid credentials
5. Rate limiting
6. Concurrent requests

**Acceptance Criteria**:
- [ ] All happy paths tested
- [ ] All error cases tested
- [ ] Tests use test database
- [ ] Tests clean up after themselves

---

## Task Dependencies

```
Task 1 (Module Structure)
  └─> Task 2 (DTOs)
  └─> Task 3 (Database Schema)
      └─> Task 4 (Password Hashing)
          └─> Task 6 (Registration)
      └─> Task 5 (JWT)
          └─> Task 6 (Registration)
          └─> Task 7 (Login)
          └─> Task 8 (Refresh)
              └─> Task 10 (Auth Guard)
                  └─> Task 9 (Rate Limiting)
                      └─> Task 11 (Logging)
                          └─> Task 12 (Unit Tests)
                              └─> Task 13 (E2E Tests)
```

---

## Notes for B Session

- Follow DESIGN_STATE.yaml constraints strictly
- Controller should only validate and delegate to Service
- Service should not touch database directly, use Repository
- All database operations must be in transactions
- Use NestJS exception filters for error handling
- Never use `any` type
- All functions must have return type annotations
- Keep files under 300 lines
- Keep functions under 50 lines

---

## Completion Checklist

After completing all tasks, verify:

- [ ] All 13 tasks completed
- [ ] All acceptance criteria met
- [ ] Tests passing (unit + e2e)
- [ ] Code coverage >= 80%
- [ ] No TypeScript errors
- [ ] No linter errors
- [ ] Endpoints tested manually via Postman/curl
- [ ] Documentation updated
- [ ] Implementation report created
