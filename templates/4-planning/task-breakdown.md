# 🤖 Task Breakdown

> Claude generates this from your roadmap. Tasks are grouped into sprints for automated execution.

## Instructions for Claude

Read `roadmap.md` and `pipeline-config.md`, then:
1. Break each deliverable into atomic tasks (~2-4h each)
2. Identify dependencies between tasks
3. Group tasks into sprints based on `pipeline-config.md` → `sprints.grouping_strategy`
4. Assign an agent to each task based on the files involved
5. Present to user for approval before execution

## Sprint configuration

```yaml
# Override pipeline-config.md per sprint if needed
sprint_1:
  mode: semi-auto    # Careful start
sprint_2:
  mode: full-auto    # Feeling confident
sprint_3:
  mode: semi-auto    # New complex feature, want oversight
```

---

## Milestone: MVP

### Sprint 1: [Theme/Focus]

**Mode:** semi-auto | full-auto
**Status:** ⬜ not started | 🔄 in progress | ✅ complete

| ID | Task | Agent | Depends on | Hours | Status |
|----|------|-------|-----------|-------|--------|
| MVP-001 | | frontend | — | 3 | ⬜ |
| MVP-002 | | backend | — | 2 | ⬜ |
| MVP-003 | | database | — | 2 | ⬜ |
| MVP-004 | | frontend | MVP-002 | 3 | ⬜ |

**Sprint checkpoint:** e2e-test → code-review → integration-tests → tag

### Sprint 2: [Theme/Focus]

**Mode:** full-auto
**Status:** ⬜ not started

| ID | Task | Agent | Depends on | Hours | Status |
|----|------|-------|-----------|-------|--------|
| MVP-005 | | backend | MVP-003 | 4 | ⬜ |
| MVP-006 | | frontend | MVP-005 | 3 | ⬜ |
| MVP-007 | | infra | — | 2 | ⬜ |

**Sprint checkpoint:** e2e-test → code-review → integration-tests → tag

**Milestone checkpoint:** security-review → performance-audit → changelog → v1.0.0

---

## Milestone: v1.0

### Sprint 3: [Theme/Focus]

**Mode:** semi-auto
**Status:** ⬜ not started

| ID | Task | Agent | Depends on | Hours | Status |
|----|------|-------|-----------|-------|--------|
| V1-001 | | | — | | ⬜ |

<!-- Add more sprints as needed -->

---

## Status legend

| Icon | Status | Meaning |
|------|--------|---------|
| ⬜ | Todo | Not started |
| 🔄 | In progress | Currently being worked on by an agent |
| ✅ | Done | Implemented, tested, committed |
| 🚫 | Blocked | Failed after 3 attempts, needs manual help |
| ⏭️ | Skipped | Dependency is blocked, can't proceed |

## Progress summary

<!-- Orchestrator updates this automatically -->

```
Milestone: MVP
  Sprint 1: ⬜ 0/4 tasks (0%)
  Sprint 2: ⬜ 0/3 tasks (0%)
  Overall:  ⬜ 0/7 tasks (0%)

Milestone: v1.0
  Sprint 3: ⬜ 0/? tasks (0%)
  Overall:  ⬜ 0/? tasks (0%)

Total: 0/? tasks — Estimated: ?h remaining
```
