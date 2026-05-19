# 🤖 Command: develop

> Implements the next task from the roadmap/task-breakdown.

## When to use

During active development. Run repeatedly to progress through the task list.

## Instructions for Claude

1. Run `context-init` if not already done this session
2. Read `task-breakdown.md` — find the next task with status ⬜
3. Read relevant templates for context (data-model, api-contract, user-flows)
4. Implement the task:
   - Write the code following project conventions
   - Write tests for the new functionality
   - Run existing tests to verify nothing breaks
5. Update task status to ✅ in task-breakdown.md
6. Commit with conventional commit message: `feat(scope): description`
7. Report what was done and what's next

## Arguments

- `--task [ID]` — implement a specific task instead of the next one
- `--continue` — continue from where the last session left off
- `--dry-run` — plan what would be done without writing code

## Expected output

Working code, passing tests, updated task list.
