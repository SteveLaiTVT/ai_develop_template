# OpenSpec Integration Guide

## Overview

This template integrates [OpenSpec](https://github.com/Fission-AI/OpenSpec) to provide spec-driven development alongside the existing three-session AI workflow. OpenSpec adds explicit specification management to complement DESIGN_STATE.yaml.

## Quick Setup

### 1. Install Dependencies

```bash
npm install
```

This installs OpenSpec as a dev dependency. For global installation:

```bash
npm install -g @fission-ai/openspec@latest
```

### 2. Verify Installation

```bash
npm run openspec:list
```

You should see an empty list of changes (initially).

## How OpenSpec Fits Into the Workflow

### Traditional Flow (Without OpenSpec)
```
A Session → DESIGN_STATE.yaml → B Session → Implementation → C Session → Review
```

### Enhanced Flow (With OpenSpec)
```
A Session → DESIGN_STATE.yaml (Architecture)
    ↓
A Session → OpenSpec Proposal (Detailed Spec)
    ↓
Human Review & Refinement
    ↓
Approved Spec → B Session → Implementation
    ↓
C Session → Validation against Spec
    ↓
Archive to openspec/specs/
```

## Role-Specific Usage

### A Session (Architect) - Opus 4.5

**Responsibilities:**
1. Create high-level architecture in DESIGN_STATE.yaml
2. Create detailed OpenSpec proposals for specific features
3. Review and refine specs based on feedback
4. Approve specs for implementation

**Example Prompts:**

```
"Create an OpenSpec change proposal for user authentication API"
```

```
"Review the proposed spec and ensure it aligns with our API constraints
from DESIGN_STATE.yaml"
```

**What A Session Creates:**
- OpenSpec change proposals in `openspec/changes/`
- Detailed API contracts
- Implementation task breakdowns
- Acceptance criteria

### B Session (Implementer) - Sonnet 4.5

**Responsibilities:**
1. Read approved OpenSpec specs
2. Implement according to specifications
3. Report blockers or unclear specs
4. Create implementation reports

**Example Prompts:**

```
"Implement the authentication API according to the approved OpenSpec
in openspec/changes/add-auth-api/"
```

```
"The current spec for password reset is unclear about token expiration.
Create a question for A Session."
```

**What B Session Does:**
- Reads from `openspec/changes/` and `openspec/specs/`
- Implements code following specs exactly
- Uses existing implementation_report.md template
- Flags spec inconsistencies

### C Session (Reviewer) - Sonnet 4.5

**Responsibilities:**
1. Validate implementation matches OpenSpec specs
2. Check compliance with DESIGN_STATE.yaml constraints
3. Identify spec drift or outdated specs
4. Create review reports

**Example Prompts:**

```
"Review the authentication implementation against the OpenSpec
specification in openspec/changes/add-auth-api/"
```

**What C Session Checks:**
- Implementation matches spec requirements
- All acceptance criteria met
- Constraints from DESIGN_STATE.yaml followed
- Spec is still accurate

## Workflow Examples

### Example 1: New Feature Development

**Step 1: A Session - Create Proposal**
```
Prompt: "Create an OpenSpec change proposal for adding password reset
functionality. It should follow our API constraints and integrate with
the existing auth module."
```

A Session creates `openspec/changes/add-password-reset/` with:
- `SPEC.md` - Feature specification
- `TASKS.md` - Implementation tasks
- `ACCEPTANCE.md` - Criteria

**Step 2: Human Review**
Review the proposal, ask clarifying questions, refine as needed.

**Step 3: Approve & Implement**
```
B Session Prompt: "Implement the password reset feature according to
openspec/changes/add-password-reset/. Follow all specifications exactly."
```

**Step 4: Review**
```
C Session Prompt: "Review the password reset implementation against
the OpenSpec in openspec/changes/add-password-reset/."
```

**Step 5: Archive**
```bash
openspec archive add-password-reset --yes
```

### Example 2: Refactoring Existing Code

**Step 1: Document Current State**
```
A Session Prompt: "Create an OpenSpec spec documenting the current
authentication flow, then propose a refactoring to improve testability."
```

**Step 2: Review Refactoring Plan**
Ensure refactoring maintains backward compatibility per constraints.

**Step 3: Implement**
```
B Session Prompt: "Refactor the authentication module according to
openspec/changes/refactor-auth-testability/."
```

**Step 4: Validate**
```
C Session Prompt: "Verify the refactored auth module maintains the same
behavior and improves testability as specified."
```

## Integration with Existing Templates

OpenSpec works alongside existing templates:

### task_handoff.md (A → B)
**Before:**
```markdown
## Tasks
1. Implement auth registration
   - Validate email
   - Hash password
   - Generate JWT
```

**With OpenSpec:**
```markdown
## Tasks
1. Implement auth registration per OpenSpec
   - Spec: `openspec/changes/add-auth-api/`
   - Read SPEC.md for requirements
   - Follow TASKS.md for implementation order
   - Validate against ACCEPTANCE.md
```

### implementation_report.md (B → C)
**Add Section:**
```markdown
## OpenSpec Compliance
- [ ] All tasks from OpenSpec TASKS.md completed
- [ ] Acceptance criteria from ACCEPTANCE.md met
- [ ] No deviations from specification
- Deviations (if any): [explain why and get A Session approval]
```

### review_report.md (C → A)
**Add Section:**
```markdown
## Spec Validation
- [ ] Implementation matches OpenSpec specification
- [ ] All acceptance criteria verified
- [ ] DESIGN_STATE.yaml constraints followed
- Spec Issues Found:
  - [List any outdated or unclear specs]
  - [Suggest spec updates if needed]
```

## Best Practices

### 1. Spec Granularity
- **DESIGN_STATE.yaml**: High-level architecture, module structure, constraints
- **OpenSpec**: Detailed API contracts, implementation specifics, task breakdowns

### 2. Keep Them in Sync
When updating DESIGN_STATE.yaml:
- Update affected OpenSpec specs
- Create new change proposals for implementation

When archiving OpenSpec changes:
- Update DESIGN_STATE.yaml with completed features
- Reference archived specs in module documentation

### 3. Naming Conventions
```
openspec/changes/
├── add-[feature-name]/        # New features
├── refactor-[component]/      # Refactoring
├── fix-[issue-description]/   # Bug fixes
└── update-[what-changed]/     # Updates
```

### 4. Change Proposal Structure
Each change should include:
```
openspec/changes/[change-name]/
├── SPEC.md          # Feature specification
├── TASKS.md         # Implementation tasks
├── ACCEPTANCE.md    # Acceptance criteria
└── NOTES.md         # Additional context (optional)
```

### 5. When to Use Each Tool

**Use DESIGN_STATE.yaml for:**
- Overall architecture decisions
- Technology stack choices
- Code constraints and standards
- Module dependencies
- Current iteration status

**Use OpenSpec for:**
- Detailed API specifications
- Feature implementation plans
- Task breakdowns
- Acceptance testing criteria
- Incremental feature changes

## Commands Reference

### npm Scripts
```bash
npm run openspec:init    # Initialize OpenSpec (if needed)
npm run openspec:list    # List all active changes
npm run openspec:view    # Interactive dashboard
npm run openspec:show    # Show specific change details
```

### Direct Commands (if globally installed)
```bash
openspec list                      # List changes
openspec view                      # Interactive dashboard
openspec show <change-name>        # Show change
openspec archive <change-name>     # Archive completed change
```

### AI Tool Slash Commands (if supported)
```
/openspec:list                     # List changes
/openspec:show <change-name>       # Show change details
/openspec:apply <change-name>      # Apply change
/openspec:archive <change-name>    # Archive completed
```

## Troubleshooting

### "OpenSpec not found"
```bash
npm install
# or
npm install -g @fission-ai/openspec@latest
```

### "No changes found"
This is normal initially. A Session needs to create proposals first.

### "Change already exists"
```bash
openspec archive <change-name> --yes
# Then create new proposal
```

### Spec and Code Out of Sync
1. C Session identifies the discrepancy
2. A Session decides: update spec or fix code
3. Create new OpenSpec change proposal for correction
4. B Session implements the fix

## Further Reading

- [OpenSpec GitHub](https://github.com/Fission-AI/OpenSpec)
- [openspec/README.md](../../openspec/README.md)
- [openspec/AGENTS.md](../../openspec/AGENTS.md)
- [DESIGN_STATE.yaml](../.claude/DESIGN_STATE.yaml)
- [Templates INDEX](./INDEX.md)

## Examples

See `openspec/specs/` directory for archived examples of completed specifications.
