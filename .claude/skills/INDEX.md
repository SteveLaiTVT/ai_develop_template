# Agent Skills Index

本目录包含所有 Agent 技能规范，帮助用户更好地使用 AI 开发流程。

This directory contains all Agent skill specifications to help users better utilize the AI development workflow.

---

## Skills Overview

| Skill | File | Priority | Description |
|-------|------|----------|-------------|
| **Git Version Control** | `git_skills.md` | **MANDATORY** | Git 版本控制规范 (强制) |
| **Frontend Development** | `frontend_skills.md` | Conditional | 前端开发规范 |
| **Backend Development** | `backend_skills.md` | Conditional | 后端开发规范 |
| **Agent-Browser** | `agent_browser_skills.md` | Conditional | 浏览器自动化技能 |
| **Testing** | `testing_skills.md` | Conditional | 测试规范与最佳实践 |

---

## Skill Priority Levels

### MANDATORY (强制性)

These skills **MUST** be applied to all projects:

- **Git Version Control** - All code changes must follow Git best practices

### Conditional (条件性)

These skills are applied based on project type:

| Project Type | Applicable Skills |
|--------------|-------------------|
| **Web Frontend** | Frontend, Agent-Browser, Testing |
| **Web Backend** | Backend, Testing |
| **Full-Stack Web** | Frontend, Backend, Agent-Browser, Testing |
| **API Service** | Backend, Testing |
| **Mobile App** | Testing |

---

## Skills Application in Workflow

### During Initialization (A Session)

```yaml
# DESIGN_STATE.yaml - skills section
skills:
  mandatory:
    - git_skills           # Always applied
  
  applied:
    - frontend_skills      # Applied for web frontend projects
    - backend_skills       # Applied for backend projects
    - agent_browser_skills # Applied for web projects
    - testing_skills       # Applied for all projects with tests
  
  skipped:
    - mobile_skills        # Not applicable to this project
```

### During Implementation (B Session)

B Session should reference applicable skills when:

1. Making Git commits → Follow `git_skills.md`
2. Writing frontend code → Follow `frontend_skills.md`
3. Writing backend code → Follow `backend_skills.md`
4. Performing browser testing → Follow `agent_browser_skills.md`
5. Writing tests → Follow `testing_skills.md`

### During Review (C Session)

C Session validates code against applicable skills:

- [ ] Git commit messages follow convention
- [ ] Frontend code follows component guidelines
- [ ] Backend code follows API standards
- [ ] Tests follow testing best practices

---

## Quick Links

### Git Version Control (Mandatory)
- [Commit Message Convention](git_skills.md#commit-message-convention)
- [Branch Strategy](git_skills.md#branch-strategy)
- [Workflow Procedures](git_skills.md#workflow-procedures)

### Frontend Development
- [Component Guidelines](frontend_skills.md#component-guidelines)
- [State Management](frontend_skills.md#state-management)
- [Performance Optimization](frontend_skills.md#performance-optimization)

### Backend Development
- [API Design Standards](backend_skills.md#api-design-standards)
- [Authentication & Authorization](backend_skills.md#authentication--authorization)
- [Database Patterns](backend_skills.md#database-patterns)

### Agent-Browser
- [Navigation & Interaction](agent_browser_skills.md#core-capabilities)
- [Common Testing Patterns](agent_browser_skills.md#common-testing-patterns)
- [Screenshot Capabilities](agent_browser_skills.md#screenshot-capabilities)

### Testing
- [Test Types](testing_skills.md#test-types)
- [Test Structure (AAA Pattern)](testing_skills.md#test-structure)
- [What to Test](testing_skills.md#what-to-test)

---

## Adding New Skills

To add a new skill:

1. Create `{skill_name}_skills.md` in this directory
2. Follow the standard structure:
   - Core Principles
   - Detailed Guidelines
   - Examples
   - Quick Reference Checklist
3. Update this INDEX.md file
4. Update `DESIGN_STATE.yaml` to reference the new skill

---

## External Skills Resources

For additional best practices, refer to:

| Resource | URL | Domain |
|----------|-----|--------|
| Vercel React Best Practices | https://skills.sh/vercel-labs/agent-skills/vercel-react-best-practices | React |
| Web Design Guidelines | https://skills.sh/vercel-labs/agent-skills/web-design-guidelines | UI/UX |
| Anthropic Frontend Design | https://skills.sh/anthropics/skills/frontend-design | Frontend |
| Agent Browser | https://skills.sh/vercel-labs/agent-browser/agent-browser | Browser Automation |
| Remotion Best Practices | https://skills.sh/remotion-dev/skills/remotion-best-practices | Video |
| Alibaba Java 开发手册 | https://github.com/alibaba/p3c | Java |
