# 🤖 Command: refactor

> Improves code quality without changing behavior.

## When to use

When code works but is messy, duplicated, or hard to understand. After a burst of feature development.

## Instructions for Claude

1. Analyze the codebase for:
   - Duplicated code (DRY violations)
   - Functions longer than 50 lines
   - Files longer than 300 lines
   - Deeply nested conditionals (>3 levels)
   - Poor naming (single-letter variables, unclear function names)
   - Missing type annotations
   - Dead code (unreachable, unused exports)
2. Prioritize by impact: what changes improve readability the most?
3. Refactor one thing at a time, running tests after each change
4. Never change behavior — tests should pass without modification

## Arguments

- `--file [path]` — refactor a specific file
- `--scope [feature]` — refactor a specific feature/module
- `--report-only` — list issues without fixing them
