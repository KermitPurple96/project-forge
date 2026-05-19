# 🤖 Command: test-coverage-report

> Analyzes test coverage and identifies gaps.

## When to use

Before releases. When deciding what to test next.

## Instructions for Claude

1. Run test suite with coverage: `npx vitest --coverage` / `pytest --cov` / equivalent
2. Parse coverage report
3. Identify:
   - Files with 0% coverage (completely untested)
   - Files below target threshold (from testing-strategy.md)
   - Critical paths without tests (auth, payments, core business logic)
   - Tests that exist but don't assert anything meaningful
4. Prioritize what to test next based on:
   - Risk (auth > UI components)
   - Complexity (complex logic > simple getters)
   - Change frequency (hot files > stable files)

## Output

Coverage summary + prioritized list of "test these next" recommendations.
