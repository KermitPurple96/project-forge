# 🤖 Command: docs-gen

> Generates or updates project documentation.

## When to use

Before a release, after significant changes, or when docs are stale.

## Instructions for Claude

1. Scan the codebase for:
   - Public functions/methods without docstrings
   - API endpoints without documentation
   - README sections that are outdated
   - Missing setup/install instructions
2. Generate:
   - README.md with project overview, setup, usage, and contribution guide
   - API documentation (from actual code, not just api-contract.md)
   - JSDoc/docstrings for public functions
   - Environment variables documentation
3. Verify all code examples in docs actually work

## Arguments

- `--api` — only generate API docs
- `--readme` — only update README
- `--docstrings` — only add code-level documentation
