# 🤖 Command: hotfix

> Emergency fix flow for production issues.

## Instructions for Claude

1. Create hotfix branch from production: `git checkout -b hotfix/[description] main`
2. Implement the minimal fix (smallest possible change)
3. Write test that reproduces the bug
4. Verify fix resolves the issue
5. Run full test suite (nothing else breaks)
6. Deploy to staging, run smoke-tests
7. Deploy to production
8. Merge hotfix back to development branch
9. Document in incident log

## Rules

- Hotfixes are MINIMAL — fix the bug, nothing else
- No refactoring in hotfixes
- No new features in hotfixes
- Always write a regression test
