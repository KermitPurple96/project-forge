# 🤖 Command: smoke-tests

> Quick validation that a deployment is healthy.

## Instructions for Claude

1. Generate smoke test script that checks:
   - Health endpoint returns 200
   - Homepage loads
   - Auth endpoint responds (login/register)
   - Database connection works (via health check)
   - Static assets load (CSS, JS, images)
   - Core API endpoint returns expected format
   - WebSocket connection (if applicable)
2. Each test has a 10s timeout
3. Exit with failure code if any test fails
4. Output clear pass/fail for each check

## Usage

```bash
./scripts/smoke-test.sh https://staging.yourapp.com
```
