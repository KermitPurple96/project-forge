---
description: Whole-repo audit for over-engineering — find what to delete, simplify, or replace with stdlib/native equivalents
---

# Bloat Audit

Scan the entire codebase for unnecessary complexity. Rank findings by impact (biggest cut first). Does NOT apply fixes — reports only.

## Invocation

```
/bloat-audit                    # Full repo scan
/bloat-audit src/               # Specific directory
/bloat-audit --deps             # Focus on unnecessary dependencies
```

## Tags

- `delete:` dead code, unused flexibility, speculative feature. Replacement: nothing.
- `stdlib:` hand-rolled thing the standard library ships. Name the function.
- `native:` dependency doing what the platform already does. Name the native feature.
- `yagni:` abstraction with one implementation, config nobody sets, layer with one caller.
- `shrink:` same logic, fewer lines. Show the shorter form.

## Instructions for the AI

### Step 1: Inventory the codebase

```bash
# Get a full picture of the repo
find . -type f \( -name "*.go" -o -name "*.ts" -o -name "*.tsx" -o -name "*.py" -o -name "*.rs" -o -name "*.c" -o -name "*.h" -o -name "*.js" -o -name "*.jsx" \) \
  ! -path "*/node_modules/*" ! -path "*/.git/*" ! -path "*/vendor/*" ! -path "*/dist/*" ! -path "*/build/*" \
  | wc -l

# Count lines
find . -type f \( -name "*.go" -o -name "*.ts" -o -name "*.tsx" -o -name "*.py" -o -name "*.rs" \) \
  ! -path "*/node_modules/*" ! -path "*/.git/*" ! -path "*/vendor/*" \
  | xargs wc -l | tail -1

# List dependencies
cat package.json 2>/dev/null | jq '.dependencies, .devDependencies' 2>/dev/null
cat go.mod 2>/dev/null | head -30
cat requirements.txt 2>/dev/null
cat Cargo.toml 2>/dev/null | head -30
```

### Step 2: Hunt targets

Scan for each category:

**Dead code (`delete`)**
- Exported functions/types never imported elsewhere
- Feature flags that are always on or always off
- Commented-out code blocks
- Files with no importers (orphans)
- Config options nobody sets (always default)

**Stdlib replacements (`stdlib`)**
- Hand-rolled deep clone (use `structuredClone`, `copy.deepcopy`, etc.)
- Custom UUID generator (use `crypto.randomUUID`, `uuid` stdlib)
- Manual JSON parsing (use stdlib decoder)
- Custom HTTP client wrappers that just delegate
- Hand-rolled sorting, searching, or data structures

**Native replacements (`native`)**
- npm package for something the browser/Node API already does
- pip package for something Python stdlib ships
- Go dependency for something the standard library covers
- Rust crate for something `std` provides

**YAGNI (`yagni`)**
- Abstract classes / interfaces with exactly one implementation
- Factory functions that produce exactly one product
- Strategy pattern with one strategy
- Config options with only one valid value
- Wrapper functions that only delegate (add nothing)
- "Plugin" systems with one plugin
- Generic type parameters used at exactly one call site

**Shrinkable (`shrink`)**
- Multi-line logic that could be a one-liner
- Verbose error handling that could use a helper
- Repeated boilerplate that could be a loop or map
- Files exporting one small thing that could be inline

### Step 3: Dependency audit (if `--deps` flag)

```bash
# Node: check for unused deps
npx depcheck 2>/dev/null

# Or manually: for each dependency, grep for its import
for dep in $(cat package.json | jq -r '.dependencies // {} | keys[]'); do
  count=$(grep -rn "from ['\"]${dep}" src/ --include="*.ts" --include="*.tsx" --include="*.js" | wc -l)
  if [ "$count" -eq 0 ]; then
    echo "UNUSED: $dep"
  fi
done

# Check for deps that duplicate stdlib
# e.g., lodash.clonedeep when structuredClone exists
# e.g., axios when fetch is available
# e.g., moment when Intl.DateTimeFormat + Date work
```

## Output

One line per finding, ranked biggest cut first:

```
delete  Unused UserProfileV2 component (142 lines, 0 importers). [src/components/UserProfileV2.tsx]
delete  Feature flag ENABLE_DARK_MODE always true, never checked. [src/config.ts:14]
stdlib  Hand-rolled deepClone (28 lines). structuredClone(). [src/utils/clone.ts:1-28]
native  lodash.debounce — one call site. setTimeout + clearTimeout. [package.json, src/hooks/useSearch.ts:5]
yagni   IAuthProvider interface — one impl (JwtAuthProvider). Inline it. [src/auth/IAuthProvider.ts]
yagni   createLoggerFactory() — returns exactly one logger. Use direct constructor. [src/logging/factory.ts]
shrink  45-line switch statement — object lookup (8 lines). [src/utils/mapStatus.ts:20-65]
shrink  Repeated try/catch in 6 API handlers — extract withErrorHandler(). [src/api/*.ts]

net: -312 lines, -2 deps possible
```

Nothing to cut: `Lean already. Ship.`

## Rules

1. **Scope: over-engineering and complexity ONLY.** Correctness bugs, security issues, performance problems belong in `/code-review`, not here.
2. **A single smoke test or assert-based check is NOT bloat** — never flag minimal tests.
3. **Report only, don't apply fixes.** This is an audit, not a refactor session.
4. **Rank by impact.** Biggest cut first. A 200-line dead file matters more than a 2-line shrink.
5. **Be specific.** Name the stdlib function. Name the native API. Show the shorter form.
6. **One-shot scan.** Don't iterate — scan once, report, done.
7. **Ignore vendored/generated code.** Only audit code the team wrote.
8. **Don't flag intentional flexibility** if there's a comment explaining why (e.g., "// interface for testing" with a mock in tests).
