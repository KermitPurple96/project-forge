# 🤖 Command: consistency-check

> Verifies that naming, patterns, and structure are consistent across the codebase.

## When to use

After multiple development sessions. Before code review. When multiple people contribute.

## Instructions for Claude

1. Read `project-structure.md` for defined conventions
2. Check for inconsistencies:
   - File naming (mix of kebab-case and camelCase?)
   - Component patterns (some use hooks, others don't?)
   - API response format (some return `{data}`, others return raw?)
   - Error handling (some try/catch, others unhandled?)
   - Import style (relative vs absolute, order)
   - Code formatting (should be caught by linter, but verify)
3. List all inconsistencies with locations
4. Suggest which pattern to standardize on (based on majority usage)
5. Optionally auto-fix with `--fix`

## Arguments

- `--fix` — automatically fix inconsistencies (uses majority pattern)
- `--report-only` — list without fixing
