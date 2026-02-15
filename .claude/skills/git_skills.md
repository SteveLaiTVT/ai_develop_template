# Git Version Control Skills (Mandatory)

**强制性技能** - 所有 Agent 在进行开发工作时必须遵循这些 Git 版本控制规范。

This skill is **MANDATORY** for all agents working on development tasks. All code changes must follow these Git version control practices.

---

## Core Principles

### 1. Atomic Commits

```
每次提交应该是一个完整的、独立的逻辑单元
Each commit should be a complete, independent logical unit
```

**Good Examples:**
- `feat(auth): add login API endpoint`
- `fix(user): correct email validation regex`
- `refactor(payment): extract payment gateway logic`

**Bad Examples:**
- `WIP` (work in progress - too vague)
- `fix bugs` (no context)
- `update files` (no meaning)

### 2. Meaningful Commit Messages

Follow the **Conventional Commits** format:

```
<type>(<scope>): <subject>

[optional body]

[optional footer]
```

**Types:**

| Type | Description | Example |
|------|-------------|---------|
| `feat` | New feature | `feat(auth): add OAuth2 login` |
| `fix` | Bug fix | `fix(cart): correct total calculation` |
| `docs` | Documentation | `docs: update API reference` |
| `style` | Code style (formatting) | `style: fix indentation` |
| `refactor` | Code refactoring | `refactor(db): optimize query` |
| `test` | Tests | `test(auth): add login tests` |
| `chore` | Build/tooling | `chore: update dependencies` |
| `perf` | Performance | `perf(api): cache responses` |

---

## Branch Strategy

### Standard Branch Structure

```
main                     # Production-ready code (protected)
  └── develop           # Integration branch
        ├── feature/*   # New features
        ├── fix/*       # Bug fixes
        ├── refactor/*  # Code refactoring
        └── release/*   # Release preparation
```

### Branch Naming Convention

```bash
# Feature branches
feature/user-authentication
feature/payment-integration
feature/dashboard-redesign

# Bug fix branches
fix/login-redirect-error
fix/cart-calculation-bug

# Refactoring branches
refactor/database-optimization
refactor/api-response-format

# Release branches
release/v1.2.0
```

### Creating Branches

```bash
# Start from latest develop
git checkout develop
git pull origin develop

# Create feature branch
git checkout -b feature/my-new-feature
```

---

## Workflow Procedures

### Before Starting Work

```bash
# 1. Ensure you're on the correct base branch
git checkout develop

# 2. Pull latest changes
git pull origin develop

# 3. Create your working branch
git checkout -b feature/your-feature-name
```

### During Development

```bash
# Stage specific files (preferred)
git add src/auth/login.ts

# Stage all changes in a directory
git add src/auth/

# Review what's staged
git status
git diff --staged

# Commit with meaningful message
git commit -m "feat(auth): implement login validation

- Add email format validation
- Add password strength check
- Handle empty field errors"
```

### Completing Work

```bash
# Ensure all changes are committed
git status

# Push to remote
git push origin feature/your-feature-name

# Create Pull Request (PR)
# PR title should follow commit message format
```

---

## Commit Frequency Guidelines

### When to Commit

| Situation | Action |
|-----------|--------|
| Completed a logical unit of work | ✅ Commit |
| Added a new function/method | ✅ Commit |
| Fixed a specific bug | ✅ Commit |
| Refactored a module | ✅ Commit |
| About to try experimental change | ✅ Commit (save point) |
| End of work session | ✅ Commit (if work is stable) |

### When NOT to Commit

| Situation | Action |
|-----------|--------|
| Code doesn't compile/build | ❌ Don't commit |
| Tests are failing | ❌ Fix first, then commit |
| Work is half-done with broken state | ❌ Complete or stash |
| Debugging console.log statements | ❌ Remove first |

---

## Git Operations Reference

### Viewing History

```bash
# View commit history
git log --oneline -10

# View with graph
git log --graph --oneline --all

# View specific file history
git log --oneline -- path/to/file

# Show specific commit details
git show <commit-hash>
```

### Managing Changes

```bash
# Discard unstaged changes
git checkout -- path/to/file

# Unstage a file
git reset HEAD path/to/file

# Stash changes temporarily
git stash
git stash pop  # Restore

# Amend last commit (before push)
git commit --amend
```

### Syncing with Remote

```bash
# Fetch updates without merging
git fetch origin

# Pull and rebase (cleaner history)
git pull --rebase origin develop

# Push to remote
git push origin branch-name
```

---

## Conflict Resolution

### Step-by-Step Resolution

```bash
# 1. Pull/merge and encounter conflicts
git pull origin develop

# 2. Git marks conflicting files
# Look for markers:
<<<<<<< HEAD
your changes
=======
incoming changes
>>>>>>> develop

# 3. Edit files to resolve conflicts

# 4. Stage resolved files
git add resolved-file.ts

# 5. Continue the operation
git commit -m "merge: resolve conflicts from develop"
```

### Best Practices for Avoiding Conflicts

1. **Pull frequently** - Stay updated with base branch
2. **Small, focused changes** - Less overlap with others
3. **Communicate** - Coordinate on shared files
4. **Use feature flags** - Avoid long-lived branches

---

## Protected Operations

### Operations Requiring Extra Care

```bash
# Force push (destructive - avoid if possible)
git push --force  # ⚠️ Use with extreme caution

# Reset to previous state
git reset --hard HEAD~1  # ⚠️ Loses uncommitted changes

# Delete branch
git branch -D branch-name  # ⚠️ Permanent deletion
```

### Safe Alternatives

```bash
# Instead of force push, use
git push --force-with-lease  # Safer - checks for others' changes

# Instead of hard reset
git revert <commit>  # Creates new commit undoing changes

# Instead of delete, archive
git tag archive/branch-name branch-name
git branch -D branch-name
```

---

## Pre-Commit Checklist

Before every commit, verify:

```
✓ Code compiles/builds successfully
✓ All tests pass (or test specific module)
✓ No debug statements (console.log, print, etc.)
✓ No commented-out code (unless intentional)
✓ Code follows project style guide
✓ Commit message is meaningful
✓ Changes are related to stated purpose
```

---

## Integration with AI Development Workflow

### A Session (Architect)
- Creates design branches: `design/iter-XXX`
- Commits DESIGN_STATE.yaml changes
- Tags versions after iterations

### B Session (Implementer)
- Creates feature branches from iteration branch
- Commits frequently with detailed messages
- Pushes before code review

### C Session (Reviewer)
- Reviews on PR/MR
- Suggests changes via review comments
- Approves merge when satisfied

---

## Common Mistakes to Avoid

| Mistake | Why It's Bad | Better Approach |
|---------|-------------|-----------------|
| `git add .` blindly | May include unwanted files | Stage files explicitly |
| Commit message "fix" | No context for future | Describe what and why |
| Long-lived branches | Merge conflicts pile up | Merge frequently |
| Ignoring .gitignore | Commits binaries/secrets | Review gitignore first |
| Skipping pull before push | Creates unnecessary merges | Always pull first |

---

## Quick Reference Card

```bash
# Daily workflow
git checkout develop && git pull
git checkout -b feature/my-feature
# ... make changes ...
git add <files>
git commit -m "type(scope): description"
git push origin feature/my-feature
# Create PR

# Sync with develop
git fetch origin
git rebase origin/develop
# or
git merge origin/develop

# View status
git status
git log --oneline -5
git diff
```
