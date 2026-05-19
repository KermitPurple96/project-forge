# 🤖 Command: debug

> Diagnoses and fixes bugs systematically.

## Arguments

- `--error "[error message]"` — debug a specific error
- `--file [path]` — debug issues in a specific file
- `--flow [description]` — debug a user flow that's broken

## Instructions for Claude

1. **Reproduce:** Understand what should happen vs what actually happens
2. **Isolate:** Narrow down where the problem is
   - Read error message and stack trace
   - Identify the file and line
   - Check recent changes to that area: `git log --oneline -10 -- [file]`
3. **Diagnose:** Determine root cause
   - Read the relevant code
   - Check for common issues:
     - Null/undefined values
     - Type mismatches
     - Async/await errors
     - Wrong imports/paths
     - Environment variables missing
     - Database query errors
4. **Fix:** Implement the minimal fix
5. **Verify:** Run the failing scenario again + existing tests
6. **Prevent:** Consider if a test should be added to prevent regression
7. **Commit:** `fix(scope): description of what was broken`
