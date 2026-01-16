# Claude Cowork Integration Guide

This template explains how to use Claude Cowork with the AI Development Workflow.

## What is Claude Cowork?

Claude Cowork is Anthropic's feature that brings Claude Code's agentic capabilities to a broader audience through the Claude Desktop app on macOS. It allows Claude to:

- **Read, edit, and create files** in authorized folders
- **Navigate websites** (with Chrome extension)
- **Connect to third-party apps** via Connectors (Asana, Notion, Canva, etc.)

## When to Use Cowork

### Recommended For

| Use Case | Why Cowork Helps |
|----------|------------------|
| Non-developers | Friendlier interface, no terminal needed |
| Quick file operations | Direct file access without CLI commands |
| Visual testing | Browser navigation for frontend testing |
| Integration tasks | Connectors for project management tools |
| Design review | Access to design files and mockups |

### Still Use Claude Code For

| Use Case | Why Code is Better |
|----------|-------------------|
| Complex builds | Full terminal access for npm, cargo, etc. |
| Database operations | Direct access to databases and migrations |
| CI/CD workflows | GitHub Actions, deployment pipelines |
| Multi-container setups | Docker Compose orchestration |

## Setting Up Cowork

### Prerequisites

```
1. Claude Max subscription ($100-200/month)
2. macOS with Claude Desktop app
3. Optional: Claude Chrome extension
```

### Step-by-Step Setup

```
┌─────────────────────────────────────────────────────────────────┐
│  CLAUDE COWORK SETUP                                            │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  Step 1: Open Claude Desktop                                    │
│  └── Launch from Applications                                   │
│                                                                 │
│  Step 2: Enable Cowork Feature                                  │
│  └── Find in settings or main interface                         │
│                                                                 │
│  Step 3: Grant Folder Access                                    │
│  └── Select your project folder                                 │
│  └── Example: ~/Projects/my-app                                 │
│                                                                 │
│  Step 4: (Optional) Install Chrome Extension                    │
│  └── Enables web navigation capabilities                        │
│                                                                 │
│  Step 5: (Optional) Connect Third-Party Apps                    │
│  └── Asana, Notion, Canva, etc.                                 │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

## Cowork + Three-Session Workflow

### How Each Session Benefits

#### A Session (Architect) with Cowork

```
┌─────────────────────────────────────────────────────────────────┐
│  A SESSION + COWORK BENEFITS                                    │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  Discovery Phase:                                               │
│  • Browse competitor apps for research                          │
│  • Access Notion/Asana for existing requirements                │
│  • View Canva designs for brand guidelines                      │
│                                                                 │
│  Design Phase:                                                  │
│  • Reference documentation sites directly                       │
│  • Compare against design mockups                               │
│  • Access project management boards                             │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

#### B Session (Implementer) with Cowork

```
┌─────────────────────────────────────────────────────────────────┐
│  B SESSION + COWORK BENEFITS                                    │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  Implementation Phase:                                          │
│  • Simplified file editing interface                            │
│  • No terminal complexity for basic edits                       │
│  • Direct access to project files                               │
│                                                                 │
│  Self-Test Phase:                                               │
│  • Browser navigation for frontend testing                      │
│  • Visual verification of UI changes                            │
│  • Screenshot capabilities for documentation                    │
│                                                                 │
│  Note: Still needs Claude Code for:                             │
│  • Running npm/yarn commands                                    │
│  • Database migrations                                          │
│  • Docker operations                                            │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

#### C Session (Reviewer) with Cowork

```
┌─────────────────────────────────────────────────────────────────┐
│  C SESSION + COWORK BENEFITS                                    │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  Review Phase:                                                  │
│  • Visual inspection of frontend implementations                │
│  • Compare UI against design mockups in Canva                   │
│  • Access issue trackers for bug reporting                      │
│                                                                 │
│  Feedback Phase:                                                │
│  • Create issues in Asana/Notion directly                       │
│  • Annotate screenshots for feedback                            │
│  • Link to documentation for reference                          │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

## Detection and Configuration

### Session Start Detection

When starting a session, check if Cowork is available:

```yaml
# In DESIGN_STATE.yaml, update cowork.current_session:
cowork:
  current_session:
    is_cowork: true  # Detected at session start
    granted_folders:
      - "/Users/name/Projects/my-app"
    active_connectors:
      - "notion"
      - "asana"
    browser_enabled: true
```

### Session Start Banner (Cowork Mode)

```
╔══════════════════════════════════════════════════════════════════╗
║                    🚀 AI DEVELOPMENT WORKFLOW                     ║
║                    🔗 COWORK MODE ACTIVE                          ║
╠══════════════════════════════════════════════════════════════════╣
║ Current Phase: [phase]                                           ║
║ Project: [project_name]                                          ║
╠══════════════════════════════════════════════════════════════════╣
║ COWORK CAPABILITIES                                              ║
║ ┌────────────────────────────────────────────────────────────┐   ║
║ │ ✅ File Access: ~/Projects/my-app                          │   ║
║ │ ✅ Browser Navigation: Enabled                             │   ║
║ │ ✅ Connectors: Notion, Asana                               │   ║
║ └────────────────────────────────────────────────────────────┘   ║
╠══════════════════════════════════════════════════════════════════╣
║ 👉 Cowork enhances this workflow with direct file access         ║
║    and web navigation capabilities                               ║
╚══════════════════════════════════════════════════════════════════╝
```

## User Choice Prompt

When Cowork is detected but not yet configured, ask the user:

```
╔══════════════════════════════════════════════════════════════════╗
║  🔗 CLAUDE COWORK DETECTED                                       ║
╠══════════════════════════════════════════════════════════════════╣
║                                                                  ║
║  I've detected you're using Claude Cowork.                       ║
║                                                                  ║
║  Would you like to enable Cowork integration?                    ║
║                                                                  ║
║  Benefits:                                                       ║
║  • Simplified file operations                                    ║
║  • Browser-based frontend testing                                ║
║  • Third-party app connections                                   ║
║                                                                  ║
║  Options:                                                        ║
║  1. Yes, enable Cowork integration                               ║
║  2. No, use standard workflow                                    ║
║  3. Tell me more about Cowork                                    ║
║                                                                  ║
╚══════════════════════════════════════════════════════════════════╝
```

## Cowork-Enhanced Workflows

### Visual Frontend Testing with Cowork

Instead of agent-browser CLI, Cowork can:

```
1. Navigate to http://localhost:5173/login
2. Take screenshot of the page
3. Interact with form elements
4. Verify visual rendering
5. Report results with screenshots
```

### Design Comparison Workflow

```
1. Access design mockup in Canva via Connector
2. Open implemented page in browser
3. Compare visually
4. Note discrepancies in review report
```

### Requirements from Project Management

```
1. Connect to Asana/Notion via Connector
2. Fetch task requirements directly
3. Update DESIGN_STATE.yaml with requirements
4. Mark tasks complete in project management tool
```

## Security Considerations

### Important Safety Notes

```
⚠️ COWORK SECURITY REMINDERS

1. Sandboxing
   • Cowork runs in isolated Linux VM
   • Files are accessed through virtualization layer

2. File Access Scope
   • Claude can ONLY access folders you explicitly grant
   • Never grant access to entire home directory
   • Use project-specific folders only

3. Destructive Actions
   • Claude CAN delete files in granted folders
   • Always use git for version control
   • Review changes before approving

4. Recommendations
   • Grant access to: ~/Projects/my-app ✅
   • Don't grant: ~/ (home directory) ❌
   • Keep .git folder for easy rollback
   • Review Claude's proposed changes
```

## Hybrid Workflow: Cowork + Claude Code

For complex projects, use both:

```
┌─────────────────────────────────────────────────────────────────┐
│              HYBRID WORKFLOW: COWORK + CODE                     │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  Use COWORK for:                                                │
│  • Reading/writing source files                                 │
│  • Visual frontend testing                                      │
│  • Design reference browsing                                    │
│  • Project management access                                    │
│                                                                 │
│  Use CLAUDE CODE for:                                           │
│  • npm/yarn/pnpm commands                                       │
│  • Database migrations                                          │
│  • Docker operations                                            │
│  • Git operations                                               │
│  • Running test suites                                          │
│  • Build processes                                              │
│                                                                 │
│  Switch between them based on task needs!                       │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

## Updating DESIGN_STATE.yaml for Cowork

When using Cowork, update the configuration:

```yaml
# Enable Cowork in DESIGN_STATE.yaml
cowork:
  enabled: true

  capabilities:
    file_access:
      enabled: true
      granted_folders:
        - "/Users/yourname/Projects/my-app"

    browser_navigation:
      enabled: true

    connectors:
      enabled: true
      available:
        - "notion"
        - "asana"
        - "canva"

  current_session:
    is_cowork: true
    granted_folders:
      - "/Users/yourname/Projects/my-app"
    active_connectors:
      - "notion"
    browser_enabled: true
```

## Availability Note

As of January 2026, Claude Cowork is available as a **research preview** for:
- **Claude Max subscribers** ($100-200/month)
- **macOS only** (via Claude Desktop app)
- Other plans can join a waitlist

---

*For more information, see:*
- *Anthropic's official Cowork announcement*
- *DESIGN_STATE.yaml section 10 (Cowork Integration)*
- *Session prompts for Cowork-aware workflows*
