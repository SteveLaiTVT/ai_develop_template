# OpenSpec AI Agent Instructions

This project uses OpenSpec for spec-driven development alongside the existing three-session AI development workflow.

## Overview

OpenSpec provides structured collaboration between humans and AI by establishing agreed-upon specifications before implementation begins. It complements the existing A/B/C session model by adding explicit specification management.

## Directory Structure

- `openspec/specs/` - Current specifications (source of truth)
- `openspec/changes/` - Proposed specification updates

## Integration with Three-Session Model

### A Session (Architect)
- Can create OpenSpec change proposals
- Reviews and approves spec changes
- Updates DESIGN_STATE.yaml to reference OpenSpec specs

### B Session (Implementer)
- Implements code based on approved OpenSpec specs
- Uses `/openspec:apply <change-name>` to apply changes
- Reports back when specs are outdated or unclear

### C Session (Reviewer)
- Validates implementation against OpenSpec specs
- Checks if specs match actual implementation
- Suggests spec updates if inconsistencies found

## Available Commands

For AI tools with native OpenSpec support:

- `/openspec:list` - Show all active changes
- `/openspec:show <change-name>` - Display change details
- `/openspec:apply <change-name>` - Apply a change
- `/openspec:archive <change-name>` - Archive completed change

For command-line usage:
```bash
openspec list                # List changes
openspec view                # Interactive dashboard
openspec show <name>         # Show specific change
openspec archive <name>      # Archive completed change
```

**Note**: Requires global installation: `npm install -g @fission-ai/openspec@latest`

## Workflow Example

1. **A Session**: Create specification proposal
   ```
   "Create an OpenSpec change proposal for adding user authentication API"
   ```

2. **Review & Refine**: Discuss and refine the spec

3. **B Session**: Implement when approved
   ```
   /openspec:apply add-auth-api
   ```

4. **C Session**: Review implementation against spec

5. **Archive**: When complete
   ```
   /openspec:archive add-auth-api
   ```

## Best Practices

1. **Spec Before Code**: Always create/update specs before implementation
2. **One Change at a Time**: Keep changes focused and atomic
3. **Link to DESIGN_STATE**: Reference DESIGN_STATE.yaml constraints in specs
4. **Update Both**: Keep both OpenSpec specs and DESIGN_STATE.yaml in sync
5. **Archive Regularly**: Move completed changes to specs/ to keep changes/ clean

## Integration Points

OpenSpec specs should reference:
- Module structure from DESIGN_STATE.yaml
- API constraints from DESIGN_STATE.yaml
- Code constraints from DESIGN_STATE.yaml

DESIGN_STATE.yaml should reference:
- Active OpenSpec changes in current iteration
- Completed specs in module documentation
