# 🤖 Command: standards-enforce

> Verifies code follows project-defined conventions.

## Instructions for Claude

1. Read `project-structure.md` for naming and organization rules
2. Read `testing-strategy.md` for test requirements
3. Read `error-handling-strategy.md` for error patterns
4. Check all files modified since last run:
   - File is in the correct directory per conventions
   - Naming matches the defined pattern
   - Exports follow the convention (default vs named)
   - Error handling follows the strategy
   - New functions have appropriate tests
   - Types are properly defined
5. Report violations with fix suggestions
6. Optionally auto-fix with `--fix`
