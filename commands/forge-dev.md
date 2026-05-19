# 🤖 Command: forge-dev

> Simplified development interface. The user just says "continue" and Claude handles everything.

## Invocation

```
/forge-dev                     # Continue the roadmap from where we left off
/forge-dev continue            # Same as above
/forge-dev status              # Show what's done, what's next
/forge-dev fix "description"   # Fix something the user noticed
/forge-dev change "description"# Change something already built
/forge-dev refactor [scope]    # Improve code quality without changing behavior
/forge-dev hotfix "description"# Emergency fix: branch from prod, fix, deploy, merge back
/forge-dev test                # Run tests on current state
/forge-dev test sprint         # Run full sprint test suite
/forge-dev review              # Code review of recent changes
/forge-dev pause               # Save state and stop
```

## Instructions for Claude

### On `continue` (default)

1. Run `context-sync` — understand where we are
2. Read `task-breakdown.md` — find current sprint and next ⬜ task
3. Display brief status:
   ```
   Sprint 2 | Task 3/6 | MVP-008: Create user profile page
   Mode: semi-auto | Next: implement → test → commit
   ```
4. Execute the orchestrator loop for this task:
   - Dispatch to appropriate agent
   - Implement the task
   - Run unit tests
   - Auto-commit
   - Update task-breakdown.md
5. In semi-auto: ask "Continue to next task?" before proceeding
6. In full-auto: proceed until sprint ends or error occurs

### On `status`

```
## Project: [Name]

### Current sprint: Sprint 2 — Core Features
████████░░ 60% (3/5 tasks done)

| ID | Task | Status |
|----|------|--------|
| MVP-006 | User CRUD API | ✅ done |
| MVP-007 | User list page | ✅ done |
| MVP-008 | User profile page | ✅ done |
| MVP-009 | Search & filters | ⬜ next |
| MVP-010 | Export to CSV | ⬜ todo |

### Overall: Sprint 2 of 4 | 12/22 tasks | ~18h remaining
### Last commit: feat(users): add profile page [MVP-008] (12 min ago)
```

### On `fix "description"`

1. User describes what's wrong: "the login button doesn't redirect after success"
2. Claude identifies the relevant files
3. Dispatches to the appropriate agent
4. Fixes the issue
5. Runs affected tests
6. Commits: `fix(auth): redirect after successful login`
7. Does NOT advance the task counter — this is a correction, not a new task

### On `change "description"`

1. User describes what they want different: "make the sidebar collapsible"
2. Claude assesses impact:
   - Is this a tweak to an existing task? → modify in place
   - Is this a new feature? → add to task-breakdown as a new task
   - Does it conflict with other tasks? → flag to user
3. Implements the change
4. Runs tests
5. Commits with appropriate message

### On `refactor [scope]`

1. If scope provided (file, module, feature), focus there. Otherwise scan full codebase.
2. Analyze for: duplicated code, functions >50 lines, files >300 lines, deep nesting, poor naming, dead code, missing types.
3. Prioritize by impact: what changes improve readability the most?
4. Refactor one thing at a time, running tests after each change.
5. Never change behavior — tests must pass without modification.
6. Commit: `refactor(scope): description`

### On `hotfix "description"`

1. Create hotfix branch: `git checkout -b hotfix/[description] main`
2. Implement the minimal fix (smallest possible change)
3. Write test that reproduces the bug
4. Run full test suite
5. Deploy to staging, run smoke-tests
6. If clean → deploy to production (via `forge-deploy production`)
7. Merge back to development branch
8. Hotfixes are MINIMAL — no refactoring, no new features

### On `test`

```
/forge-dev test          → unit tests on changed files since last commit
/forge-dev test sprint   → full sprint checkpoint (e2e + integration + review)
/forge-dev test all      → entire test suite
/forge-dev test security → security review
```

### On `review`

Run `code-review` on commits since last review. Fix CRITICAL/HIGH automatically.

### On `pause`

1. Commit any uncommitted work (WIP commit if incomplete)
2. Update task-breakdown.md with current state
3. Write `.forge/dev-state.md`:
   ```markdown
   # Dev State — paused 2026-05-19 14:32
   
   Sprint: 2
   Current task: MVP-009 (in progress, ~60% done)
   Last commit: abc123f
   Notes: Search UI done, filter logic pending
   Resume with: /forge-dev continue
   ```
4. Output: "Paused. Run `/forge-dev` to continue from MVP-009."

## Handling common user patterns

**"This looks wrong"** → Treat as `fix`. Ask what specifically is wrong.

**"Actually, I want it different"** → Treat as `change`. Assess impact.

**"Skip this task"** → Mark as ⏭️ skipped. Move to next. Flag if dependencies exist.

**"Go back and redo MVP-005"** → Revert commits for that task, re-implement, re-test.

**"Add a new feature"** → Add to task-breakdown in current or next sprint. Ask user where to prioritize.

**"I don't like the design"** → This is a design change. Assess scope: just CSS tweak? Or structural change?

**"How does X work?"** → Run `explain` command on the relevant code. Don't interrupt development flow.

## Rules

1. **Minimize friction** — the user should type as little as possible. "continue" is the main interaction.
2. **Show don't ask** — instead of "what do you want to work on?", show the next task and start.
3. **Fix before moving on** — if tests fail, fix them. Don't leave broken things behind.
4. **Context is everything** — always re-read state before acting. Never assume from memory.
5. **Corrections are free** — `fix` and `change` don't "cost" sprint progress. They're part of development.
