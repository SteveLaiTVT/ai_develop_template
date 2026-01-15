# Authentication API Specification

**Status**: ✅ Completed (Archived)
**Created**: 2026-01-14
**Completed**: 2026-01-14
**Owner**: A Session

## Overview

This specification defines the authentication API endpoints for user registration, login, and token management. It follows RESTful conventions and integrates with the NestJS backend architecture.

## Requirements

### Functional Requirements

1. **User Registration**
   - Accept email and password
   - Validate email uniqueness
   - Hash password with bcrypt (cost factor 12)
   - Generate JWT access and refresh tokens
   - Return user profile with tokens

2. **User Login**
   - Accept email and password
   - Verify credentials
   - Generate JWT tokens
   - Return user profile with tokens

3. **Token Refresh**
   - Accept refresh token
   - Validate refresh token
   - Generate new access token
   - Rotate refresh token (security best practice)

### Non-Functional Requirements

1. **Security**
   - Password must be hashed, never stored in plain text
   - JWT tokens must expire (access: 15min, refresh: 7 days)
   - Rate limiting on auth endpoints (10 attempts/hour per IP)

2. **Performance**
   - Registration endpoint: < 500ms (P95)
   - Login endpoint: < 300ms (P95)

3. **Validation**
   - Email format validation
   - Password strength: min 8 chars, 1 uppercase, 1 number
   - All validation errors return HTTP 400

## API Endpoints

### POST /api/v1/auth/register

**Request Body**:
```json
{
  "email": "user@example.com",
  "password": "SecurePass123"
}
```

**Success Response (201 Created)**:
```json
{
  "success": true,
  "data": {
    "user": {
      "id": "uuid-v4",
      "email": "user@example.com",
      "created_at": "2026-01-14T10:00:00Z"
    },
    "tokens": {
      "access_token": "jwt-token",
      "refresh_token": "jwt-refresh-token",
      "expires_in": 900
    }
  }
}
```

**Error Responses**:
- 400 Bad Request: Invalid email format or weak password
- 409 Conflict: Email already exists

### POST /api/v1/auth/login

**Request Body**:
```json
{
  "email": "user@example.com",
  "password": "SecurePass123"
}
```

**Success Response (200 OK)**:
```json
{
  "success": true,
  "data": {
    "user": {
      "id": "uuid-v4",
      "email": "user@example.com"
    },
    "tokens": {
      "access_token": "jwt-token",
      "refresh_token": "jwt-refresh-token",
      "expires_in": 900
    }
  }
}
```

**Error Responses**:
- 400 Bad Request: Missing email or password
- 401 Unauthorized: Invalid credentials
- 429 Too Many Requests: Rate limit exceeded

### POST /api/v1/auth/refresh

**Request Body**:
```json
{
  "refresh_token": "jwt-refresh-token"
}
```

**Success Response (200 OK)**:
```json
{
  "success": true,
  "data": {
    "tokens": {
      "access_token": "new-jwt-token",
      "refresh_token": "new-jwt-refresh-token",
      "expires_in": 900
    }
  }
}
```

**Error Responses**:
- 401 Unauthorized: Invalid or expired refresh token

## Architecture Integration

### Module Structure (NestJS)

```
apps/backend/src/modules/auth/
├── auth.controller.ts      # Handle HTTP requests
├── auth.service.ts         # Business logic
├── auth.repository.ts      # Database operations
├── dto/
│   ├── register.dto.ts
│   ├── login.dto.ts
│   └── refresh.dto.ts
├── entities/
│   └── user.entity.ts
└── guards/
    └── jwt-auth.guard.ts
```

### Constraints from DESIGN_STATE.yaml

1. **Controller**: Only parameter validation, delegates to Service
2. **Service**: Contains business logic, calls Repository
3. **Repository**: Handles database operations via Prisma
4. **Transactions**: All database write operations in transactions
5. **Logging**: Log all authentication attempts (success/failure)
6. **Error Handling**: Use NestJS exception filters

## Dependencies

- `@nestjs/jwt` - JWT token generation
- `@nestjs/passport` - Authentication middleware
- `bcrypt` - Password hashing
- `class-validator` - DTO validation
- `class-transformer` - DTO transformation

## Testing Requirements

### Unit Tests

- `auth.service.spec.ts`: Business logic tests
- `auth.controller.spec.ts`: Controller tests
- `auth.repository.spec.ts`: Repository tests

### Integration Tests

- `auth.e2e.spec.ts`: End-to-end API tests

### Test Coverage

- Minimum 80% code coverage
- All error paths tested
- Security edge cases covered

## Security Considerations

1. **Password Security**
   - Use bcrypt with cost factor 12
   - Never log passwords
   - Clear password from memory after hashing

2. **Token Security**
   - Use secure JWT secret (from environment variable)
   - Include user ID in token payload
   - Validate token signature on every request

3. **Rate Limiting**
   - Implement on all auth endpoints
   - Use Redis for distributed rate limiting
   - Block by IP address

4. **HTTPS Only**
   - All endpoints must use HTTPS in production
   - Set secure cookie flags

## Migration Notes

### Database Schema

```sql
CREATE TABLE users (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  email VARCHAR(255) UNIQUE NOT NULL,
  password_hash VARCHAR(255) NOT NULL,
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW()
);

CREATE TABLE refresh_tokens (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID REFERENCES users(id) ON DELETE CASCADE,
  token_hash VARCHAR(255) NOT NULL,
  expires_at TIMESTAMP NOT NULL,
  created_at TIMESTAMP DEFAULT NOW()
);

CREATE INDEX idx_refresh_tokens_user_id ON refresh_tokens(user_id);
CREATE INDEX idx_refresh_tokens_expires_at ON refresh_tokens(expires_at);
```

## References

- DESIGN_STATE.yaml: API constraints, code constraints
- NestJS Authentication Guide: https://docs.nestjs.com/security/authentication
- JWT Best Practices: https://tools.ietf.org/html/rfc8725

## Implementation Notes

This specification was implemented in iteration `iter-001`, task `TASK-001`. See implementation files in `apps/backend/src/modules/auth/`.

**Acceptance Criteria**: ✅ All criteria met
- [x] POST /api/v1/auth/register available
- [x] POST /api/v1/auth/login available
- [x] POST /api/v1/auth/refresh available
- [x] Password hashing with bcrypt
- [x] JWT token generation
- [x] Rate limiting configured
- [x] Unit tests passing (85% coverage)
- [x] E2E tests passing
