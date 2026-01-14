# ITERATION_SUMMARY Template - Version Release

Use this template when completing an iteration to summarize what was done.
This is the **Chinese summary** delivered to stakeholders.

---

```yaml
# ITERATION SUMMARY / 迭代总结
iteration_id: "iter-XXX"
version: "X.X.X"
completed_at: "YYYY-MM-DD"
duration: "X days"

# ============================================================
# 版本概述 (Version Overview)
# ============================================================
overview:
  goal: |
    [What this iteration aimed to achieve]
    [这个迭代要达成的目标]
    
  result: "completed"  # completed | partial | failed
  
  summary_zh: |
    [中文总结 - 给项目干系人看的]
    本次迭代完成了XXX功能，包括：
    1. 功能A
    2. 功能B
    3. 功能C

# ============================================================
# 完成的功能 (Completed Features)
# ============================================================
features_completed:
  - id: "FEAT-XXX"
    name_en: "[Feature name in English]"
    name_zh: "[功能名称]"
    description_zh: "[功能描述]"
    tasks:
      - "TASK-XXX"
      - "TASK-YYY"
    demo_url: "[Demo link if available]"
    
  - id: "FEAT-YYY"
    name_en: "[Feature name]"
    name_zh: "[功能名称]"
    description_zh: "[功能描述]"
    tasks:
      - "TASK-ZZZ"

# ============================================================
# 修复的 Bug (Bugs Fixed)
# ============================================================
bugs_fixed:
  - bug_id: "BUG-XXX"
    title_zh: "[Bug 标题]"
    severity: "high"
    fix_task: "TASK-XXX"

# ============================================================
# 未完成项 (Incomplete Items)
# ============================================================
incomplete:
  - id: "TASK-XXX"
    title: "[Task title]"
    reason: "[Why not completed]"
    carry_over_to: "iter-YYY"

# ============================================================
# 技术债务 (Technical Debt)
# ============================================================
technical_debt:
  added:
    - description_zh: "[新增的技术债务]"
      severity: "low"
      plan: "[何时处理]"
      
  resolved:
    - description_zh: "[解决的技术债务]"

# ============================================================
# 设计变更 (Design Changes)
# ============================================================
design_changes:
  - change_id: "CL-XXX"
    description_zh: "[变更描述]"
    rationale_zh: "[变更原因]"

# ============================================================
# 指标 (Metrics)
# ============================================================
metrics:
  tasks:
    planned: 0
    completed: 0
    completion_rate: "0%"
    
  code:
    files_created: 0
    files_modified: 0
    total_lines_added: 0
    
  quality:
    review_rounds: 0
    issues_found: 0
    issues_fixed: 0
    
  bugs:
    found_during_testing: 0
    fixed: 0
    remaining: 0

# ============================================================
# 下一迭代计划 (Next Iteration Plan)
# ============================================================
next_iteration:
  id: "iter-YYY"
  planned_start: "YYYY-MM-DD"
  goals_zh:
    - "[下一迭代目标1]"
    - "[下一迭代目标2]"
  carry_over_tasks:
    - "TASK-XXX"

# ============================================================
# 中文 TODO 清单 (Chinese TODO List)
# ============================================================
todo_list_zh:
  已完成:
    - "✅ [完成的事项1]"
    - "✅ [完成的事项2]"
    
  待处理:
    - "⏳ [待处理事项1]"
    - "⏳ [待处理事项2]"
    
  已知问题:
    - "⚠️ [已知问题1]"
    - "⚠️ [已知问题2]"
```

---

## Example: Auth Module Iteration Complete

```yaml
iteration_id: "iter-001"
version: "0.1.0"
completed_at: "2026-01-20"
duration: "5 days"

overview:
  goal: |
    Complete user authentication MVP with email/password login
    完成用户认证 MVP，支持邮箱密码登录
    
  result: "completed"
  
  summary_zh: |
    本次迭代完成了用户认证功能的 MVP 版本，包括：
    1. 用户注册（邮箱+密码）
    2. 用户登录
    3. JWT Token 管理
    4. 基础权限验证
    
    技术要点：
    - 使用 bcrypt 加密密码（cost factor 12）
    - JWT 采用 RS256 签名算法
    - Token 有效期 15 分钟，支持 Refresh Token

features_completed:
  - id: "FEAT-001"
    name_en: "User Registration"
    name_zh: "用户注册"
    description_zh: "支持邮箱密码注册，包含邮箱格式验证和密码强度检查"
    tasks:
      - "TASK-001"
      - "TASK-002"
    
  - id: "FEAT-002"
    name_en: "User Login"
    name_zh: "用户登录"
    description_zh: "支持邮箱密码登录，返回 JWT Token"
    tasks:
      - "TASK-003"

bugs_fixed:
  - bug_id: "BUG-001"
    title_zh: "登录成功后白屏"
    severity: "high"
    fix_task: "TASK-004"

incomplete: []

technical_debt:
  added:
    - description_zh: "错误消息硬编码，未支持国际化"
      severity: "low"
      plan: "iter-003 处理"
      
  resolved: []

metrics:
  tasks:
    planned: 4
    completed: 4
    completion_rate: "100%"
  code:
    files_created: 12
    files_modified: 3
    total_lines_added: 450
  quality:
    review_rounds: 2
    issues_found: 5
    issues_fixed: 5
  bugs:
    found_during_testing: 1
    fixed: 1
    remaining: 0

next_iteration:
  id: "iter-002"
  planned_start: "2026-01-21"
  goals_zh:
    - "完成用户资料管理"
    - "完成 Admin 用户列表"

todo_list_zh:
  已完成:
    - "✅ POST /api/v1/auth/register"
    - "✅ POST /api/v1/auth/login"
    - "✅ POST /api/v1/auth/refresh"
    - "✅ JWT Token 验证中间件"
    
  待处理:
    - "⏳ 用户资料 CRUD"
    - "⏳ Admin 用户管理界面"
    
  已知问题:
    - "⚠️ 错误消息未国际化"
    - "⚠️ 登录失败无次数限制"
```
