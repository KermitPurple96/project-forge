---
description: Thorough review of all code written today — parallel agents, security, testing, UI states
---

# Strict Code Review

Perform a thorough review of code changes across the full stack. Uses parallel agents for speed. Fixes every CRITICAL and HIGH issue before reporting.

## Invocation

```
/code-review                       # Interactive — reviews uncommitted changes + today's commits
/code-review today                 # All commits from today
/code-review 2026-05-19            # All commits on that date
/code-review abc123f               # Single commit
/code-review abc123f..def456a      # Commit range
/code-review "dropper injection"   # Search commits by message keyword
/code-review LoaderConfig.tsx      # Changes in specific file
/code-review --scope bl            # Only Black-Lotus
/code-review --scope df            # Only Devil-Fruit
/code-review --scope bd            # Only Black-Death
/code-review --last 5              # Last 5 commits
```

## Instructions for the AI

### Step 1: Determine scope

Based on the argument, find what to review:

```bash
# No argument: uncommitted + today
git diff --stat HEAD
git log --since="$(date +%Y-%m-%d)" --oneline

# By date
git log --since="2026-05-19" --until="2026-05-20" --oneline
git diff $(git log --since="2026-05-19" --format=%H | tail -1)..HEAD

# By commit
git show <hash> --stat
git diff <hash>^..<hash>

# By range
git diff <start>..<end> --stat

# By keyword
git log --all --grep="dropper" --oneline
git diff $(git log --grep="dropper" --format=%H | tail -1)^..$(git log --grep="dropper" --format=%H | head -1)

# By file
git log --follow -p -- <filepath>
```

Read EVERY diff line. Don't skip files because they "look fine". Read the actual changes.

### Step 2: Launch parallel review agents

Split by area and launch ALL simultaneously via Agent tool:

**Agent 1 — C/Agent code** (`agent/src/`, `agent/include/`)
- Memory safety: buffer overflows, use-after-free, double-free
- CRT-free compliance: no disallowed C runtime calls
- API resolution: all calls through DJB2 hash, not GetProcAddress in plaintext
- OPSEC: no debug strings in release, no hardcoded IPs/paths, no stack string leaks
- Thread safety: BeaconGate guards on all API calls, no race conditions
- Resource cleanup: handles closed, memory freed, COM released

**Agent 2 — Go/Backend** (`teamserver/pkg/`, `server/pkg/`)
- SQL injection: parameterized queries only, no string concatenation
- Auth: JWT validation on all endpoints, operation scoping
- Error handling: no panic in handlers, all errors wrapped with context
- Concurrency: mutex/channel usage correct, no goroutine leaks
- API contract: response shapes match TypeScript types field-for-field
- Validation: user input validated at boundary, internal calls trust data

**Agent 3 — TypeScript/Frontend** (`client/src/`, `ui/src/`)
- Type safety: no `any` types, no `@ts-ignore`, no type assertions without reason
- UI states: loading, empty, success, error, edge case — ALL handled
- Accessibility: keyboard nav, aria labels, focus management
- State management: Zustand store updates immutable, no stale closures
- API calls: error handling, loading states, AbortController for cleanup
- New features VISIBLE: dropdown options exist, routes registered, menu items present

**Agent 4 — Scripts/Config/Docs** (`scripts/`, `docs/`, migrations, configs)
- No hardcoded credentials (check for passwords, tokens, API keys)
- Python scripts: type hints, argparse, error handling, timeout on network calls
- Docs match code: if code changed, docs must reflect it
- Migrations: reversible, tested, no data loss
- Config: no dev values in prod configs, secrets from env vars

Each agent MUST:
1. Read every changed file in their scope (use `git diff` not just filenames)
2. List findings with `file:line — SEVERITY — description`
3. Fix CRITICAL and HIGH issues directly (edit the files)
4. Report MEDIUM/LOW without fixing

### Step 3: Cross-cutting checks (main agent does these)

After parallel agents complete:

**Security scan:**
```bash
# Check for secrets
grep -rn "password\|secret\|token\|apikey\|api_key" --include="*.go" --include="*.ts" --include="*.py" <changed-files> | grep -v test | grep -v _test
# Check for debug leftovers
grep -rn "console\.log\|fmt\.Print\|println\|TODO\|FIXME\|HACK\|XXX" <changed-files>
```

**Type consistency:**
- Go struct JSON tags match TypeScript interface fields
- API response shape matches client-side type assertions
- New fields added to Go structs also added to TypeScript types

**Feature completeness:**
- Every backend change has a corresponding UI path (not API-only)
- Every new dropdown option maps to a handled backend value
- Every new command/technique is in docs, AI catalog, and test suite

**Build verification:**
```bash
# Both repos must compile clean
cd teamserver && go build ./...
cd client && npx tsc --noEmit
cd server && go build ./...   # DF
```

**Test verification:**
```bash
# Run and report counts
cd teamserver && go test ./... -timeout 300s 2>&1 | tail -5
cd client && pnpm test 2>&1 | tail -5
cd server && go test ./... -timeout 60s 2>&1 | tail -5
```

### Step 4: Fix and report

1. Fix ALL CRITICAL and HIGH issues found by agents
2. Run builds + tests again after fixes
3. Output the verification matrix

## Review categories (agents check ALL of these)

### Code quality
- Dead code, unused imports, leftover debug statements
- Duplication — extract shared functions
- Naming conventions consistent with codebase
- Functions do one thing, files handle one concern

### Security — CRITICAL
- No hardcoded secrets (API keys, tokens, passwords, creds)
- Loaded from env vars or secrets manager
- .env in .gitignore
- No PII in logs
- Input validation at boundaries (SQL injection, XSS, path traversal, command injection)
- No `exec.Command` with user-controlled strings

### OPSEC — CRITICAL (agent/C code)
- No plaintext API names (use DJB2 hash resolution)
- No debug strings in release build (`#ifdef DEBUG_BUILD` guards)
- No hardcoded C2 URLs, IPs, or domains
- Stack strings for sensitive constants
- PE metadata stripped (Rich header, timestamps, debug directory)

### Robustness
- Error handling: try/catch, validation, nil checks
- API calls have timeouts and error states
- Resource cleanup: file handles, connections, goroutines
- Graceful degradation when dependencies unavailable

### Testing
- New code has tests (not just "it compiles")
- Edge cases covered: empty, null, boundary, very large, concurrent
- Test suite still passes after changes
- No test count regression

### Performance
- No N+1 queries, unnecessary allocations, redundant API calls
- Frontend: no unnecessary re-renders, large lists virtualized
- Backend: database queries use indexes, no full table scans

### UI availability & UX
- New features accessible through UI (buttons, menus, dropdowns, routes)
- All 5 states: loading, empty, success, error, edge case
- No raw IDs, hex codes, or internal names shown to users
- Forms validate input before submission
- Error messages are helpful, not stack traces

### Documentation
- Code changes reflected in TechnicalDocs.tsx and UserGuide.tsx
- API changes reflected in endpoint docs
- New techniques in AI catalog and prompts
- CHANGELOG updated

### Git hygiene
- No commented-out code blocks
- No merge conflict markers
- No unfixed TODOs that block functionality
- Commit messages explain WHY, not just WHAT

## Output format

```
## Review Results: <scope description>

### Files Reviewed: N
### Findings: N total (C critical, H high, M medium, L low)
### Fixed: N

| File | Line | Severity | Finding | Status |
|------|------|----------|---------|--------|
| loader.go | 104 | HIGH | Default injection still "create_thread" | FIXED |
| config.go | 198 | MEDIUM | Missing JSON tag on new field | FIXED |
| LoaderConfig.tsx | 42 | LOW | Description could be clearer | Reported |

### Build Status
| Check | Result |
|-------|--------|
| Go build (BL) | PASS |
| Go build (DF) | PASS |
| TypeScript | PASS |
| Go tests (BL) | 14/14 PASS |
| Go tests (DF) | 12/12 PASS |
| Client tests | 901/901 PASS |

### Summary
- CRITICAL: 0 found
- HIGH: 3 found, 3 fixed
- MEDIUM: 5 found, 2 fixed, 3 reported
- LOW: 2 found, 0 fixed, 2 reported
```

## Rules

1. **Read the actual diff**: Don't review file names — read the code changes line by line.
2. **Fix, don't just report**: CRITICAL and HIGH must be fixed before the review is done.
3. **Verify after fixing**: Run builds and tests after every fix.
4. **No false positives**: Don't report "potential issue" without evidence. Verify it IS an issue.
5. **Cross-reference**: A Go struct field added? Check if TypeScript type was updated. A new injection technique? Check if it's in docs, AI catalog, tests, AND UI.
6. **Security is non-negotiable**: Any hardcoded secret, any SQL injection, any command injection = CRITICAL. No exceptions.
7. **OPSEC is non-negotiable**: Any plaintext API name in agent release build, any debug string leak, any hardcoded C2 address = CRITICAL.
8. **Don't nitpick style**: Focus on bugs, security, and correctness. Ignore formatting preferences unless they cause real confusion.
