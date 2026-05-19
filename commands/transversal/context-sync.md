# 🤖 Command: context-sync

> Re-synchronizes Claude's understanding when context is lost or stale.

## When to use

- Starting a new chat session mid-project
- After significant changes by another person/tool
- When Claude's responses seem confused about the project state

## Instructions for Claude

1. Read the project root: `ls -la` + `find . -name "*.md" -path "*/.forge/*"`
2. Read key state files:
   - `task-breakdown.md` — current progress (which tasks are ✅ vs ⬜)
   - `roadmap.md` — which phase we're in
   - `CHANGELOG.md` — what's been done recently
3. Check recent git history: `git log --oneline -20`
4. Check current branch and status: `git status`
5. Read any recently modified source files: `git diff --name-only HEAD~5`
6. Produce a status summary and confirm with user:
   - "We're in Phase [X], working on [task]. Last thing done was [Y]. Next up: [Z]. Correct?"
