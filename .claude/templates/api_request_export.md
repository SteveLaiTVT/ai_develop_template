# API Request Export Template

This template guides B Session on exporting API requests for user testing before C Session review.

## Purpose

Before C Session starts code review, B Session MUST export all tested API requests to enable:
- Quick user verification of backend functionality
- Rapid feedback loop for API behavior
- Documentation of expected request/response formats

## Directory Structure

```
tests/
├── mock-data/                    # Mock data for testing
│   ├── {module}/
│   │   ├── {scenario}.json
│   │   └── ...
│   └── README.md
│
├── http-requests/                # Shell scripts (REQUIRED)
│   ├── env.sh                    # Environment variables
│   ├── README.md                 # Usage instructions
│   ├── run-all.sh                # Run all tests
│   ├── {module}/
│   │   ├── 01-{action}.sh
│   │   ├── 02-{action}.sh
│   │   └── ...
│   └── {module}.http             # (Optional) VS Code REST Client
│
├── postman/                      # (Optional) Postman exports
│   ├── collection.json
│   └── environment.json
│
└── apifox/                       # (Optional) Apifox exports
    └── collection.json
```

## File Templates

### 1. Environment Variables (env.sh)

```bash
#!/bin/bash
# =============================================================
# API Request Environment Variables
# =============================================================
# Usage: source env.sh
# =============================================================

# Server Configuration
export BASE_URL="http://localhost:3000"
export API_VERSION="v1"
export API_URL="${BASE_URL}/api/${API_VERSION}"

# Authentication Tokens (updated by login scripts)
export ACCESS_TOKEN=""
export REFRESH_TOKEN=""

# Test User Credentials
export TEST_EMAIL="test@example.com"
export TEST_PASSWORD="SecurePassword123!"
export TEST_USER_NAME="Test User"

# Additional Test Data
export TEST_USER_ID=""

# =============================================================
# Helper Functions
# =============================================================

# Pretty print JSON response
json_print() {
    echo "$1" | jq '.' 2>/dev/null || echo "$1"
}

# Check if jq is available
check_dependencies() {
    if ! command -v jq &> /dev/null; then
        echo "Warning: jq not installed. JSON output will not be formatted."
        echo "Install with: brew install jq (macOS) or apt-get install jq (Linux)"
    fi
}

check_dependencies
```

### 2. Request Script Template

```bash
#!/bin/bash
# =============================================================
# {MODULE} - {ACTION}
# =============================================================
# Endpoint: {METHOD} {PATH}
# Description: {DESCRIPTION}
# =============================================================

set -e
source "$(dirname "$0")/../env.sh"

# =============================================================
# Input Parameters
# =============================================================
PARAM1="${1:-$DEFAULT_VALUE}"

# =============================================================
# Request
# =============================================================
echo "=== {MODULE}: {ACTION} ==="
echo "Endpoint: {METHOD} ${API_URL}/{path}"
echo "Time: $(date)"
echo ""

RESPONSE=$(curl -s -w "\n%{http_code}" -X {METHOD} "${API_URL}/{path}" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer ${ACCESS_TOKEN}" \
  -d '{
    "field1": "value1",
    "field2": "value2"
  }')

# =============================================================
# Response Processing
# =============================================================
HTTP_CODE=$(echo "$RESPONSE" | tail -n1)
BODY=$(echo "$RESPONSE" | sed '$d')

echo "Status Code: ${HTTP_CODE}"
echo "Response:"
json_print "$BODY"
echo ""

# =============================================================
# Validation
# =============================================================
EXPECTED_CODE={EXPECTED_CODE}

if [ "$HTTP_CODE" -eq "$EXPECTED_CODE" ]; then
  echo "✅ SUCCESS: {SUCCESS_MESSAGE}"

  # Extract data if needed
  # SOME_VALUE=$(echo "$BODY" | jq -r '.data.someField // empty')

else
  echo "❌ FAILED: Expected ${EXPECTED_CODE}, got ${HTTP_CODE}"
  exit 1
fi
```

### 3. README Template

```markdown
# API Request Tests

Quick test scripts for verifying API endpoints.

## Prerequisites

- bash (built into macOS/Linux)
- curl (usually pre-installed)
- jq (optional, for JSON formatting)

## Quick Start

1. **Start the backend server:**
   ```bash
   cd apps/backend && npm run start:dev
   ```

2. **Run all tests:**
   ```bash
   cd tests/http-requests
   chmod +x *.sh **/*.sh
   ./run-all.sh
   ```

## Individual Tests

### Authentication

```bash
# Register a new user
./auth/01-register.sh

# Login (saves token automatically)
./auth/02-login.sh

# Refresh token
./auth/03-refresh-token.sh

# Logout
./auth/04-logout.sh
```

### User Management

```bash
# Get current user profile
./user/01-get-profile.sh

# Update profile
./user/02-update-profile.sh
```

## Environment Configuration

Edit `env.sh` to configure:
- `BASE_URL` - Backend server URL
- `TEST_EMAIL` / `TEST_PASSWORD` - Test credentials

## Test Data

| Field    | Value                  | Notes                |
|----------|------------------------|----------------------|
| Email    | test@example.com       | Default test user    |
| Password | SecurePassword123!     | Meets complexity req |

## Expected Results

| Endpoint               | Method | Expected Status | Notes              |
|------------------------|--------|-----------------|---------------------|
| /auth/register         | POST   | 201             | Creates new user    |
| /auth/login            | POST   | 200             | Returns JWT tokens  |
| /auth/refresh          | POST   | 200             | Refreshes tokens    |
| /auth/logout           | POST   | 200             | Invalidates token   |
| /users/profile         | GET    | 200             | Requires auth       |
| /users/profile         | PATCH  | 200             | Requires auth       |

## Troubleshooting

| Error                  | Solution                                    |
|------------------------|---------------------------------------------|
| Connection refused     | Ensure backend is running on correct port   |
| 401 Unauthorized       | Run login script first to get fresh token   |
| 400 Bad Request        | Check request body format in script         |
| 500 Server Error       | Check backend logs for details              |

## Mock Data

All test data is defined in `tests/mock-data/`. Each scenario includes:
- Request body
- Expected response
- Status codes

Reference mock data when creating or modifying scripts.
```

### 4. Run All Script (run-all.sh)

```bash
#!/bin/bash
# =============================================================
# Run All API Tests
# =============================================================
# Executes all API request scripts in order
# =============================================================

set -e
cd "$(dirname "$0")"

echo "=============================================="
echo "  API REQUEST TESTS"
echo "=============================================="
echo ""

# Source environment
source env.sh

# Track results
PASSED=0
FAILED=0
TOTAL=0

run_test() {
    local script="$1"
    local name="$2"

    TOTAL=$((TOTAL + 1))
    echo "[$TOTAL] Running: $name"

    if bash "$script" > /dev/null 2>&1; then
        PASSED=$((PASSED + 1))
        echo "    ✅ Passed"
    else
        FAILED=$((FAILED + 1))
        echo "    ❌ Failed"
    fi
}

# =============================================================
# Auth Tests
# =============================================================
echo ""
echo "--- Authentication Tests ---"
run_test "auth/01-register.sh" "Register User"
run_test "auth/02-login.sh" "Login User"
run_test "auth/03-refresh-token.sh" "Refresh Token"

# =============================================================
# User Tests
# =============================================================
echo ""
echo "--- User Tests ---"
run_test "user/01-get-profile.sh" "Get Profile"
run_test "user/02-update-profile.sh" "Update Profile"

# =============================================================
# Summary
# =============================================================
echo ""
echo "=============================================="
echo "  SUMMARY"
echo "=============================================="
echo "  Total:  $TOTAL"
echo "  Passed: $PASSED"
echo "  Failed: $FAILED"
echo "=============================================="

if [ $FAILED -gt 0 ]; then
    echo ""
    echo "Some tests failed. Run individual scripts for details."
    exit 1
else
    echo ""
    echo "All tests passed!"
    exit 0
fi
```

## Postman Collection Structure

```json
{
  "info": {
    "name": "{{project_name}} API",
    "_postman_id": "{{uuid}}",
    "description": "Generated by B Session",
    "schema": "https://schema.getpostman.com/json/collection/v2.1.0/collection.json"
  },
  "auth": {
    "type": "bearer",
    "bearer": [
      {
        "key": "token",
        "value": "{{access_token}}",
        "type": "string"
      }
    ]
  },
  "variable": [
    {
      "key": "base_url",
      "value": "http://localhost:3000/api/v1",
      "type": "string"
    },
    {
      "key": "access_token",
      "value": "",
      "type": "string"
    },
    {
      "key": "refresh_token",
      "value": "",
      "type": "string"
    }
  ],
  "item": [
    {
      "name": "Auth",
      "description": "Authentication endpoints",
      "item": []
    },
    {
      "name": "Users",
      "description": "User management endpoints",
      "item": []
    }
  ]
}
```

## Apifox Collection Structure

```json
{
  "apifoxProject": "2.0",
  "info": {
    "name": "{{project_name}} API",
    "description": "Generated by B Session"
  },
  "apiCollection": [
    {
      "name": "Auth",
      "items": []
    },
    {
      "name": "Users",
      "items": []
    }
  ],
  "environment": {
    "name": "Local",
    "values": [
      {
        "key": "base_url",
        "value": "http://localhost:3000/api/v1"
      },
      {
        "key": "access_token",
        "value": ""
      }
    ]
  }
}
```

## VS Code REST Client (.http file)

```http
# =============================================================
# {{module}} API Requests
# =============================================================
# Usage: Install "REST Client" extension in VS Code
# Click "Send Request" above each request
# =============================================================

@baseUrl = http://localhost:3000/api/v1
@contentType = application/json

# =============================================================
# Auth Endpoints
# =============================================================

### Register User
# @name register
POST {{baseUrl}}/auth/register
Content-Type: {{contentType}}

{
  "email": "test@example.com",
  "password": "SecurePassword123!"
}

### Login User
# @name login
POST {{baseUrl}}/auth/login
Content-Type: {{contentType}}

{
  "email": "test@example.com",
  "password": "SecurePassword123!"
}

### Token from Login Response
@accessToken = {{login.response.body.data.accessToken}}

### Get Profile (Authenticated)
GET {{baseUrl}}/users/profile
Authorization: Bearer {{accessToken}}
```

## Export Checklist

Before handoff to C Session, verify:

- [ ] `tests/http-requests/env.sh` created with all variables
- [ ] `tests/http-requests/README.md` with clear instructions
- [ ] `tests/http-requests/run-all.sh` runs without errors
- [ ] All endpoint scripts created and tested
- [ ] Scripts handle both success and error cases
- [ ] Token management works (login saves, other scripts use)
- [ ] (Optional) Postman collection exported
- [ ] (Optional) Apifox collection exported
- [ ] (Optional) .http files for VS Code created

## Integration with Mock Data

API request scripts should use data from `tests/mock-data/`:

```bash
#!/bin/bash
# Example: Using mock data in scripts

source "$(dirname "$0")/../env.sh"

# Load mock data
MOCK_FILE="../mock-data/auth/register-success.json"
if [ -f "$MOCK_FILE" ]; then
    EMAIL=$(jq -r '.request.body.email' "$MOCK_FILE")
    PASSWORD=$(jq -r '.request.body.password' "$MOCK_FILE")
else
    EMAIL="$TEST_EMAIL"
    PASSWORD="$TEST_PASSWORD"
fi

# Use in request...
```

This ensures consistency between mock data and API request scripts.
