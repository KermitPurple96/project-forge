# 🤖 Command: secrets-scan

> Scans the entire codebase and git history for leaked secrets, API keys, tokens, IPs, and credentials.

## When to use

- **Before every commit** (ideally as a pre-commit hook)
- Before pushing to a remote repository
- Before making a repo public
- During security review
- When onboarding a new team member who may have committed secrets

## Invocation

```
/secrets-scan                  # Full scan: codebase + git history
/secrets-scan --staged         # Only staged files (pre-commit)
/secrets-scan --history        # Only git history (deep scan)
/secrets-scan --fix            # Scan + help remediate found secrets
```

## Instructions for Claude

### Step 1: Scan current codebase

```bash
# High-confidence patterns (real secrets, not variable names)
echo "=== API Keys & Tokens ==="
grep -rnE "(ghp_[a-zA-Z0-9]{36}|github_pat_[a-zA-Z0-9_]{82}|sk-[a-zA-Z0-9]{48}|AIza[a-zA-Z0-9_-]{35}|xox[baprs]-[a-zA-Z0-9-]{10,}|AKIA[A-Z0-9]{16}|sq0[a-z]{3}-[a-zA-Z0-9_-]{22,})" \
  --include="*.{ts,tsx,js,jsx,go,py,rs,java,rb,php,sh,yaml,yml,json,toml,env,cfg,conf,ini}" . \
  | grep -v node_modules | grep -v ".git/"

echo "=== Private keys ==="
grep -rnl "BEGIN.*PRIVATE KEY" --include="*.{pem,key,p12,pfx,ts,js,go,py,env}" . | grep -v node_modules

echo "=== Hardcoded passwords ==="
grep -rnE "(password|passwd|pwd)\s*[:=]\s*['\"][^'\"]{4,}['\"]" \
  --include="*.{ts,tsx,js,jsx,go,py,rs,yaml,yml,json,toml,env}" . \
  | grep -v node_modules | grep -v "test" | grep -v "example" | grep -v "placeholder"

echo "=== Connection strings ==="
grep -rnE "(mongodb(\+srv)?://|postgres(ql)?://|mysql://|redis://|amqp://)[^\s'\"]+" \
  --include="*.{ts,tsx,js,jsx,go,py,yaml,yml,json,toml,env}" . \
  | grep -v node_modules | grep -v "localhost" | grep -v "example"

echo "=== Hardcoded IPs (non-local) ==="
grep -rnE "\b([0-9]{1,3}\.){3}[0-9]{1,3}\b" \
  --include="*.{ts,tsx,js,jsx,go,py,rs,yaml,yml,json,toml,env,sh}" . \
  | grep -v node_modules | grep -v "127.0.0.1" | grep -v "0.0.0.0" | grep -v "localhost" \
  | grep -v "192.168." | grep -v "10.0." | grep -v "172.1[6-9]\." | grep -v "172.2[0-9]\." | grep -v "172.3[01]\." \
  | grep -v "255.255" | grep -v "test" | grep -v ".git/"

echo "=== .env files tracked by git ==="
git ls-files | grep -E "^\.env$|^\.env\.(local|production|staging)$"

echo "=== AWS/Cloud credentials ==="
grep -rnE "(aws_access_key_id|aws_secret_access_key|AZURE_CLIENT_SECRET|GOOGLE_APPLICATION_CREDENTIALS)\s*[:=]" \
  --include="*.{ts,tsx,js,jsx,go,py,yaml,yml,json,toml,env,sh}" . \
  | grep -v node_modules | grep -v "example" | grep -v ".git/"
```

### Step 2: Scan git history

```bash
# Check if secrets were ever committed (even if removed later)
echo "=== Scanning git history for secrets ==="

# Check all commits for high-value patterns
git log --all --diff-filter=A -p -- "*.env" "*.pem" "*.key" "*.p12" 2>/dev/null | head -100

# Look for removed .env files (committed then deleted = still in history)
git log --all --diff-filter=D --name-only -- "*.env*" 2>/dev/null

# Search history for token patterns
git log --all -p -S "ghp_" --format="%H %an %ae %s" 2>/dev/null | head -20
git log --all -p -S "sk-" --format="%H %an %ae %s" 2>/dev/null | head -20
git log --all -p -S "AKIA" --format="%H %an %ae %s" 2>/dev/null | head -20
git log --all -p -S "BEGIN PRIVATE KEY" --format="%H %an %ae %s" 2>/dev/null | head -20
```

### Step 3: Verify .gitignore protections

```bash
echo "=== .gitignore audit ==="
# These patterns MUST be in .gitignore
REQUIRED_PATTERNS=(".env" ".env.local" ".env.production" "*.pem" "*.key" "*.p12" "*.pfx" "*.jks" "*.keystore")

for pattern in "${REQUIRED_PATTERNS[@]}"; do
  if grep -qF "$pattern" .gitignore 2>/dev/null; then
    echo "✅ $pattern"
  else
    echo "❌ MISSING: $pattern"
  fi
done
```

### Step 4: Report

```
## Secrets Scan Results

| Category | Found | Severity | Details |
|----------|-------|----------|---------|
| API keys/tokens | 0 | CRITICAL | — |
| Private keys | 0 | CRITICAL | — |
| Hardcoded passwords | 0 | CRITICAL | — |
| Connection strings | 0 | HIGH | — |
| Hardcoded IPs | 0 | MEDIUM | — |
| .env in git | 0 | CRITICAL | — |
| .gitignore gaps | 0 | HIGH | — |
| History leaks | 0 | CRITICAL | — |

### Status: CLEAN ✅ / LEAKS FOUND ❌
```

### Step 5: Remediate (if `--fix`)

If secrets are found:

1. **Rotate immediately** — the secret is compromised the moment it hits git
2. **Remove from code** — move to environment variable
3. **Remove from history** (if committed):
   ```bash
   # Option A: BFG Repo Cleaner (recommended)
   bfg --replace-text passwords.txt repo.git
   
   # Option B: git filter-repo
   git filter-repo --invert-paths --path .env.production
   ```
4. **Force push** (coordinate with team)
5. **Add to .gitignore** to prevent recurrence
6. **Set up pre-commit hook** (see below)

## Pre-commit hook setup

Add this to `.git/hooks/pre-commit` (or use with husky/lefthook):

```bash
#!/bin/bash
# Scan staged files for secrets before committing

PATTERNS="(ghp_[a-zA-Z0-9]{36}|github_pat_|sk-[a-zA-Z0-9]{48}|AIza[a-zA-Z0-9_-]{35}|AKIA[A-Z0-9]{16}|BEGIN.*PRIVATE KEY)"

STAGED_FILES=$(git diff --cached --name-only --diff-filter=ACM)

if [ -z "$STAGED_FILES" ]; then
  exit 0
fi

FOUND=$(echo "$STAGED_FILES" | xargs grep -lE "$PATTERNS" 2>/dev/null)

if [ -n "$FOUND" ]; then
  echo "❌ SECRETS DETECTED in staged files:"
  echo "$FOUND"
  echo ""
  echo "Remove the secrets and try again."
  echo "If this is a false positive, use: git commit --no-verify"
  exit 1
fi

exit 0
```

## Rules

1. **Any secret in code = CRITICAL** — no exceptions, no "it's just for testing"
2. **Rotate first, clean later** — assume the secret is compromised
3. **Git history is permanent** — deleting a file doesn't remove it from history
4. **localhost is fine** — local IPs and placeholder tokens are not findings
5. **Test files get a pass** — mock tokens in test fixtures are acceptable if clearly fake
6. **.env.example is fine** — placeholder values for documentation are expected
