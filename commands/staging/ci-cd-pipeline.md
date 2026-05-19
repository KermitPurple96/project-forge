# 🔀 Command: ci-cd-pipeline

> Generates CI/CD pipeline configuration.

## Instructions for Claude

1. Read `stack-selection.md` for CI tool and hosting
2. Generate pipeline that runs on every push/PR:
   - Install dependencies
   - Lint (ESLint, Ruff, etc.)
   - Type check (TypeScript, mypy, etc.)
   - Unit tests
   - Integration tests
   - Build
   - Security audit (npm audit / pip audit)
3. Deploy pipeline (on merge to main):
   - Build production bundle
   - Run smoke tests
   - Deploy to staging (automatic)
   - Deploy to production (manual approval or automatic)
4. Add status badge to README
5. Configure caching for faster runs (node_modules, pip cache)

## Supported platforms

Adapt the generated config to: GitHub Actions, GitLab CI, CircleCI, or whatever is in `stack-selection.md`.
