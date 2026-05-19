# 🤖 Command: explain

> Explains how a part of the codebase works.

## Arguments

- `--file [path]` — explain a specific file
- `--function [name]` — explain a specific function
- `--flow [description]` — explain a data/user flow
- `--architecture` — explain the overall architecture

## Instructions for Claude

1. Read the relevant code
2. Explain in clear language:
   - **What** it does (purpose)
   - **How** it works (step by step)
   - **Why** it's done this way (if there's a decision-log entry, reference it)
   - **Where** it fits in the bigger picture (what calls it, what it calls)
3. Identify any:
   - Non-obvious behavior
   - Potential gotchas
   - Dependencies on other parts of the code
4. If the code is complex, suggest simplification
