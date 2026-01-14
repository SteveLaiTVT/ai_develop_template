# BUG_REPORT Template - Human Testing → A Session

Use this template when reporting bugs found during manual testing.

---

## Quick Template

```yaml
# BUG REPORT
bug_id: "BUG-XXX"
reported_at: "YYYY-MM-DD HH:MM"
reporter: "[Your name]"
severity: "high"  # critical | high | medium | low

# Description
title: "[One sentence describing the problem]"
observed: "[What actually happened]"
expected: "[What should have happened]"

# Steps to Reproduce
steps:
  1. "[Step 1]"
  2. "[Step 2]"
  3. "[Step 3]"

# Environment
environment:
  app: "user-web"  # admin-web | user-web | android | ios | backend
  browser: "Chrome 120"
  device: "MacBook Pro"
  os: "macOS 14.2"
  
# Attachments (Optional)
attachments:
  - type: "screenshot"
    url: "[screenshot link]"
  - type: "console_log"
    content: "[error log]"

# Notes
notes: "[Any additional information]"
```

---

## Detailed Template (Complex Issues)

```yaml
# BUG REPORT - Detailed
bug_id: "BUG-XXX"
reported_at: "YYYY-MM-DD HH:MM"
reporter: "[Your name]"

# ============================================================
# Basic Info
# ============================================================
title: "[One sentence describing the problem]"
severity: "high"  # See severity guide below
category: "functional"  # functional | ui | performance | security | data

# ============================================================
# Description
# ============================================================
description:
  observed: |
    [What actually happened]
    [Describe in detail]
    
  expected: |
    [What should have happened]
    [What is the correct behavior]
    
  impact: |
    [What is the impact of this bug]
    [What can't users do because of this]

# ============================================================
# Steps to Reproduce
# ============================================================
steps_to_reproduce:
  preconditions:
    - "[Precondition 1: e.g., must be logged in]"
    - "[Precondition 2: e.g., specific data required]"
    
  steps:
    1. "[Specific step 1]"
    2. "[Specific step 2]"
    3. "[Specific step 3]"
    4. "[Bug occurs]"
    
  reproducibility: "always"  # always | sometimes | rarely
  occurrence_rate: "~30%"  # If intermittent

# ============================================================
# Environment
# ============================================================
environment:
  app: "user-web"
  app_version: "0.1.0"
  
  # Client
  browser: "Chrome 120.0.6099.129"
  device: "MacBook Pro 14-inch"
  os: "macOS 14.2.1"
  screen_resolution: "1512x982"
  
  # Network
  network: "WiFi"  # WiFi | 4G | 5G
  
  # Account (if relevant)
  user_type: "regular"  # admin | regular | guest
  user_id: "[anonymized user ID]"

# ============================================================
# Evidence
# ============================================================
attachments:
  screenshots:
    - description: "Error page screenshot"
      url: "[link or filename]"
      
  videos:
    - description: "Screen recording of reproduction"
      url: "[video link]"
      
  logs:
    - type: "console"
      content: |
        Error: Cannot read property 'id' of undefined
        at UserProfile.render (UserProfile.tsx:45)
        
    - type: "network"
      content: |
        POST /api/v1/users/profile
        Status: 500
        Response: {"error": "Internal Server Error"}

# ============================================================
# Analysis (Optional)
# ============================================================
analysis:
  possible_cause: |
    [Your hypothesis about what's causing this]
    [e.g., API returns empty data causing frontend crash]
    
  related_feature: "[Related feature name]"
  related_task: "TASK-XXX"  # If known

# ============================================================
# Workaround (Optional)
# ============================================================
workaround:
  exists: true
  description: |
    [Temporary way to bypass the issue]
    [e.g., Refreshing the page and retrying works]

# ============================================================
# Notes
# ============================================================
notes: |
  [Any other useful information]
  [e.g., This bug started appearing today, wasn't there yesterday]
```

---

## Severity Guide

| Severity | Definition | Examples |
|----------|------------|----------|
| **critical** | System crash / Data loss / Security breach | Payment lost after order, password exposed |
| **high** | Core feature broken | Can't login, can't submit form |
| **medium** | Feature broken but has workaround | Button needs double-click to work |
| **low** | Minor issue / Cosmetic | Text alignment off, color mismatch |

## Category Guide

| Category | Description |
|----------|-------------|
| **functional** | Feature doesn't work as expected |
| **ui** | Visual display issue |
| **performance** | Slow loading, lag, freeze |
| **security** | Security-related issue |
| **data** | Data error or loss |
| **crash** | Application crash |
| **compatibility** | Works in one environment but not another |

---

## Good Bug Report Example ✅

```yaml
bug_id: "BUG-042"
reported_at: "2026-01-14 15:30"
reporter: "John"
severity: "high"
category: "functional"

title: "White screen after successful login"

description:
  observed: |
    After entering correct email and password and clicking login,
    the page goes completely white. Console shows:
    "Cannot read property 'name' of undefined"
    
  expected: |
    After successful login, should redirect to homepage
    and display user information

steps_to_reproduce:
  preconditions:
    - "Using a registered account"
  steps:
    1. Go to https://app.example.com/login
    2. Enter email: test@example.com
    3. Enter password: Test123456
    4. Click "Login" button
    5. Page goes white
  reproducibility: "always"

environment:
  app: "user-web"
  browser: "Chrome 120.0.6099.129"
  os: "macOS 14.2"

attachments:
  logs:
    - type: "console"
      content: |
        TypeError: Cannot read property 'name' of undefined
            at Header.tsx:23

analysis:
  possible_cause: |
    Login API response might be missing the 'name' field,
    but Header component directly accesses user.name

workaround:
  exists: false

notes: |
  Always happens with newly registered users.
  Old users (with complete profile) work fine.
```

## Bad Bug Report Example ❌

```
Title: Login broken
Description: Can't login
```

**Problems:**
- No specific description
- No reproduction steps
- No environment info
- Impossible to locate issue
