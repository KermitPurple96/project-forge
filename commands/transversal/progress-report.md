# 🤖 Command: progress-report

> Generates a snapshot of project status.

## Instructions for Claude

1. Read `task-breakdown.md` and count:
   - Tasks done (✅)
   - Tasks in progress (🔄)
   - Tasks remaining (⬜)
   - Tasks blocked (🚫)
2. Read `roadmap.md` — which milestone are we in?
3. Read recent git log for activity
4. Calculate:
   - Completion percentage
   - Estimated time remaining (based on average task time)
   - Velocity trend (accelerating or slowing down?)
5. Generate report:

```
## Progress Report — [date]

**Phase:** [current phase]
**Milestone:** [current milestone]
**Progress:** ██████░░░░ 60% (18/30 tasks)

### Completed this session
- [task 1]
- [task 2]

### Up next
- [task 3]
- [task 4]

### Blockers
- [if any]

### Estimated completion: [date]
```
