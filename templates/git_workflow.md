# GIT_WORKFLOW - Version Control Strategy

This document defines how git is used in the AI-assisted development workflow.

---

## Branch Strategy

```
main                    # Production-ready code
  └── develop           # Integration branch
        ├── iter-001    # Iteration branch
        │     ├── task-001-auth-backend
        │     ├── task-002-auth-frontend
        │     └── task-003-auth-fix
        └── iter-002
              └── ...
```

## Workflow by Session

### A Session (Design & Decision)

```bash
# Start new iteration
git checkout develop
git pull origin develop
git checkout -b iter-XXX

# After design decisions
git add .claude/DESIGN_STATE.yaml
git add docs/decisions/ADR-XXX.md
git commit -m "design(iter-XXX): [decision description]"
```

### B Session (Implementation)

```bash
# Start task
git checkout iter-XXX
git checkout -b task-XXX-[short-description]

# During implementation - commit frequently
git add [files]
git commit -m "feat(module): [what was implemented]"

# After completing task
git push origin task-XXX-[short-description]
# Create PR to iter-XXX branch
```

### C Session (Review)

```bash
# Review happens on PR
# If changes needed, B Session fixes and force-pushes

# After approval
git checkout iter-XXX
git merge task-XXX-[short-description]
git push origin iter-XXX
```

### Iteration Complete

```bash
# Merge iteration to develop
git checkout develop
git merge iter-XXX
git tag -a v0.X.0 -m "Release iter-XXX"
git push origin develop --tags

# Cleanup
git branch -d iter-XXX
git push origin --delete iter-XXX
```

---

## Commit Message Convention

### Format

```
<type>(<scope>): <subject>

[optional body]

[optional footer]
```

### Types

| Type | Description | Example |
|------|-------------|---------|
| `feat` | New feature | `feat(auth): add login API` |
| `fix` | Bug fix | `fix(auth): handle empty password` |
| `design` | Design decision | `design(iter-001): define auth module` |
| `refactor` | Code refactoring | `refactor(auth): extract validation` |
| `docs` | Documentation | `docs: update API spec` |
| `test` | Tests | `test(auth): add login tests` |
| `chore` | Build/tooling | `chore: update dependencies` |
| `style` | Code style | `style: fix linting errors` |

### Scope

Use module or component name:
- `auth`, `user`, `profile` (backend modules)
- `admin-web`, `user-web` (frontend apps)
- `android`, `ios` (mobile apps)
- `types`, `api-client` (packages)
- `iter-XXX` (iteration-level changes)

### Examples

```bash
# Feature
git commit -m "feat(auth): implement JWT token generation

- Use RS256 algorithm
- Token expires in 15 minutes
- Include user role in payload

Task: TASK-001"

# Bug fix
git commit -m "fix(user-web): prevent white screen after login

Root cause: API response missing 'name' field
Solution: Add null check before accessing user.name

Bug: BUG-042"

# Design decision
git commit -m "design(iter-001): define auth module structure

- Controller/Service/Repository pattern
- JWT with refresh token rotation
- bcrypt for password hashing

ADR: ADR-001"

# Refactor
git commit -m "refactor(auth): split register function

Extract validation and token generation to separate methods
to comply with 50-line function limit.

Review: RR-001, ISS-001"
```

---

## Tagging Strategy

### Version Format

```
v<major>.<minor>.<patch>

major: Breaking changes
minor: New features (iteration complete)
patch: Bug fixes
```

### Tag Examples

```bash
# Iteration complete
git tag -a v0.1.0 -m "iter-001: User authentication MVP"

# Bug fix release
git tag -a v0.1.1 -m "Fix login white screen bug"

# Major release
git tag -a v1.0.0 -m "Production release"
```

---

## Integration with AI Sessions

### Before Starting a Session

```bash
# A Session: Pull latest and check current state
git checkout develop
git pull origin develop
cat .claude/DESIGN_STATE.yaml | head -20  # Check version

# B Session: Checkout task branch
git checkout iter-XXX
git checkout -b task-YYY-description
```

### After Session Complete

```bash
# B Session: Commit implementation
git add .
git commit -m "feat(module): implement [feature]

Task: TASK-YYY
Design State: v0.1.0"

# A Session: Update design state
git add .claude/DESIGN_STATE.yaml
git commit -m "design(iter-XXX): update design state v0.1.0 -> v0.1.1

Changes:
- Added constraint X
- Updated task status

Changelog: CL-003"
```

### Linking to Templates

Include references in commit messages:

```bash
# Reference task
Task: TASK-XXX

# Reference bug
Bug: BUG-XXX

# Reference review
Review: RR-XXX

# Reference design decision
ADR: ADR-XXX

# Reference changelog
Changelog: CL-XXX
```

---

## .gitignore Recommendations

```gitignore
# Dependencies
node_modules/
.pnpm-store/

# Build outputs
dist/
build/
.next/

# Environment
.env
.env.local
.env.*.local

# IDE
.idea/
.vscode/
*.swp

# OS
.DS_Store
Thumbs.db

# Logs
*.log
logs/

# Test coverage
coverage/

# Temporary files
tmp/
temp/

# DO NOT ignore .claude/ - it's part of the project
# !.claude/
```

---

## Conflict Resolution

### DESIGN_STATE.yaml Conflicts

If multiple sessions modified DESIGN_STATE.yaml:

1. **A Session** resolves conflicts
2. Merge strategy: Always take the higher version number
3. Manually merge content changes
4. Create changelog entry for the merge

```bash
# A Session resolves
git checkout --ours .claude/DESIGN_STATE.yaml  # Take our version first
# Manually merge in changes from theirs
git add .claude/DESIGN_STATE.yaml
git commit -m "design: merge DESIGN_STATE conflicts v0.1.1 + v0.1.2 -> v0.1.3"
```

### Code Conflicts

If B Sessions have conflicting code:

1. **Later task** resolves conflicts
2. Ensure both features work after merge
3. Add test to verify
4. Request C Session re-review if significant changes
