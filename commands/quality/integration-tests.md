# 🤖 Command: integration-tests

> Tests how components work together: API endpoints, DB queries, service interactions.

## When to use

After implementing API endpoints or cross-service features.

## Instructions for Claude

1. Identify integration points: API routes, DB operations, external service calls
2. For each endpoint, test:
   - Correct response for valid requests
   - Proper auth enforcement (401/403)
   - Validation errors (400)
   - Not found cases (404)
   - Correct data persistence (read back after write)
3. Use test database (not production!)
4. Clean up test data after each test (transactions or teardown)
5. Run and verify all pass

## Arguments

- `--endpoint [path]` — test a specific endpoint
- `--auth` — focus on auth-related integration tests
