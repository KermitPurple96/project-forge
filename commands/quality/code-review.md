---
description: Thorough review of all code written today — parallel agents, security, testing, UI states, bloat detection
---

# Strict Code Review

Perform a thorough review of code changes across the full stack. Uses parallel agents for speed. Fixes every CRITICAL and HIGH issue before reporting.

## Invocation

```
/code-review                       # Interactive — reviews uncommitted changes + today's commits
/code-review today                 # All commits from today
/code-review 2026-05-19            # All commits on that date
/code-review abc123f               # Single commit hash
/code-review abc123f..def456a      # Commit range
/code-review "auth refactor"       # Search commits by message keyword
/code-review UserSettings.tsx      # Changes in specific file
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
git log --all --grep="auth" --oneline
git diff $(git log --grep="auth" --format=%H | tail -1)^..$(git log --grep="auth" --format=%H | head -1)

# By file
git log --follow -p -- <filepath>
```

Read EVERY diff line. Don't skip files because they "look fine". Read the actual changes.

### Step 2: Launch parallel review agents

Split by area and launch ALL simultaneously via Agent tool:

**Agent 1 — Backend code** (Go/Python/Node/Rust — whatever the project uses)
- SQL injection: parameterized queries only, no string concatenation
- Auth: token/session validation on all endpoints, proper scoping
- Error handling: no panics/unhandled exceptions in handlers, all errors wrapped with context
- Concurrency: mutex/channel/lock usage correct, no goroutine/thread leaks
- API contract: response shapes match frontend types field-for-field
- Validation: user input validated at boundary, internal calls trust data
- Resource cleanup: connections, file handles, transactions properly closed

**Agent 2 — Frontend code** (React/Vue/Svelte/vanilla — whatever the project uses)
- Type safety: no `any` types, no `@ts-ignore`, no type assertions without reason
- UI states: loading, empty, success, error, edge case — ALL handled
- Accessibility: keyboard nav, aria labels, focus management
- State management: store updates immutable, no stale closures
- API calls: error handling, loading states, AbortController for cleanup
- New features VISIBLE: dropdown options exist, routes registered, menu items present

**Agent 3 — Infrastructure/Config/Docs** (migrations, configs, CI/CD, docs)
- No hardcoded credentials (check for passwords, tokens, API keys)
- Scripts: type hints/types, error handling, timeout on network calls
- Docs match code: if code changed, docs must reflect it
- Migrations: reversible, tested, no data loss
- Config: no dev values in prod configs, secrets from env vars
- CI/CD: pipelines still pass, no skipped steps

**Agent 4 — Cross-cutting integrity** (API contracts, type consistency, feature completeness)
- Every API call in client code has a matching route in the backend router
- API response shapes match frontend interfaces field-for-field
- No silent 404s from mismatched paths
- No buttons that call nonexistent endpoints
- No dropdowns populated from endpoints that return different shapes than expected
- Go struct JSON tags / Python dict keys / API schemas match frontend type definitions
- New fields added to backend also added to frontend types
- Every backend capability has a corresponding UI path (not API-only)

**Agent 5 — Bloat audit** (ponytail-style over-engineering scan)
For each changed file, check the ponytail ladder:
- `delete:` dead code, unused flexibility, speculative features — remove
- `stdlib:` hand-rolled thing the standard library ships — name the stdlib function
- `native:` dependency doing what the platform already does — name the native feature
- `yagni:` abstraction with one implementation, config nobody sets, wrapper with one caller — inline it
- `shrink:` same logic achievable in fewer lines — show the shorter form

Report each as: `file:line — TAG — what to cut. replacement.`
End bloat section with: `net: -N lines possible` or `Lean already.`

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
grep -rn "password\|secret\|token\|apikey\|api_key" --include="*.go" --include="*.ts" --include="*.py" --include="*.rs" <changed-files> | grep -v test | grep -v _test
# Check for debug leftovers
grep -rn "console\.log\|fmt\.Print\|println\|TODO\|FIXME\|HACK\|XXX" <changed-files>
```

**Type consistency:**
- Backend struct/schema JSON tags match frontend interface fields
- API response shape matches client-side type assertions
- New fields added to backend structs also added to frontend types

**Feature completeness:**
- Every backend change has a corresponding UI path (not API-only)
- Every new dropdown option maps to a handled backend value
- Every new feature is in docs and test suite

**Build verification:**
```bash
# Adapt to your stack — examples:
# Go
go build ./...
# Node/TypeScript
npx tsc --noEmit
# Python
python -m py_compile <changed-files>
# Rust
cargo build
```

**Test verification:**
```bash
# Adapt to your stack — examples:
# Go
go test ./... -timeout 300s 2>&1 | tail -5
# Node
pnpm test 2>&1 | tail -5
# Python
pytest --tb=short 2>&1 | tail -10
# Rust
cargo test 2>&1 | tail -5
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
- No shell execution with user-controlled strings

### Frontend <> Backend integrity — CRITICAL
- Every API call in client code has a matching route in the backend router
- API response shapes match TypeScript/frontend interfaces field-for-field
- No silent 404s from mismatched paths
- No buttons that call nonexistent endpoints
- No dropdowns populated from endpoints that return different shapes than expected

### Code synergy & duplication — HIGH
- No duplicate data sources (same list hardcoded in multiple files instead of single source of truth)
- No dead code (components/types/functions defined but never imported — remnants of refactors)
- Feature parity: when backend has N capabilities, UI exposes all N (not a stale subset)
- Naming consistency: same concept uses same name across frontend, backend, and docs

### Over-engineering & bloat — HIGH
For each changed file, check the ponytail ladder:
- `delete:` dead code, unused flexibility, speculative features — remove
- `stdlib:` hand-rolled thing the standard library ships — name the stdlib function
- `native:` dependency doing what the platform already does — name the native feature
- `yagni:` abstraction with one implementation, config nobody sets, wrapper with one caller — inline it
- `shrink:` same logic achievable in fewer lines — show the shorter form

Report each as: `file:line — TAG — what to cut. replacement.`
End bloat section with: `net: -N lines possible` or `Lean already.`

### Robustness
- Error handling: try/catch, validation, nil/null checks
- API calls have timeouts and error states
- Resource cleanup: file handles, connections, goroutines/threads
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
- Code changes reflected in user-facing docs
- API changes reflected in endpoint docs / OpenAPI spec
- CHANGELOG updated for user-visible changes

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
| auth.go | 104 | HIGH | SQL query uses string concat | FIXED |
| config.go | 198 | MEDIUM | Missing JSON tag on new field | FIXED |
| Settings.tsx | 42 | LOW | Description could be clearer | Reported |

### Bloat Report
| File | Line | Tag | Finding | Replacement |
|------|------|-----|---------|-------------|
| utils.ts | 12 | stdlib | hand-rolled deepClone | structuredClone() |
| api.ts | 88 | yagni | AbstractFetcherFactory with one impl | inline fetch call |
net: -45 lines possible

### Build Status
| Check | Result |
|-------|--------|
| Backend build | PASS |
| Frontend build | PASS |
| Backend tests | 14/14 PASS |
| Frontend tests | 120/120 PASS |

### Summary
- CRITICAL: 0 found
- HIGH: 3 found, 3 fixed
- MEDIUM: 5 found, 2 fixed, 3 reported
- LOW: 2 found, 0 fixed, 2 reported
- Bloat: net -45 lines possible
```

## Rules

1. **Read the actual diff**: Don't review file names — read the code changes line by line.
2. **Fix, don't just report**: CRITICAL and HIGH must be fixed before the review is done.
3. **Verify after fixing**: Run builds and tests after every fix.
4. **No false positives**: Don't report "potential issue" without evidence. Verify it IS an issue.
5. **Cross-reference**: A backend field added? Check if frontend type was updated. A new feature? Check if it's in docs, tests, AND UI.
6. **Security is non-negotiable**: Any hardcoded secret, any SQL injection, any command injection = CRITICAL. No exceptions.
7. **Don't nitpick style**: Focus on bugs, security, correctness, and bloat. Ignore formatting preferences unless they cause real confusion.
8. **Bloat is real work**: Over-engineering wastes time forever. Flag it, quantify it, cut it.
