# 🤖 Command: orchestrator

> Master command that drives the development pipeline. Reads the roadmap, groups tasks into sprints, and executes them with the configured automation level.

## Invocation

```
/orchestrator                          # Start or continue from where we left off
/orchestrator --sprint 3               # Execute sprint 3 specifically
/orchestrator --mode full-auto         # Override mode for this session
/orchestrator --mode semi-auto         # Task by task with confirmation
/orchestrator --status                 # Show current pipeline status
/orchestrator --plan                   # Show what would be executed (dry run)
/orchestrator --skip-to MVP-015        # Skip to a specific task
/orchestrator --rollback-sprint 2      # Rollback all changes from sprint 2
```

## Instructions for Claude

### Step 0: Initialize

1. Read `pipeline-config.md` for automation rules
2. Read `task-breakdown.md` for tasks and sprint assignments
3. Read `stack-selection.md` to know which agents are available
4. Determine current state:
   - Which tasks are ✅ done?
   - Which sprint are we in?
   - What's the current mode (full-auto / semi-auto)?
5. Run `context-init` if this is a new session

### Step 1: Sprint planning (if sprints not yet assigned)

If `task-breakdown.md` doesn't have sprint assignments:

1. Read all tasks with status ⬜
2. Group into sprints based on `pipeline-config.md` → `sprints.grouping_strategy`:
   - **dependency:** Tasks that depend on each other go in the same sprint
   - **feature:** Tasks for the same feature grouped together
   - **layer:** Backend tasks first, then frontend, then integration
3. Respect `max_tasks_per_sprint` limit
4. Write sprint assignments back to `task-breakdown.md`
5. Present the sprint plan to the user for approval

### Step 2: Execute sprint

For each task in the current sprint:

```
┌─────────────────────────────────────────────────┐
│                   TASK LOOP                     │
│                                                 │
│  1. Read task from task-breakdown.md            │
│  2. Launch appropriate stack agent(s)           │
│     └── agent reads task + context              │
│     └── agent implements the task               │
│  3. Run unit tests (scope: changed files)       │
│     └── if FAIL → agent fixes → retest          │
│     └── max 3 fix attempts, then flag to user   │
│  4. Auto-commit with conventional message       │
│     └── feat(scope): description [TASK-ID]      │
│  5. Update task-breakdown.md → ✅               │
│  6. If semi-auto → ask user to continue         │
│     If full-auto → proceed to next task         │
│                                                 │
└─────────────────────────────────────────────────┘
```

### Step 3: Sprint completion

When all tasks in a sprint are ✅:

```
┌─────────────────────────────────────────────────┐
│              SPRINT CHECKPOINT                  │
│                                                 │
│  1. Run e2e-test for sprint commits             │
│  2. Run code-review for sprint commits          │
│  3. Run integration-tests                       │
│  4. If any CRITICAL/HIGH findings:              │
│     └── Fix automatically                       │
│     └── Re-run failing checks                   │
│  5. Generate sprint progress-report             │
│  6. Git tag: sprint-{N}-complete                │
│  7. Notify user with sprint summary             │
│                                                 │
└─────────────────────────────────────────────────┘
```

### Step 4: Milestone completion

When all sprints in a milestone are done:

```
┌─────────────────────────────────────────────────┐
│             MILESTONE CHECKPOINT                │
│                                                 │
│  1. Run security-review (full codebase)         │
│  2. Run performance-audit                       │
│  3. Run dependency-audit                        │
│  4. Run secrets-scan                            │
│  5. Generate/update documentation               │
│  6. Generate changelog                          │
│  7. Version bump (minor)                        │
│  8. Git tag: v{X.Y.Z}                          │
│  9. Generate milestone report                   │
│                                                 │
└─────────────────────────────────────────────────┘
```

## Agent dispatch

The orchestrator dispatches tasks to the appropriate agent based on the files involved:

```
Task involves frontend files? → Launch frontend-agent
Task involves backend files?  → Launch backend-agent
Task involves database?       → Launch database-agent
Task involves infra/config?   → Launch infra-agent
Task spans multiple layers?   → Launch agents in parallel, coordinate results
```

Agent definitions are in `.forge/agents/`. If they don't exist, run `agent-gen` first.

## Error handling

```
Task fails to implement:
  → Retry once with more context
  → If still fails → mark as 🚫 blocked, log reason, continue to next task
  → Non-blocking tasks continue; dependent tasks skip

Unit tests fail after implementation:
  → Agent attempts fix (max 3 attempts)
  → If still fails → revert changes, mark task as 🚫, notify user

Sprint checkpoint fails:
  → Fix CRITICAL/HIGH issues automatically
  → If unfixable → pause pipeline, notify user with details
  → User decides: fix manually, skip check, or rollback sprint

Build breaks:
  → Identify breaking commit via git bisect
  → Attempt auto-fix
  → If unfixable → rollback to last green commit, notify user
```

## State management

The orchestrator tracks state in `task-breakdown.md` using these markers:

```
⬜ todo        — Not started
🔄 in-progress — Currently being worked on
✅ done        — Completed and committed
🚫 blocked     — Failed, needs manual intervention
⏭️ skipped     — Skipped (dependency blocked)
```

## Output format

After each task:
```
✅ MVP-003 | feat(auth): add JWT refresh endpoint | 2 files changed | tests: 4/4 pass
```

After each sprint:
```
## Sprint 2 Complete

| Metric | Value |
|--------|-------|
| Tasks completed | 5/5 |
| Tasks blocked | 0 |
| Commits | 5 |
| Tests added | 12 |
| Test suite | 47/47 PASS |
| E2E tests | 8/8 PASS |
| Code review | 0 critical, 1 medium (fixed) |
| Duration | ~45 min |

Next: Sprint 3 (4 tasks, mode: full-auto)
Continue? [Y/n]
```

## Rules

1. **Never skip tests** — if tests fail, fix or flag. Never mark a task ✅ with failing tests.
2. **Never skip commits** — every task gets its own commit. No mega-commits.
3. **Respect the mode** — in semi-auto, ALWAYS wait for user confirmation between tasks.
4. **Agents are isolated** — each agent works on its layer. Cross-layer coordination goes through the orchestrator.
5. **State is always current** — task-breakdown.md is the source of truth. Update it after every action.
6. **Fail gracefully** — a blocked task doesn't stop the sprint. Skip dependents, continue independents.
7. **Report, don't hide** — every decision, skip, or failure is logged and reported.
