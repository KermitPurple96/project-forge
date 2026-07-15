---
description: Create, update, and track a detailed project roadmap with sprints, specs, research, blockers, and progress visualization
---

# Roadmap

Create and manage a detailed project roadmap. Breaks features into researched, specified sprints with clear deliverables, blockers, and progress tracking.

## Invocation

```
/roadmap                           # Show current roadmap status
/roadmap create                    # Create roadmap from requirements (after /forge-init)
/roadmap add "feature description" # Add a feature to the roadmap
/roadmap sprint 3                  # Show sprint 3 details
/roadmap next                      # Start the next sprint
/roadmap block "description"       # Log a blocker
/roadmap unblock BLOCK-001         # Resolve a blocker
/roadmap replan                    # Replan remaining sprints based on progress
```

## Instructions for Claude

### On `create`

Read all templates from forge-init (requirements.md, data-model.md, mvp-definition.md, task-breakdown.md). Then:

1. **Group features into sprints** by dependency order:
   - Sprint 1: Foundation (auth, database, base layout)
   - Sprint 2-N: Features (ordered by user value, not technical convenience)
   - Final sprint: Polish (testing, performance, deployment)

2. **For each sprint, create a specification:**

```markdown
## Sprint N: [Name]

### Goal
One sentence describing what's shippable after this sprint.

### Tasks
| ID | Task | Type | Est. | Deps | Agent |
|----|------|------|------|------|-------|
| S2-001 | User profile API | backend | 2h | S1-003 | backend |
| S2-002 | Profile page UI | frontend | 3h | S2-001 | frontend |
| S2-003 | Avatar upload | fullstack | 2h | S2-001 | backend+frontend |

### Research needed (before implementation)
- [ ] Best image upload approach (direct to S3 vs server proxy)
- [ ] Avatar crop library comparison

### Spec notes
- Profile page must show: name, email, avatar, join date, recent activity
- Avatar: max 2MB, jpg/png/webp, crop to square
- API: GET/PUT /api/users/:id/profile

### Blockers
(none yet)

### Demo checkpoint
At sprint end, user should be able to: create account → upload avatar → view profile page.
Show this in the browser before proceeding.
```

3. **Write to `docs/roadmap/`:**
   - `docs/roadmap/overview.md` — high-level sprint list with progress bars
   - `docs/roadmap/sprint-N.md` — detailed spec per sprint
   - `docs/roadmap/blockers.md` — active blockers log
   - `docs/roadmap/changelog.md` — what shipped per sprint

### On `roadmap` (status)

Display:
```
## Roadmap: [Project Name]

Sprint 1: Foundation     ████████████ 100% ✅
Sprint 2: Core Features  ██████░░░░░░  50% 🔄 ← current
Sprint 3: Integrations   ░░░░░░░░░░░░   0% ⬜
Sprint 4: Polish & Deploy ░░░░░░░░░░░░  0% ⬜

Overall: 12/28 tasks (43%)
Blockers: 1 active (BLOCK-003: waiting for Stripe API key)
Next: S2-004 — Search & filters (backend)
```

### On `next`

1. Read current sprint status
2. If current sprint done → run demo checkpoint → ask user to verify
3. Mark sprint complete, move to next
4. Show next sprint spec
5. Auto-commit roadmap state update

### On `block` / `unblock`

Log blockers with:
- ID (auto-increment: BLOCK-001)
- Description
- Impact (which tasks are blocked)
- Created date
- Resolution (when unblocked)

### Research integration

Before each sprint starts:
1. Check "Research needed" list
2. Run `/deep-research` for each item
3. Save results to `docs/research/`
4. Update sprint spec with findings
5. Adjust estimates if research changes the approach

### Demo checkpoints

At the end of each sprint:
1. Start the dev server if not running
2. Open the app in the browser
3. Walk through the sprint's demo scenario
4. Take screenshots or ask user to verify
5. Log any issues found during demo

## Rules

1. **Sprints have demo checkpoints.** Never move to the next sprint without showing the user what shipped.
2. **Research before implementation.** If a sprint has research items, do them first.
3. **Blockers are visible.** Every blocker gets an ID and is tracked until resolved.
4. **Auto-commit roadmap changes.** Every sprint completion, blocker, and replan is committed.
5. **Replan is normal.** When progress differs from estimates, replan remaining sprints — don't pretend the original plan still works.
6. **Specs are living documents.** Sprint specs get updated as implementation reveals new requirements.
