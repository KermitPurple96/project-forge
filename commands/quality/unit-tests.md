# 🤖 Command: unit-tests

> Generates unit tests for functions and modules.

## When to use

After implementing new features, or to increase coverage on existing code.

## Instructions for Claude

1. Read `testing-strategy.md` for tools and conventions
2. Identify untested or under-tested files:
   - Run coverage report if available
   - Check for files with no corresponding `.test` or `.spec` file
3. For each function/module, generate tests covering:
   - Happy path (expected input → expected output)
   - Edge cases (empty input, null, max values, boundary conditions)
   - Error cases (invalid input → appropriate error)
4. Follow project testing conventions (naming, file location, assertion style)
5. Run all tests and verify they pass
6. Report coverage change

## Arguments

- `--file [path]` — generate tests for a specific file
- `--coverage` — show coverage report after
- `--missing-only` — only generate for files without tests
