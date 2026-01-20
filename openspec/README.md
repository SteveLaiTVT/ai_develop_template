# OpenSpec Integration

This directory contains OpenSpec specifications for structured, spec-driven AI collaboration.

## What is OpenSpec?

OpenSpec enables structured collaboration between humans and AI by establishing agreed-upon specifications before implementation begins. It provides a lightweight, no-code framework that separates current specifications (`specs/`) from proposed updates (`changes/`).

## Directory Structure

```
openspec/
├── AGENTS.md              # Instructions for AI coding assistants
├── README.md              # This file
├── specs/                 # Current specifications (source of truth)
│   └── (archived specs from completed changes)
└── changes/               # Active proposed changes
    └── (work-in-progress change proposals)
```

## Quick Start

### 1. Install OpenSpec Globally

```bash
npm install -g @fission-ai/openspec@latest
```

**Requirements**: Node.js >= 20.19.0

### 2. View Active Changes

```bash
openspec list
```

### 3. Interactive Dashboard

```bash
openspec view
```

### 4. Show Change Details

```bash
openspec show <change-name>
```

### 5. Archive Completed Work

```bash
openspec archive <change-name> --yes
```

## How It Works with This Template

This template uses a three-session AI development model:
- **A Session (Reasoning)**: Architecture and design decisions
- **B Session (Execution)**: Implementation
- **C Session (Review)**: Review and validation

OpenSpec integrates seamlessly:

1. **A Session** creates specification proposals in `openspec/changes/`
2. **Review** happens between human and A Session to refine specs
3. **B Session** implements based on approved specs
4. **C Session** validates implementation matches specs
5. **Archive** completed specs to `openspec/specs/`

## AI Tool Slash Commands

If your AI tool supports OpenSpec natively:

- `/openspec:list` - Show all active changes
- `/openspec:show <change-name>` - Display change details
- `/openspec:apply <change-name>` - Apply a specific change
- `/openspec:archive <change-name>` - Archive completed change

## Integration with DESIGN_STATE.yaml

OpenSpec complements (doesn't replace) the DESIGN_STATE.yaml:

- **DESIGN_STATE.yaml**: Overall architecture, constraints, module status
- **OpenSpec specs**: Detailed API contracts, implementation specs, task breakdowns

Both should be kept in sync. OpenSpec specs should reference constraints from DESIGN_STATE.yaml.

## Creating Your First Spec

Ask your AI assistant:

```
"Create an OpenSpec change proposal for [feature description]"
```

The AI will create a structured specification in `openspec/changes/` including:
- Feature overview
- Technical requirements
- Implementation tasks
- Acceptance criteria

## Best Practices

1. **Spec before code**: Always create/approve specs before implementation
2. **Keep changes atomic**: One focused change per proposal
3. **Link to DESIGN_STATE**: Reference relevant constraints and architecture
4. **Review regularly**: Use the interactive dashboard to track progress
5. **Archive promptly**: Move completed work to `specs/` to keep `changes/` clean

## Learn More

- [OpenSpec GitHub](https://github.com/Fission-AI/OpenSpec)
- [AGENTS.md](./AGENTS.md) - Detailed AI agent instructions
- [Main README](../README.md) - Overall template documentation
- [DESIGN_STATE.yaml](../.claude/DESIGN_STATE.yaml) - Architecture source of truth
