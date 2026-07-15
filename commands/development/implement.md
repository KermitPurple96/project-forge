---
description: Guided feature implementation — analyze impact across the full stack before writing code, then implement with automatic commits
---

# Implement

Guided implementation of a feature, fix, or change. Analyzes impact across the full stack BEFORE writing code, then implements step by step with automatic commits.

## Invocation

```
/implement "add user profile page"
/implement "integrate Stripe payments"
/implement "fix: login redirects to wrong page"
/implement "refactor: extract auth middleware"
/implement --plan-only "add real-time notifications"    # Analysis only, no code
/implement --from issue#42                              # From GitHub issue
```

## Instructions for Claude

### Phase 1: Impact Analysis (BEFORE touching any code)

Read the codebase and answer these questions:

```
## Impact Analysis: [feature description]

### What needs to change?

**Database:**
- [ ] New tables/columns needed? → list them with types
- [ ] Migrations required? → describe the migration
- [ ] Seed data changes? → what test data

**Backend/API:**
- [ ] New endpoints? → list METHOD /path with request/response shapes
- [ ] Modified endpoints? → what changes and why
- [ ] New middleware? → what it does
- [ ] Business logic? → where does it live, what functions

**Frontend:**
- [ ] New pages/routes? → list paths
- [ ] New components? → list with props
- [ ] Modified components? → what changes
- [ ] State management? → new stores, modified stores
- [ ] API calls? → new service functions

**Config/Infra:**
- [ ] New env vars? → list with descriptions
- [ ] New dependencies? → list with justification (apply ponytail ladder: do we NEED this dep?)
- [ ] CI/CD changes? → what and why

### Affected files (existing)
List every file that will be modified, with what changes in each.

### New files
List every file that will be created, with its responsibility.

### Risk assessment
- What could break? Which existing features are affected?
- What edge cases need handling?
- What tests need to be added?
```

Show this analysis to the user:
> "Here's what needs to change for [feature]. Review this before I start coding."

Wait for approval before proceeding to Phase 2.

### Phase 2: Implementation

Execute in order (backend-first, then frontend):

1. **Database** — migrations, schema changes
2. **Backend** — API endpoints, business logic, middleware
3. **Frontend** — pages, components, state, API integration
4. **Tests** — unit tests for new logic, integration tests for API
5. **Docs** — update any affected documentation

For each step:
- Write the code
- Run relevant tests
- Auto-commit with descriptive message: `feat(scope): what changed [TASK-ID]`
- Report progress

### Phase 3: Verification

After implementation:
- Run full test suite
- Check for TypeScript/build errors
- Open the feature in the browser (if UI change)
- Verify the happy path works end-to-end

### Notes & Blockers

During implementation, maintain a running log:

```
### Implementation Notes
- [timestamp] Discovered X while implementing Y — adjusted approach
- [timestamp] BLOCKER: need API key for Z service — skipping for now
- [timestamp] Found existing util that handles part of this — reusing instead of building
```

## Rules

1. **Analyze before coding.** Phase 1 is mandatory. Never start coding without understanding full impact.
2. **Backend first.** API and data layer before UI. The frontend should call real endpoints, not mock data.
3. **One commit per logical change.** Not one giant commit at the end.
4. **Apply the ponytail ladder.** Before adding a dependency: does stdlib do it? Does the platform handle it? Can it be a one-liner?
5. **Show samples.** For UI changes, open the result in the browser and take a screenshot or ask the user to verify.
6. **Track blockers.** Don't silently skip something — log it as a blocker.
7. **Existing patterns win.** If the codebase has a pattern for X, follow it. Don't introduce a new pattern for the same thing.
