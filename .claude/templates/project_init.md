# Project Initialization Template

This template guides the first-time setup when the AI Development Template is loaded in Claude Code or Claude Cowork.

## Initialization Detection

**Check if initialization is needed:**
```yaml
# In DESIGN_STATE.yaml
initialization:
  is_template: true      # If true, run initialization
  initialized: false     # If false, initialization not complete
```

## Initialization Flow

```
┌─────────────────────────────────────────────────────────────────┐
│                    PROJECT INITIALIZATION                        │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  Step 1: Welcome & Detection                                     │
│     └── Detect fresh template, greet user                        │
│                                                                  │
│  Step 2: Onboarding Questions                                    │
│     └── Ask about project, goals, preferences                    │
│                                                                  │
│  Step 3: Cowork Detection (Optional)                             │
│     └── Ask if user wants Cowork integration                     │
│                                                                  │
│  Step 4: Git Setup                                               │
│     └── Clear template .git, init new repo, set remote           │
│                                                                  │
│  Step 5: Project Configuration                                   │
│     └── Update DESIGN_STATE.yaml with user's answers             │
│                                                                  │
│  Step 6: Initial Commit                                          │
│     └── Commit initialized project                               │
│                                                                  │
│  Step 7: Launch Sessions                                         │
│     └── Start A/B/C sessions in parallel workflow                │
│                                                                  │
│  Step 8: Notify User                                             │
│     └── Tell user to test when first iteration complete          │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## Step 1: Welcome Banner

Display when `initialization.is_template: true`:

```
╔══════════════════════════════════════════════════════════════════╗
║                                                                  ║
║     🎉 WELCOME TO THE AI DEVELOPMENT TEMPLATE                    ║
║                                                                  ║
╠══════════════════════════════════════════════════════════════════╣
║                                                                  ║
║  I'm Claude, your AI development partner.                        ║
║                                                                  ║
║  This template helps you build applications with AI-assisted     ║
║  development using a three-session workflow:                     ║
║                                                                  ║
║  • A Session (Architect) - Designs and plans                     ║
║  • B Session (Implementer) - Writes code                         ║
║  • C Session (Reviewer) - Reviews and validates                  ║
║                                                                  ║
║  Let's set up your project! I'll ask a few questions first.      ║
║                                                                  ║
╚══════════════════════════════════════════════════════════════════╝
```

---

## Step 2: Onboarding Questions

### Question Set

Ask these questions to understand user's intent:

```
╔══════════════════════════════════════════════════════════════════╗
║  📋 PROJECT SETUP - Let's get started!                           ║
╠══════════════════════════════════════════════════════════════════╣
║                                                                  ║
║  Q1: What would you like to call this project?                   ║
║      (e.g., "my-awesome-app", "task-tracker", "e-commerce")      ║
║                                                                  ║
║  Q2: Briefly describe what you want to build:                    ║
║      (1-2 sentences about your app's purpose)                    ║
║                                                                  ║
║  Q3: What type of project is this?                               ║
║      • web-app      - Browser-based application                  ║
║      • mobile-app   - iOS/Android application                    ║
║      • api-service  - Backend API only                           ║
║      • full-stack   - Frontend + Backend + possibly Mobile       ║
║      • other        - Something else                             ║
║                                                                  ║
║  Q4: Do you have a tech stack preference?                        ║
║      • "recommend"  - Let me suggest based on your project       ║
║      • Or specify   - e.g., "React + Node.js + PostgreSQL"       ║
║                                                                  ║
║  Q5: Do you have a Git remote repository? (OPTIONAL)             ║
║      • Enter URL (e.g., github.com/username/my-project)          ║
║      • Or "skip" - we'll use local git only (recommended start)  ║
║      💡 You can add a remote later when you're ready to share    ║
║                                                                  ║
╚══════════════════════════════════════════════════════════════════╝
```

### Recording Answers

Update `DESIGN_STATE.yaml`:

```yaml
initialization:
  onboarding:
    completed: true
    answers:
      project_name: "task-tracker"
      project_description: "A simple task management app for teams"
      project_type: "full-stack"
      use_cowork: true
      git_remote: "github.com/user/task-tracker"
      preferred_stack: "recommend"

meta:
  project_name: "task-tracker"
  description: "A simple task management app for teams"
```

---

## Step 3: Cowork Detection

If running in Claude Cowork environment or user indicates macOS + Claude Desktop:

```
╔══════════════════════════════════════════════════════════════════╗
║  🔗 CLAUDE COWORK AVAILABLE                                      ║
╠══════════════════════════════════════════════════════════════════╣
║                                                                  ║
║  I detected you may be using Claude Cowork!                      ║
║                                                                  ║
║  Would you like to enable Cowork integration?                    ║
║                                                                  ║
║  Benefits:                                                       ║
║  • Simplified file operations                                    ║
║  • Browser-based frontend testing                                ║
║  • Third-party app connections (Asana, Notion, etc.)             ║
║                                                                  ║
║  Options:                                                        ║
║  1. Yes, enable Cowork                                           ║
║  2. No, use standard Claude Code workflow                        ║
║                                                                  ║
╚══════════════════════════════════════════════════════════════════╝
```

---

## Step 4: Git Setup (Local-First)

**Git remote is OPTIONAL.** We use a local-first approach - you can add a remote later when ready.

### Step 4.1: Clear Template Git

```bash
# Remove template's git history
rm -rf .git
```

### Step 4.2: Create .gitignore

Create a comprehensive `.gitignore` file:

```bash
cat > .gitignore << 'EOF'
# Dependencies
node_modules/
vendor/
.pnpm-store/

# Build outputs
dist/
build/
out/
.next/
.nuxt/
.output/

# Environment files (IMPORTANT: never commit secrets)
.env
.env.local
.env.*.local
*.env

# IDE and editor files
.idea/
.vscode/
*.swp
*.swo
.DS_Store
Thumbs.db

# Logs and debug
*.log
npm-debug.log*
yarn-debug.log*
yarn-error.log*
debug.log

# Test coverage
coverage/
.nyc_output/

# Cache
.cache/
.parcel-cache/
.eslintcache
.prettiercache
*.tsbuildinfo

# OS files
.DS_Store
.DS_Store?
._*
.Spotlight-V100
.Trashes
ehthumbs.db

# Python (if using)
__pycache__/
*.py[cod]
*$py.class
.Python
*.egg-info/
.eggs/
venv/
.venv/

# Mobile (if using)
*.apk
*.aab
*.ipa
*.dSYM.zip
*.dSYM
android/.gradle/
android/app/build/
ios/Pods/
ios/build/

# Database (local dev)
*.sqlite
*.db

# Temporary files
tmp/
temp/
*.tmp
*.temp
EOF
```

### Step 4.3: Initialize Git Repository

```bash
# Initialize new git repository
git init

# Set default branch to main
git branch -M main
```

### Step 4.4: Add Remote (OPTIONAL)

Only if user provided a remote URL:

```bash
# Add remote origin (OPTIONAL - skip if user said "skip")
git remote add origin https://github.com/user/project.git
```

### Update State

```yaml
initialization:
  git_setup:
    cleared_template_git: true
    created_gitignore: true
    initialized_new_repo: true
    created_main_branch: true
    initial_commit: false  # Will be true after commit
    remote_added: false    # true only if user provided remote
    pushed_to_remote: false
```

---

## Step 5: Project Configuration

Update all relevant sections in `DESIGN_STATE.yaml`:

```yaml
meta:
  project_name: "<user's project name>"
  description: "<user's description>"

initialization:
  is_template: false  # No longer a template
  initialized: false  # Will be true after all steps

# Based on project_type, pre-configure architecture:
project_analysis:
  has_frontend: true   # if web-app or full-stack
  has_backend: true    # if api-service or full-stack
  has_mobile: true     # if mobile-app

# If user chose "recommend" for stack:
architecture:
  # Based on project_type, suggest appropriate stack
```

---

## Step 6: Initial Commit

Make the first commit with the user's project intent:

```bash
git add .
git commit -m "$(cat <<'EOF'
chore: Initialize <project_name>

<project_description>

Project Type: <project_type>
Tech Stack: <preferred_stack or recommended>

Initialized with AI Development Template:
- Three-session workflow (Architect/Implementer/Reviewer)
- DESIGN_STATE.yaml configuration
- OpenSpec integration
- Cowork integration: <enabled/disabled>

Ready for discovery phase.
EOF
)"
```

### Push to Remote (OPTIONAL)

Only if user provided a remote URL:

```bash
# Push to remote (ONLY if remote was added)
git push -u origin main
```

**If no remote:** That's fine! The project stays local. User can add remote later:

```bash
# Add remote later when ready
git remote add origin https://github.com/user/project.git
git push -u origin main
```

---

## Step 7: Launch Sessions

### Transition to Discovery

```yaml
workflow_state:
  current_phase: "discovery"  # Move from initialization

initialization:
  is_template: false
  initialized: true
  initialized_at: "2026-01-16T12:00:00Z"
```

### Session Launch Banner

```
╔══════════════════════════════════════════════════════════════════╗
║  ✅ PROJECT INITIALIZED SUCCESSFULLY!                            ║
╠══════════════════════════════════════════════════════════════════╣
║                                                                  ║
║  Project: <project_name>                                         ║
║  Type: <project_type>                                            ║
║  Git: Local repository (remote: <url or "not configured">)       ║
║  Cowork: <Enabled/Disabled>                                      ║
║                                                                  ║
╠══════════════════════════════════════════════════════════════════╣
║                                                                  ║
║  🚀 LAUNCHING AI DEVELOPMENT WORKFLOW                            ║
║                                                                  ║
║  The three-session workflow is now starting:                     ║
║                                                                  ║
║  ┌────────────┬────────────┬────────────┐                       ║
║  │ A Session  │ B Session  │ C Session  │                       ║
║  │ Architect  │ Implementer│ Reviewer   │                       ║
║  │ ▶ Starting │ Waiting    │ Waiting    │                       ║
║  └────────────┴────────────┴────────────┘                       ║
║                                                                  ║
║  A Session will now run discovery to understand your needs.      ║
║  Then work proceeds automatically through implementation         ║
║  and review cycles.                                              ║
║                                                                  ║
║  💡 You'll be notified when the first iteration is ready         ║
║     for real-world testing!                                      ║
║                                                                  ║
╚══════════════════════════════════════════════════════════════════╝
```

### Git Workflow (Local-First)

```
┌─────────────────────────────────────────────────────────────────┐
│                    LOCAL GIT WORKFLOW                            │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  main branch (stable)                                            │
│       │                                                          │
│       ├── feature/task-001 ──► B Session implements              │
│       │         │                                                │
│       │         └──► C Session reviews                           │
│       │                   │                                      │
│       │                   ├── ❌ Issues found → B fixes           │
│       │                   │                                      │
│       │                   └── ✅ Approved → Merge to main         │
│       │                             │                            │
│       ◄─────────────────────────────┘                            │
│       │                                                          │
│       └── (Later) Push to remote when ready                      │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘

After C Session Approval:
  git checkout main
  git merge feature/task-001
  git branch -d feature/task-001   # Delete merged branch
```

**No remote? No problem!** Everything works locally until you're ready to share.

### Parallel Session Workflow

After discovery, sessions work in parallel:

```
┌─────────────────────────────────────────────────────────────────┐
│                    PARALLEL SESSION WORKFLOW                     │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  Phase 1: Discovery (Sequential)                                 │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │ A Session asks discovery questions                        │   │
│  │ User answers → Requirements captured                      │   │
│  │ A Session creates initial design + skeleton code          │   │
│  └──────────────────────────────────────────────────────────┘   │
│                        │                                         │
│                        ▼                                         │
│  Phase 2: Implementation (Parallel)                              │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │                                                           │   │
│  │  A Session          B Session          C Session          │   │
│  │  ───────────        ───────────        ───────────        │   │
│  │  • Refining         • Filling TODOs    • (Waiting)        │   │
│  │    architecture     • Self-testing                        │   │
│  │  • Planning         • Creating PRs                        │   │
│  │    next tasks                                             │   │
│  │                                                           │   │
│  └──────────────────────────────────────────────────────────┘   │
│                        │                                         │
│                        ▼                                         │
│  Phase 3: Review (Parallel)                                      │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │                                                           │   │
│  │  A Session          B Session          C Session          │   │
│  │  ───────────        ───────────        ───────────        │   │
│  │  • Processing       • Bugfix mode      • Reviewing        │   │
│  │    C feedback       • Fixing issues      code             │   │
│  │  • Planning                            • Validating       │   │
│  │    next iter                             constraints      │   │
│  │                                                           │   │
│  └──────────────────────────────────────────────────────────┘   │
│                        │                                         │
│                        ▼                                         │
│  Phase 4: User Testing                                           │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │                                                           │   │
│  │  🔔 NOTIFICATION: First iteration ready for testing!      │   │
│  │                                                           │   │
│  │  User tests the implementation in real world              │   │
│  │  Reports bugs → A Session processes → B Session fixes     │   │
│  │                                                           │   │
│  └──────────────────────────────────────────────────────────┘   │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## Step 8: User Notification

When first iteration is complete and ready for testing:

```
╔══════════════════════════════════════════════════════════════════╗
║  🎉 FIRST ITERATION COMPLETE - READY FOR TESTING!                ║
╠══════════════════════════════════════════════════════════════════╣
║                                                                  ║
║  Great news! The first iteration of task-tracker is ready.       ║
║                                                                  ║
║  What was built:                                                 ║
║  • User authentication (register, login)                         ║
║  • Basic task CRUD operations                                    ║
║  • Simple task list UI                                           ║
║                                                                  ║
║  Self-test results:                                              ║
║  ✅ Backend API: All endpoints working                           ║
║  ✅ Frontend UI: Login flow verified                             ║
║  ✅ Unit tests: 24/24 passing                                    ║
║                                                                  ║
╠══════════════════════════════════════════════════════════════════╣
║                                                                  ║
║  👉 YOUR TURN: Please test the application!                      ║
║                                                                  ║
║  To start:                                                       ║
║  1. cd apps/backend && npm run start:dev                         ║
║  2. cd apps/user-web && npm run dev                              ║
║  3. Open http://localhost:5173                                   ║
║                                                                  ║
║  Report any bugs using the bug report template, or just          ║
║  describe the issue and I'll create a fix task.                  ║
║                                                                  ║
╚══════════════════════════════════════════════════════════════════╝
```

---

## Initialization State Machine

```
                    ┌─────────────────────┐
                    │  Template Loaded    │
                    │  is_template: true  │
                    └─────────┬───────────┘
                              │
                              ▼
                    ┌─────────────────────┐
                    │  Welcome & Detect   │
                    └─────────┬───────────┘
                              │
                              ▼
                    ┌─────────────────────┐
                    │  Onboarding Q&A     │
                    └─────────┬───────────┘
                              │
                              ▼
                    ┌─────────────────────┐
                    │  Cowork Detection   │
                    └─────────┬───────────┘
                              │
                              ▼
                    ┌─────────────────────┐
                    │  Git Setup          │
                    │  • Clear .git       │
                    │  • Init new repo    │
                    │  • Add remote       │
                    └─────────┬───────────┘
                              │
                              ▼
                    ┌─────────────────────┐
                    │  Configure Project  │
                    │  • Update meta      │
                    │  • Set preferences  │
                    └─────────┬───────────┘
                              │
                              ▼
                    ┌─────────────────────┐
                    │  Initial Commit     │
                    │  • git add .        │
                    │  • git commit       │
                    │  • git push         │
                    └─────────┬───────────┘
                              │
                              ▼
                    ┌─────────────────────┐
                    │  Mark Initialized   │
                    │  is_template: false │
                    │  initialized: true  │
                    └─────────┬───────────┘
                              │
                              ▼
                    ┌─────────────────────┐
                    │  Launch Discovery   │
                    │  (A Session starts) │
                    └─────────┬───────────┘
                              │
                              ▼
                    ┌─────────────────────┐
                    │  Parallel Workflow  │
                    │  A ↔ B ↔ C Sessions │
                    └─────────┬───────────┘
                              │
                              ▼
                    ┌─────────────────────┐
                    │  Notify User        │
                    │  "Ready to test!"   │
                    └─────────────────────┘
```

---

## Quick Commands

After initialization, user can use these commands:

| Command | Description |
|---------|-------------|
| "Show status" | Display current workflow state |
| "Run discovery" | Start/continue discovery interview |
| "Create task" | A Session creates new task for B |
| "Show my tasks" | B Session shows pending tasks |
| "Review code" | C Session starts code review |
| "Report bug" | User reports issue for fixing |

---

## Troubleshooting

### Reset Initialization

If user wants to start over:

```yaml
# Reset these values in DESIGN_STATE.yaml
initialization:
  is_template: true
  initialized: false
```

Then delete `.git` and restart Claude.

### Skip Git Setup

If user doesn't have a remote repository ready:

```
Q: What's your GitHub/GitLab repository URL?
A: skip

# Git setup will be deferred:
initialization:
  git_setup:
    cleared_template_git: true
    initialized_new_repo: true
    remote_added: false  # Skipped
    initial_commit: true
```

User can add remote later with:
```bash
git remote add origin https://github.com/user/repo.git
git push -u origin main
```
