# 🔀 Security Baseline

> Minimum security practices every project MUST follow from day one. Non-negotiable.

## .gitignore (mandatory entries)

Every project created with Project Forge MUST include these in `.gitignore`:

```gitignore
# Environment & secrets
.env
.env.local
.env.development
.env.staging
.env.production
.env.*.local

# Keys & certificates
*.pem
*.key
*.p12
*.pfx
*.jks
*.keystore
*.crt
*.cert

# Cloud credentials
credentials.json
service-account*.json
*-credentials.json
.aws/
.gcloud/

# IDE & OS
.idea/
.vscode/settings.json
.DS_Store
Thumbs.db

# Debug & logs
*.log
debug/
crash-reports/
```

## Pre-commit hook

Install a pre-commit secret scanner on every project. See `commands/transversal/secrets-scan.md` for the hook script.

Recommended tools (pick one):
- **Manual hook:** Script in `.git/hooks/pre-commit` (zero dependencies)
- **gitleaks:** `brew install gitleaks` → `gitleaks detect --source .`
- **trufflehog:** `brew install trufflehog` → `trufflehog filesystem .`
- **husky + custom script:** For Node.js projects

## Environment variables

Rules:
1. **Never hardcode secrets in source code** — always use env vars
2. **Always provide `.env.example`** — with placeholder values and descriptions
3. **Validate env vars at startup** — fail fast if required vars are missing
4. **Different secrets per environment** — dev, staging, and prod use different keys
5. **Rotate secrets regularly** — at minimum when team members leave

## API keys in documentation

When writing docs, commands, or templates:
- Use `<your-api-key>`, `$TOKEN`, `$API_KEY` — never paste real values
- Use `localhost` or `example.com` for URLs — never real domains
- Use `192.168.x.x` or `10.0.x.x` for IPs — never real public IPs
- Use `user@example.com` for emails — never real addresses

## Code review checklist (security section)

Before merging any PR, verify:
- [ ] No secrets in code (run `secrets-scan --staged`)
- [ ] No secrets in commit messages
- [ ] Input validation at all API boundaries
- [ ] SQL queries parameterized (no string concatenation)
- [ ] User-uploaded files validated (type, size, content)
- [ ] Auth checks on every protected endpoint
- [ ] Error messages don't expose internal details
- [ ] Logs don't contain PII or secrets
- [ ] Dependencies scanned (`npm audit` / `pip audit`)

## Incident response for leaked secrets

If a secret is committed to git:

1. **Rotate the secret immediately** (new API key, new password, new token)
2. **Revoke the old secret** at the provider (GitHub, AWS, Stripe, etc.)
3. **Remove from git history** using BFG or git filter-repo
4. **Audit access logs** at the provider for unauthorized usage
5. **Add to .gitignore** to prevent recurrence
6. **Document the incident** in the decision log
