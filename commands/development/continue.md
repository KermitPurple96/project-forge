# 🤖 Command: continue

> The simplest command in the framework. Just keeps building.

## Invocation

```
/continue                  # Continue the next task in the current sprint
/continue sprint           # Continue until the sprint is complete (full-auto)
/continue milestone        # Continue until the milestone is complete (full-auto)
/continue 3                # Do the next 3 tasks then stop
```

## Instructions for Claude

1. Run `context-sync` — understand where we are
2. Read `task-breakdown.md` — find the next ⬜ task
3. Delegate to `orchestrator` with the appropriate mode:
   - No argument → execute 1 task (semi-auto)
   - `sprint` → execute remaining tasks in current sprint (full-auto)
   - `milestone` → execute remaining sprints in current milestone (full-auto)
   - `N` → execute next N tasks (full-auto)
4. Report what was done

## Output

```
✅ MVP-007 | feat(dashboard): add analytics widget | 3 files | tests: 6/6
Next: MVP-008 — "Add export to CSV" (backend-agent)
Continue? [Y/n]
```

## Rules

1. If no tasks remain → tell the user the milestone/project is complete
2. If next task is 🚫 blocked → skip to next unblocked, report the skip
3. If this is the first run of the session → run `context-sync` first
4. Never ask "what do you want to work on?" — the roadmap decides
