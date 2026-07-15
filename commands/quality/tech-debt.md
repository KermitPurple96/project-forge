---
description: Harvest deliberate shortcuts and deferrals into a tracked debt ledger
---

# Tech Debt Ledger

Scan the codebase for marked technical debt comments and compile them into a tracked ledger. Prevents deliberate shortcuts from quietly becoming permanent.

## Invocation

```
/tech-debt                      # Scan and report
/tech-debt --save               # Write ledger to TECH-DEBT.md
/tech-debt --stale              # Show only markers with no upgrade trigger
```

## Convention

Mark deliberate shortcuts with: `// debt: <what was simplified>, <upgrade trigger>`

Examples:
```go
// debt: global lock, per-account locks if throughput > 100 req/s
```
```python
# debt: O(n^2) loop, switch to index if items > 10k
```
```typescript
// debt: polling every 5s, switch to WebSocket when client supports it
```
```rust
// debt: single-threaded, spawn worker pool if latency > 50ms p99
```

The format is: `(// | #) debt: <what was simplified>, <upgrade trigger>`

The upgrade trigger is the condition under which the shortcut should be replaced with the proper solution. Every debt marker MUST have one.

## Instructions for the AI

### Step 1: Scan for debt markers

```bash
grep -rnE '(#|//) ?debt:' . \
  --include='*.go' --include='*.ts' --include='*.tsx' --include='*.js' --include='*.jsx' \
  --include='*.py' --include='*.rs' --include='*.c' --include='*.h' \
  --include='*.rb' --include='*.java' --include='*.kt' --include='*.swift' \
  | grep -v node_modules | grep -v .git | grep -v vendor | grep -v dist | grep -v build
```

### Step 2: Parse each marker

For each result, extract:
- **File and line number**
- **What was simplified** (the shortcut taken)
- **Upgrade trigger** (the condition for replacing it)

If a marker has no upgrade trigger (no comma, no second clause), tag it `no-trigger`.

### Step 3: Check for staleness (if `--stale` flag)

A debt marker is stale if:
- It has no upgrade trigger (`no-trigger` tag)
- The trigger condition may already be met (check if the system has grown past the stated limit)
- The surrounding code has been significantly refactored but the debt comment remains unchanged

### Step 4: Report

Group findings by file:

```
## Tech Debt Ledger

### src/api/auth.ts
- Line 42: global rate limiter. ceiling: all users share one bucket. upgrade: per-user rate limit if users > 1000.
- Line 89: password hashed with bcrypt cost=10. ceiling: 100ms per hash. upgrade: increase cost when hardware allows < 200ms at cost=12.

### src/workers/sync.go
- Line 15: polling every 30s. ceiling: 30s stale data window. upgrade: switch to CDC/webhooks when source supports it.
- Line 78: full table scan on sync. ceiling: O(n) per run. upgrade: incremental sync with cursor if table > 100k rows. `no-trigger` (original comment missing trigger — inferred)

### src/utils/cache.py
- Line 3: in-memory dict cache. ceiling: single-process, lost on restart. upgrade: Redis when running multiple instances.

5 markers, 1 with no trigger.
```

### Step 5: Save (if `--save` flag)

Write the report to `TECH-DEBT.md` in the repo root. Include a timestamp header:

```markdown
# Tech Debt Ledger
> Generated: 2026-07-15. Re-run with `/tech-debt --save` to update.

[findings grouped by file as above]
```

## Output

One row per marker, grouped by file:

```
<file>:<line>, <what was simplified>. ceiling: <the limit>. upgrade: <trigger>.
```

Flag rot risk: any debt comment with no upgrade trigger gets a `no-trigger` tag.

End with: `<N> markers, <M> with no trigger.`

Nothing found: `No tracked debt. Clean ledger.`

## Rules

1. **Debt markers are a discipline, not a smell.** Having them means the team is honest about shortcuts. Don't treat the count as a quality score.
2. **Every marker MUST have an upgrade trigger.** Flag those without one — they're the real risk (shortcuts with no exit plan).
3. **Don't confuse debt with TODOs.** `TODO` is "I should do this someday." `debt:` is "I chose a simpler path and here's when to upgrade." Only scan for `debt:` markers.
4. **Don't audit code quality here.** This is a ledger scan, not a code review. Route quality issues to `/code-review`.
5. **Report only.** Don't fix the debt, don't refactor, don't add markers. Just inventory what exists.
6. **Infer missing triggers if possible.** If a debt comment says "polling" but doesn't state the trigger, note it as `no-trigger` and suggest a reasonable trigger.
