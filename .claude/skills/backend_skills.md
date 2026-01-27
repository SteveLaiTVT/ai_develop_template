# Backend Development Skills

后端开发规范与最佳实践 - 一个合格的后台开发人员所具备的开发能力还有规范。

This skill defines backend development standards and best practices that a qualified backend developer should possess.

---

## Core Principles

### 1. Clean Architecture

```
分层架构，关注点分离
Layered architecture, separation of concerns
```

**Standard Layers:**
```
┌─────────────────────────────────────────────────┐
│           Presentation Layer (Controllers)       │
│   - HTTP routing, request/response handling      │
├─────────────────────────────────────────────────┤
│           Application Layer (Services)           │
│   - Business logic, orchestration               │
├─────────────────────────────────────────────────┤
│           Domain Layer (Entities)                │
│   - Core business rules, entities               │
├─────────────────────────────────────────────────┤
│           Infrastructure Layer (Repositories)    │
│   - Database, external services, caching        │
└─────────────────────────────────────────────────┘
```

### 2. SOLID Principles

| Principle | Description | Example |
|-----------|-------------|---------|
| **S**ingle Responsibility | One class, one purpose | `UserService` only handles user logic |
| **O**pen/Closed | Open for extension, closed for modification | Use interfaces for new payment methods |
| **L**iskov Substitution | Subtypes replaceable for parent types | Any `Logger` implementation works |
| **I**nterface Segregation | Many specific interfaces over one general | `Readable`, `Writable` vs `FileHandler` |
| **D**ependency Inversion | Depend on abstractions, not concretions | Inject `IUserRepository`, not `MySqlUserRepository` |

### 3. API First Design

- Design API contracts before implementation
- Use OpenAPI/Swagger for documentation
- Version your APIs from the start
- Follow RESTful conventions

---

## Project Structure

### Standard Backend Structure

```
src/
├── controllers/         # HTTP request handlers
│   ├── auth.controller.ts
│   └── user.controller.ts
├── services/            # Business logic
│   ├── auth.service.ts
│   └── user.service.ts
├── repositories/        # Data access layer
│   ├── user.repository.ts
│   └── base.repository.ts
├── entities/            # Domain models
│   ├── user.entity.ts
│   └── base.entity.ts
├── dto/                 # Data Transfer Objects
│   ├── auth/
│   │   ├── login.dto.ts
│   │   └── register.dto.ts
│   └── user/
│       └── update-user.dto.ts
├── middleware/          # Express/Koa middleware
│   ├── auth.middleware.ts
│   ├── logging.middleware.ts
│   └── error.middleware.ts
├── guards/              # Authorization guards
│   └── roles.guard.ts
├── utils/               # Utility functions
├── config/              # Configuration
├── exceptions/          # Custom exceptions
└── types/               # TypeScript types
```

---

## API Design Standards

### RESTful Conventions

```
GET    /api/users          # List users (paginated)
GET    /api/users/:id      # Get single user
POST   /api/users          # Create user
PUT    /api/users/:id      # Replace user
PATCH  /api/users/:id      # Update user partially
DELETE /api/users/:id      # Delete user
```

### HTTP Status Codes

| Code | Meaning | When to Use |
|------|---------|-------------|
| 200 | OK | Successful GET, PUT, PATCH |
| 201 | Created | Successful POST creating resource |
| 204 | No Content | Successful DELETE |
| 400 | Bad Request | Invalid input, validation errors |
| 401 | Unauthorized | Missing or invalid authentication |
| 403 | Forbidden | Authenticated but not authorized |
| 404 | Not Found | Resource doesn't exist |
| 409 | Conflict | Duplicate resource, state conflict |
| 422 | Unprocessable Entity | Semantic errors |
| 500 | Internal Server Error | Server-side errors |

### Response Format

```typescript
// Success Response
{
  "success": true,
  "data": {
    "id": "123",
    "name": "John Doe",
    "email": "john@example.com"
  }
}

// Error Response
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Email format is invalid",
    "details": [
      {
        "field": "email",
        "message": "Must be a valid email address"
      }
    ]
  }
}

// Paginated Response
{
  "success": true,
  "data": [...],
  "pagination": {
    "page": 1,
    "limit": 20,
    "totalItems": 150,
    "totalPages": 8
  }
}
```

---

## Data Validation

### Input Validation (DTO Pattern)

```typescript
// Using class-validator (NestJS/TypeScript)
import { IsEmail, IsString, MinLength, MaxLength, IsOptional } from 'class-validator';

export class RegisterDto {
  @IsEmail()
  email: string;

  @IsString()
  @MinLength(8, { message: 'Password must be at least 8 characters' })
  @MaxLength(100)
  password: string;

  @IsString()
  @IsOptional()
  @MaxLength(100)
  displayName?: string;
}
```

### Validation Rules

| Field Type | Rules |
|------------|-------|
| Email | Valid format, max 255 chars, lowercase |
| Password | Min 8 chars, complexity requirements |
| Username | Alphanumeric, 3-50 chars, no spaces |
| Phone | Valid format with country code |
| URL | Valid URL format, protocol required |
| ID (UUID) | Valid UUID v4 format |

---

## Authentication & Authorization

### JWT Authentication Flow

```
┌───────────────────────────────────────────────────────────┐
│                     JWT Authentication Flow                │
├───────────────────────────────────────────────────────────┤
│                                                           │
│  1. Login Request                                         │
│     POST /api/auth/login { email, password }              │
│                                                           │
│  2. Server validates credentials                          │
│     - Hash password comparison                            │
│     - Generate access token (short-lived, 15min)          │
│     - Generate refresh token (long-lived, 7days)          │
│                                                           │
│  3. Response                                              │
│     { accessToken, refreshToken, expiresIn }              │
│                                                           │
│  4. Client stores tokens securely                         │
│     - Access token: Memory or short-term storage          │
│     - Refresh token: HttpOnly cookie (preferred)          │
│                                                           │
│  5. Protected requests                                    │
│     Authorization: Bearer <accessToken>                   │
│                                                           │
│  6. Token refresh when expired                            │
│     POST /api/auth/refresh { refreshToken }               │
│                                                           │
└───────────────────────────────────────────────────────────┘
```

### Role-Based Access Control (RBAC)

```typescript
// Define roles
enum Role {
  USER = 'user',
  ADMIN = 'admin',
  SUPER_ADMIN = 'super_admin',
}

// Guard decorator
@Roles(Role.ADMIN)
@Get('admin/dashboard')
getDashboard() {
  return this.adminService.getDashboard();
}

// Guard implementation
@Injectable()
export class RolesGuard implements CanActivate {
  canActivate(context: ExecutionContext): boolean {
    const requiredRoles = this.reflector.get<Role[]>('roles', context.getHandler());
    if (!requiredRoles) return true;
    
    const { user } = context.switchToHttp().getRequest();
    return requiredRoles.some((role) => user.roles?.includes(role));
  }
}
```

---

## Database Patterns

### Repository Pattern

```typescript
// Abstract repository
export interface IRepository<T> {
  findById(id: string): Promise<T | null>;
  findAll(options?: FindOptions): Promise<T[]>;
  create(data: Partial<T>): Promise<T>;
  update(id: string, data: Partial<T>): Promise<T>;
  delete(id: string): Promise<boolean>;
}

// Concrete implementation
@Injectable()
export class UserRepository implements IRepository<User> {
  constructor(private readonly prisma: PrismaService) {}

  async findById(id: string): Promise<User | null> {
    return this.prisma.user.findUnique({ where: { id } });
  }

  async findAll(options?: FindOptions): Promise<User[]> {
    return this.prisma.user.findMany({
      skip: options?.offset,
      take: options?.limit,
      orderBy: options?.orderBy,
    });
  }

  // ... other methods
}
```

### Transaction Handling

```typescript
// Transaction example (Prisma)
async transferFunds(fromId: string, toId: string, amount: number): Promise<void> {
  await this.prisma.$transaction(async (tx) => {
    // Debit from sender
    await tx.account.update({
      where: { id: fromId },
      data: { balance: { decrement: amount } },
    });

    // Credit to receiver
    await tx.account.update({
      where: { id: toId },
      data: { balance: { increment: amount } },
    });

    // Record transaction
    await tx.transaction.create({
      data: { fromId, toId, amount },
    });
  });
}
```

---

## Error Handling

### Centralized Error Handling

```typescript
// Custom exception classes
export class AppException extends Error {
  constructor(
    public readonly code: string,
    public readonly message: string,
    public readonly statusCode: number = 500,
    public readonly details?: unknown,
  ) {
    super(message);
  }
}

export class ValidationException extends AppException {
  constructor(message: string, details?: unknown) {
    super('VALIDATION_ERROR', message, 400, details);
  }
}

export class NotFoundException extends AppException {
  constructor(resource: string) {
    super('NOT_FOUND', `${resource} not found`, 404);
  }
}

export class UnauthorizedException extends AppException {
  constructor(message = 'Unauthorized') {
    super('UNAUTHORIZED', message, 401);
  }
}
```

### Global Exception Filter

```typescript
@Catch()
export class GlobalExceptionFilter implements ExceptionFilter {
  catch(exception: unknown, host: ArgumentsHost) {
    const ctx = host.switchToHttp();
    const response = ctx.getResponse<Response>();

    let status = 500;
    let errorResponse = {
      success: false,
      error: {
        code: 'INTERNAL_ERROR',
        message: 'An unexpected error occurred',
      },
    };

    if (exception instanceof AppException) {
      status = exception.statusCode;
      errorResponse.error = {
        code: exception.code,
        message: exception.message,
        ...(exception.details && { details: exception.details }),
      };
    }

    // Log error for debugging
    this.logger.error(exception);

    response.status(status).json(errorResponse);
  }
}
```

---

## Logging Standards

### Log Levels

| Level | When to Use | Example |
|-------|-------------|---------|
| ERROR | Errors requiring attention | Database connection failed |
| WARN | Potential issues | Rate limit approaching |
| INFO | Normal operations | User registered, API called |
| DEBUG | Debugging information | SQL queries, cache hits |

### Structured Logging

```typescript
// Good: Structured logging with context
logger.info('User registered', {
  userId: user.id,
  email: user.email,
  source: 'registration_api',
  duration: 150,
});

// Bad: Unstructured logging
logger.info(`User ${user.email} registered in 150ms`);
```

### What to Log

```
✅ DO log:
- All incoming requests (method, path, response code, duration)
- Authentication events (login, logout, failed attempts)
- Business-critical operations (payments, orders)
- Errors and exceptions with stack traces
- External API calls (service, duration, status)

❌ DON'T log:
- Passwords or secrets
- Full credit card numbers
- Personal data (use redaction)
- High-frequency health checks
```

---

## Security Practices

### OWASP Top 10 Prevention

| Vulnerability | Prevention |
|---------------|------------|
| Injection | Parameterized queries, input validation |
| Broken Auth | Secure session management, MFA |
| Sensitive Data Exposure | Encryption at rest and in transit |
| XXE | Disable XML external entities |
| Broken Access Control | RBAC, resource ownership checks |
| Security Misconfiguration | Secure defaults, regular audits |
| XSS | Output encoding, CSP headers |
| Insecure Deserialization | Validate serialized data |
| Known Vulnerabilities | Regular dependency updates |
| Insufficient Logging | Comprehensive audit logging |

### Password Security

```typescript
// ✅ Correct password handling
import * as bcrypt from 'bcrypt';

const SALT_ROUNDS = 12; // Adjust based on performance

async function hashPassword(password: string): Promise<string> {
  return bcrypt.hash(password, SALT_ROUNDS);
}

async function verifyPassword(password: string, hash: string): Promise<boolean> {
  return bcrypt.compare(password, hash);
}
```

### Rate Limiting

```typescript
// Rate limiting configuration
@UseGuards(ThrottlerGuard)
@Throttle(10, 60) // 10 requests per 60 seconds
@Post('login')
async login(@Body() dto: LoginDto) {
  return this.authService.login(dto);
}
```

---

## Performance Optimization

### Caching Strategy

```typescript
// Cache-aside pattern
async getUser(id: string): Promise<User> {
  // Check cache first
  const cached = await this.cache.get(`user:${id}`);
  if (cached) return cached;

  // Fetch from database
  const user = await this.userRepository.findById(id);
  
  // Store in cache
  await this.cache.set(`user:${id}`, user, 3600); // 1 hour TTL
  
  return user;
}
```

### Database Optimization

```
✅ DO:
- Add indexes for frequently queried columns
- Use pagination for list endpoints
- Select only needed columns
- Use connection pooling
- Optimize N+1 queries with eager loading

❌ DON'T:
- Fetch all records without limits
- Use SELECT * in production code
- Create indexes on every column
- Store large blobs in database
```

---

## Testing Strategy

### Testing Pyramid

```
        /\
       /  \  E2E Tests (5%)
      /----\  Integration Tests (25%)
     /------\  Unit Tests (70%)
    /--------\
```

### Unit Testing

```typescript
describe('AuthService', () => {
  let authService: AuthService;
  let userRepository: MockType<UserRepository>;

  beforeEach(async () => {
    const module = await Test.createTestingModule({
      providers: [
        AuthService,
        { provide: UserRepository, useFactory: createMockRepository },
      ],
    }).compile();

    authService = module.get<AuthService>(AuthService);
    userRepository = module.get(UserRepository);
  });

  describe('validateCredentials', () => {
    it('should return user for valid credentials', async () => {
      const user = { id: '1', email: 'test@example.com' };
      userRepository.findByEmail.mockResolvedValue(user);

      const result = await authService.validateCredentials(
        'test@example.com',
        'password123',
      );

      expect(result).toEqual(user);
    });

    it('should throw for invalid password', async () => {
      userRepository.findByEmail.mockResolvedValue(null);

      await expect(
        authService.validateCredentials('test@example.com', 'wrong'),
      ).rejects.toThrow(UnauthorizedException);
    });
  });
});
```

---

## API Documentation

### OpenAPI/Swagger

```typescript
// Controller documentation
@ApiTags('users')
@Controller('users')
export class UserController {
  @ApiOperation({ summary: 'Get user by ID' })
  @ApiParam({ name: 'id', description: 'User ID', example: '123e4567-e89b-12d3-a456-426614174000' })
  @ApiResponse({ status: 200, description: 'User found', type: UserResponseDto })
  @ApiResponse({ status: 404, description: 'User not found' })
  @Get(':id')
  async getUser(@Param('id') id: string): Promise<UserResponseDto> {
    return this.userService.findById(id);
  }
}
```

---

## Quick Reference Checklist

Before submitting backend code:

```
✓ All endpoints are properly documented
✓ Input validation is implemented
✓ Authentication/authorization is in place
✓ Error handling is comprehensive
✓ Logging is appropriate (no sensitive data)
✓ Database queries are optimized
✓ Unit tests cover critical paths
✓ Security best practices are followed
✓ Rate limiting is configured
✓ API versioning is considered
```
