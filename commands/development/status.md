# 🤖 Command: status

> Quick snapshot of where the project stands.

## Invocation

```
/status                    # Full status
/status sprint             # Current sprint only
/status --blockers         # Show only blocked tasks
```

## Instructions for Claude

1. Read `task-breakdown.md`
2. Read recent git log (last 10 commits)
3. Count tasks by status
4. Calculate progress

## Output

```
## Project: [name]

📍 Phase: MVP → Sprint 2 → Task MVP-008
🔄 Mode: semi-auto

### Progress
██████████░░░░░░░░░░ 50%

Sprint 1: ✅ complete (5/5 tasks)
Sprint 2: 🔄 in progress (3/6 tasks done)
  ✅ MVP-006 — User registration API
  ✅ MVP-007 — Login page UI
  ✅ MVP-008 — Dashboard layout
  ⬜ MVP-009 — Project CRUD API
  ⬜ MVP-010 — Project list UI
  ⬜ MVP-011 — Project detail page

### Stats
Tasks done:     8/18
Tests:          47 pass, 0 fail
Last commit:    12 min ago — "feat(dashboard): add layout grid"
Blockers:       0

### Next
MVP-009 — "Project CRUD API" (backend-agent, ~3h)
Run /continue to proceed.
```
