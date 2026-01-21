# Tech Best Practices Template

This template guides A Session through applying technology-specific best practices after the tech stack is selected.

## When to Apply

This phase runs **after tech stack selection** and **before skeleton code creation**.

```
┌─────────────────────────────────────────────────────────────────┐
│                    BEST PRACTICES PHASE                          │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  Discovery Interview                                             │
│          ↓                                                       │
│  Tech Stack Selection (Q4)                                       │
│          ↓                                                       │
│  ✨ BEST PRACTICES QUESTION (Q4.5) ← YOU ARE HERE               │
│          ↓                                                       │
│  Apply Selected Best Practices                                   │
│          ↓                                                       │
│  Architecture Design                                             │
│          ↓                                                       │
│  Skeleton Code Creation                                          │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## Step 1: Ask Best Practices Question

After user confirms their tech stack, display this prompt:

```
╔══════════════════════════════════════════════════════════════════╗
║  🎯 TECH BEST PRACTICES                                          ║
╠══════════════════════════════════════════════════════════════════╣
║                                                                  ║
║  Your selected tech stack:                                       ║
║  • Frontend: [React/Vue/Next.js/etc.]                            ║
║  • Backend: [NestJS/Express/FastAPI/etc.]                        ║
║  • Database: [PostgreSQL/MongoDB/etc.]                           ║
║  • [Other technologies...]                                       ║
║                                                                  ║
╠══════════════════════════════════════════════════════════════════╣
║                                                                  ║
║  Q: Would you like me to apply industry best practices for       ║
║     each technology in your stack?                               ║
║                                                                  ║
║  Options:                                                        ║
║                                                                  ║
║  • "yes" (Recommended)                                           ║
║     Apply all relevant best practices automatically.             ║
║     Ensures modern, maintainable, and scalable code.             ║
║                                                                  ║
║  • "selective"                                                   ║
║     I'll show you available practices for each tech,             ║
║     and you choose which ones to apply.                          ║
║                                                                  ║
║  • "no"                                                          ║
║     Skip best practices. Use basic/minimal patterns.             ║
║     (Good for learning or simple prototypes)                     ║
║                                                                  ║
║  💡 Best practices include:                                      ║
║     - Code organization patterns                                 ║
║     - Performance optimizations                                  ║
║     - Security guidelines                                        ║
║     - Testing strategies                                         ║
║     - Error handling patterns                                    ║
║                                                                  ║
╚══════════════════════════════════════════════════════════════════╝
```

---

## Step 2: Handle User Response

### If "yes" (Apply All)

1. Read user's tech stack from `DESIGN_STATE.yaml`
2. Match each technology with practices in `tech_best_practices.available_practices.catalog`
3. Apply all matching practices automatically
4. Update `tech_best_practices.applied_practices` with applied patterns
5. Show summary of applied practices

```
╔══════════════════════════════════════════════════════════════════╗
║  ✅ BEST PRACTICES APPLIED                                       ║
╠══════════════════════════════════════════════════════════════════╣
║                                                                  ║
║  I've configured the following best practices for your project:  ║
║                                                                  ║
║  📦 NestJS Best Practices                                        ║
║     • Modular architecture with feature modules                  ║
║     • DTOs with class-validator for validation                   ║
║     • Repository pattern for data access                         ║
║     • Exception filters for error handling                       ║
║                                                                  ║
║  ⚛️  React 18+ Best Practices                                    ║
║     • Functional components with hooks                           ║
║     • Custom hooks for reusable logic                            ║
║     • Error boundaries for error handling                        ║
║     • React.lazy for code splitting                              ║
║                                                                  ║
║  🐘 PostgreSQL Best Practices                                    ║
║     • Proper indexing strategies                                 ║
║     • Migrations via Prisma                                      ║
║     • Connection pooling                                         ║
║                                                                  ║
║  🔒 Security Best Practices                                      ║
║     • Input validation                                           ║
║     • Parameterized queries                                      ║
║     • CORS configuration                                         ║
║     • Rate limiting                                              ║
║                                                                  ║
║  These practices will be reflected in the skeleton code.         ║
║                                                                  ║
╚══════════════════════════════════════════════════════════════════╝
```

### If "selective" (User Chooses)

Display practices for each technology and let user pick:

```
╔══════════════════════════════════════════════════════════════════╗
║  🎯 SELECT BEST PRACTICES                                        ║
╠══════════════════════════════════════════════════════════════════╣
║                                                                  ║
║  For NestJS, which practices would you like to apply?            ║
║  (Type numbers separated by commas, or "all" / "none")           ║
║                                                                  ║
║  1. ✅ Modular architecture with feature modules                 ║
║  2. ✅ DTOs with class-validator for validation                  ║
║  3. ✅ Repository pattern for data access                        ║
║  4. ✅ Exception filters for error handling                      ║
║  5. ✅ Guards for authentication/authorization                   ║
║  6. ⬜ Interceptors for logging and transformation               ║
║  7. ⬜ ConfigModule for environment variables                    ║
║  8. ⬜ Pipes for data transformation                             ║
║                                                                  ║
║  Enter selection: [e.g., "1,2,3,4,5" or "all"]                   ║
║                                                                  ║
╚══════════════════════════════════════════════════════════════════╝
```

Repeat for each technology in the stack.

### If "no" (Skip)

```
╔══════════════════════════════════════════════════════════════════╗
║  ⏭️  SKIPPING BEST PRACTICES                                     ║
╠══════════════════════════════════════════════════════════════════╣
║                                                                  ║
║  No problem! I'll use basic/minimal patterns.                    ║
║                                                                  ║
║  💡 You can always add best practices later by saying:           ║
║     "Apply best practices for [technology]"                      ║
║                                                                  ║
║  Proceeding to architecture design...                            ║
║                                                                  ║
╚══════════════════════════════════════════════════════════════════╝
```

---

## Step 3: Update DESIGN_STATE.yaml

After applying practices, update the state file:

```yaml
tech_best_practices:
  enabled: true
  asked_at: "2026-01-21T10:00:00Z"

  user_preference:
    apply_best_practices: true  # or "selective" or false
    selective_choices:
      - "nestjs:1,2,3,4,5"      # Only if "selective"
      - "react:all"

  applied_practices:
    backend:
      - name: "NestJS Best Practices"
        patterns:
          - "Modular architecture with feature modules"
          - "DTOs with class-validator for validation"
          - "Repository pattern for data access"
          - "Exception filters for error handling"
          - "Guards for authentication/authorization"
        applied: true

    frontend:
      - name: "React 18+ Best Practices"
        patterns:
          - "Functional components with hooks"
          - "Custom hooks for reusable logic"
          - "Error boundaries for error handling"
          - "React.lazy for code splitting"
        applied: true

    database:
      - name: "PostgreSQL Best Practices"
        patterns:
          - "Proper indexing strategies"
          - "Migrations via Prisma"
          - "Connection pooling"
        applied: true

    security:
      - name: "General Security Best Practices"
        patterns:
          - "Input validation and sanitization"
          - "Parameterized queries"
          - "CORS configuration"
          - "Rate limiting"
        applied: true
```

---

## Step 4: Feed Practices to Skeleton Creation

When A Session creates skeleton code, it should incorporate the applied practices.

### Example: NestJS with Best Practices

```typescript
// apps/backend/src/modules/auth/auth.module.ts
// Applied: Modular architecture with feature modules

import { Module } from '@nestjs/common';
import { AuthController } from './auth.controller';
import { AuthService } from './auth.service';
import { AuthRepository } from './auth.repository';

@Module({
  controllers: [AuthController],
  providers: [AuthService, AuthRepository],
  exports: [AuthService],
})
export class AuthModule {}
```

```typescript
// apps/backend/src/modules/auth/dto/register.dto.ts
// Applied: DTOs with class-validator

import { IsEmail, IsString, MinLength, MaxLength } from 'class-validator';

export class RegisterDto {
  @IsEmail()
  email: string;

  @IsString()
  @MinLength(8)
  @MaxLength(100)
  password: string;

  // TODO(B): Add additional fields as needed
}
```

```typescript
// apps/backend/src/modules/auth/auth.service.ts
// Applied: Repository pattern for data access

export class AuthService {
  constructor(
    private readonly authRepository: AuthRepository,  // Repository pattern
    private readonly jwtService: JwtService,
  ) {}

  /**
   * TODO(B): Implement user registration
   *
   * Best Practices Applied:
   * - Use DTO for input validation (already validated by controller)
   * - Use repository for data access (don't call Prisma directly)
   * - Use exception filters for errors (throw NestJS exceptions)
   */
  async register(dto: RegisterDto): Promise<AuthResult> {
    throw new Error('Not implemented - TODO(B)');
  }
}
```

---

## Available Best Practice Catalogs

The full catalog is stored in `DESIGN_STATE.yaml` under `tech_best_practices.available_practices.catalog`.

### Priority Order

**Always prefer practices from the technology creator/authority:**

| Priority | Domain | Source |
|----------|--------|--------|
| **0 (FIRST)** | Claude Code Plugin | Anthropic (always apply first) |
| 1 | Vercel React/Web Design | [skills.sh/vercel-labs](https://skills.sh/vercel-labs/agent-skills/vercel-react-best-practices) |
| 1 | Anthropic Frontend | [skills.sh/anthropics](https://skills.sh/anthropics/skills/frontend-design) |
| 1 | Agent Browser | [skills.sh/vercel-labs/agent-browser](https://skills.sh/vercel-labs/agent-browser/agent-browser) |
| 2 | Remotion Video | [skills.sh/remotion-dev](https://skills.sh/remotion-dev/skills/remotion-best-practices) |
| 2 | AI Development | Anthropic (Claude) > Google > Cursor > OpenAI |
| 2 | Next.js / SSR / Edge | Vercel (official) |
| 2 | React | Meta / React Team |
| 2 | Android | Google (official) |
| 2 | iOS | Apple (official) |
| 2 | Java | Oracle > Google > Spring Team (VMware) |
| 2 | TypeScript | Microsoft |
| 2 | Vue | Vue Team (Evan You) |

### External Skills (from skills.sh)

These external skills should be fetched and applied when available:

| Skill | URL | Authority |
|-------|-----|-----------|
| Vercel React Best Practices | https://skills.sh/vercel-labs/agent-skills/vercel-react-best-practices | Vercel Labs |
| Web Design Guidelines | https://skills.sh/vercel-labs/agent-skills/web-design-guidelines | Vercel Labs |
| Remotion Best Practices | https://skills.sh/remotion-dev/skills/remotion-best-practices | Remotion Dev |
| Anthropic Frontend Design | https://skills.sh/anthropics/skills/frontend-design | Anthropic |
| Agent Browser | https://skills.sh/vercel-labs/agent-browser/agent-browser | Vercel Labs |

### Quick Reference

| Category | Technologies | Authority |
|----------|--------------|-----------|
| **Claude Code Plugin** | CLAUDE.md, MCP, Three-Session | Anthropic (ALWAYS FIRST) |
| **External Skills** | Vercel, Anthropic, Remotion | skills.sh |
| **AI Development** | Claude, Gemini, Cursor, OpenAI | Anthropic, Google, OpenAI |
| **Frontend (SSR)** | Next.js, Vercel Edge | Vercel, Vercel Labs Skills |
| **Frontend (React)** | React 18+ | Meta, Vercel Labs Skills |
| **Web Design** | UI/UX, Design Systems | Vercel Labs, Anthropic Skills |
| **Video** | Remotion | Remotion Dev Skills |
| **Browser Automation** | Agent Browser | Vercel Labs Skills |
| **Frontend (Vue)** | Vue 3, Nuxt 3 | Vue Team |
| **Mobile (Android)** | Jetpack Compose, Kotlin | Google |
| **Mobile (iOS)** | SwiftUI, Swift | Apple |
| **Java Ecosystem** | Java, Spring Boot, Quarkus, Micronaut | Oracle > Google > Spring Team |
| **Backend** | NestJS, Express, FastAPI, Firebase | Framework teams, Google |
| **Database** | PostgreSQL, MongoDB | Official docs |
| **State** | Zustand, Redux Toolkit | Library teams |
| **Testing** | Jest, Vitest, Playwright | Meta, Vitest Team, Microsoft |
| **Language** | TypeScript, Kotlin, Swift | Microsoft, JetBrains, Apple |
| **API** | RESTful, GraphQL | GraphQL Foundation |
| **Security** | General practices | OWASP |
| **Deployment** | Vercel, Edge Runtime | Vercel |

---

## Commands for Later Use

After initialization, users can invoke best practices with:

| Command | Description |
|---------|-------------|
| "Apply best practices" | Re-run best practices for current stack |
| "Apply best practices for React" | Apply specific tech practices |
| "Show applied practices" | Display current applied practices |
| "Add practice: X" | Add specific practice manually |

---

## Integration with B Session

B Session should be aware of applied practices:

```yaml
# In task handoff
handoff_id: "HO-001"

best_practices_context:
  applied:
    - "NestJS: Repository pattern"
    - "React: Functional components with hooks"
    - "PostgreSQL: Migrations via Prisma"

  constraints_from_practices:
    - "Do not call Prisma directly - use repository"
    - "Use class-validator DTOs for all inputs"
    - "Implement error boundaries for React components"
```

This ensures B Session follows the selected practices when filling in TODOs.

---

## Integration with C Session

C Session validates code against applied practices:

```yaml
# In review checklist
review_checklist:
  best_practices_compliance:
    - check: "Repository pattern used"
      pass: true
    - check: "DTOs have class-validator decorators"
      pass: true
    - check: "Error boundaries implemented"
      pass: false
      issue: "Missing error boundary in UserProfile component"
```
