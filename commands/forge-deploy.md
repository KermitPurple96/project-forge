# 🤖 Command: forge-deploy

> Handles the entire deployment pipeline: environment setup, staging, and production. Designed for developers who don't want to think about DevOps.

## Invocation

```
/forge-deploy setup            # First time: configure environments and CI/CD
/forge-deploy staging          # Deploy current state to staging
/forge-deploy production       # Deploy staging to production (requires staging first)
/forge-deploy status           # Show what's deployed where
/forge-deploy rollback         # Revert production to previous version
/forge-deploy logs             # Show recent logs from deployed environment
```

## How environments work (for users who haven't done this before)

```
Your machine (localhost)     You work here
        │
        ▼
    Staging                  A copy of production for testing.
        │                    Only you and your team see it.
        │                    Break things here, not in prod.
        ▼
    Production               The real app. Users see this.
                             Only deploy here when staging works.
```

**The rule:** Code flows in one direction. localhost → staging → production. Never skip staging.

---

## Instructions for Claude

### On `setup` (first time only)

This runs once to configure everything. Claude does the heavy lifting.

#### Step 1: Read project context

```
Read: stack-selection.md → what hosting platform?
Read: infra-architecture.md → what services are needed?
Read: integration-map.md → what env vars are needed?
Read: data-model.md → what database setup is needed?
```

#### Step 2: Detect and configure hosting

Based on `stack-selection.md`, set up the appropriate platform:

**Vercel / Netlify (static + serverless):**
```bash
# Install CLI
npm i -g vercel  # or netlify-cli

# Link project
vercel link       # Creates .vercel/project.json
vercel env pull   # Pulls env vars template

# Create environments
# Staging: preview deployments (automatic on PR/branch)
# Production: main branch deployment
```

**Railway / Render / Fly.io (containers):**
```bash
# Install CLI and login
# Create two environments: staging and production
# Configure environment variables for each
# Set up auto-deploy from git branches:
#   - staging branch → staging environment
#   - main branch → production environment
```

**AWS / GCP / Azure (cloud):**
```bash
# Generate Terraform/Pulumi config for:
# - Staging environment (smaller instances, test DB)
# - Production environment (production-sized)
# - Shared resources (DNS, CDN, secrets manager)
```

**Docker Compose (self-hosted / VPS):**
```bash
# Generate:
# - docker-compose.staging.yml
# - docker-compose.production.yml
# - deploy.sh script
# - nginx/Caddy reverse proxy config
```

#### Step 3: Database setup

```bash
# Create two databases:
# - myapp_staging (can be wiped, smaller instance)
# - myapp_production (backed up, properly sized)

# Generate connection strings for each environment
# Store in environment variables, never in code
```

#### Step 4: CI/CD pipeline

Generate GitHub Actions (or equivalent) that:

```yaml
# .github/workflows/staging.yml
# Trigger: push to 'staging' or 'develop' branch
# Steps: install → lint → test → build → deploy to staging → smoke test

# .github/workflows/production.yml
# Trigger: push to 'main' branch (or manual dispatch)
# Steps: install → lint → test → build → deploy to production → smoke test → monitor
```

#### Step 5: Environment variables

```bash
# Generate .env files for each environment:
# .env.staging    → staging API URLs, test payment keys, staging DB
# .env.production → production API URLs, live payment keys, production DB

# Configure in hosting platform (not committed to git)
# Generate .env.example with descriptions
```

#### Step 6: Domain & SSL

```
# Ask user:
# - Do you have a domain? → configure DNS
# - No domain yet? → use platform's default URL for now
# - SSL: most platforms handle this automatically (Let's Encrypt)
```

#### Step 7: Verify setup

```bash
# Checklist:
# ✅ Staging environment created
# ✅ Production environment created
# ✅ Database for each environment
# ✅ CI/CD pipeline configured
# ✅ Environment variables set
# ✅ Domain/SSL configured (or deferred)
# ✅ .env.example updated
# ✅ README updated with deploy instructions
```

**Output:** Write deployment documentation to `docs/deployment.md`

---

### On `staging`

Deploy current code to staging environment:

```
1. Pre-flight checks:
   ├── All tests pass locally? 
   ├── Build succeeds?
   ├── No uncommitted changes?
   └── On correct branch? (staging/develop)

2. Deploy:
   ├── Push to staging branch (triggers CI/CD)
   │   OR
   ├── Manual deploy via CLI:
   │   ├── vercel --env preview
   │   ├── railway up --environment staging
   │   ├── fly deploy --config fly.staging.toml
   │   └── docker compose -f docker-compose.staging.yml up -d
   │
   ├── Run database migrations on staging DB
   └── Wait for deployment to complete

3. Post-deploy verification:
   ├── Health check: GET /api/health → 200
   ├── Run smoke-tests against staging URL
   ├── Key pages load (homepage, login, dashboard)
   └── Core API endpoint responds

4. Report:
   ┌─────────────────────────────────────┐
   │ ✅ Deployed to staging              │
   │ URL: https://staging.myapp.com      │
   │ Version: v1.2.0 (abc123f)           │
   │ Smoke tests: 6/6 passed             │
   │                                     │
   │ Ready for /forge-deploy production  │
   └─────────────────────────────────────┘
```

---

### On `production`

Deploy from staging to production:

```
1. Pre-flight checks:
   ├── Staging deploy exists and is recent?
   ├── Staging smoke tests passed?
   ├── Pre-launch checklist complete? (if first deploy)
   ├── Database migrations reviewed?
   └── ⚠️ CONFIRM: "Deploy v1.2.0 to production? (y/n)"

2. Backup:
   ├── Backup production database
   ├── Note current production version (for rollback)
   └── Save rollback point

3. Deploy:
   ├── Merge staging → main (or push to main)
   │   OR
   ├── Manual deploy via CLI
   ├── Run database migrations on production DB
   └── Wait for deployment to complete

4. Post-deploy monitoring (5 minutes):
   ├── Health check every 30 seconds
   ├── Monitor error rate (should stay < 1%)
   ├── Monitor response times (should stay < 2s)
   ├── Check key user flows still work
   └── If anomaly detected → AUTO-ROLLBACK

5. Report:
   ┌─────────────────────────────────────┐
   │ ✅ Deployed to production           │
   │ URL: https://myapp.com              │
   │ Version: v1.2.0 (abc123f)           │
   │ Monitoring: 5 min clean ✅          │
   │ Previous version: v1.1.0 (saved)    │
   └─────────────────────────────────────┘
```

---

### On `rollback`

```
1. Identify previous version from git tags
2. ⚠️ CONFIRM: "Rollback production from v1.2.0 to v1.1.0? (y/n)"
3. Deploy previous version
4. Rollback database migration (if safe — no data loss)
   └── If migration can't be rolled back → warn user, keep new schema
5. Verify health check passes
6. Run smoke tests
7. Report status
```

---

### On `status`

```
## Deployment Status

| Environment | Version | Deployed | Health | URL |
|-------------|---------|----------|--------|-----|
| Staging | v1.2.0 (abc123f) | 2h ago | ✅ healthy | staging.myapp.com |
| Production | v1.1.0 (def456a) | 3d ago | ✅ healthy | myapp.com |

### Recent deployments
| When | Environment | Version | Status |
|------|-------------|---------|--------|
| 2h ago | Staging | v1.2.0 | ✅ success |
| 3d ago | Production | v1.1.0 | ✅ success |
| 4d ago | Staging | v1.1.0 | ✅ success |
```

---

## Integration with the orchestrator

The orchestrator calls `forge-deploy` automatically at milestone boundaries:

```
Milestone complete
  → orchestrator runs security-review, performance-audit
  → if all pass → orchestrator calls forge-deploy staging
  → orchestrator notifies user: "MVP deployed to staging. Review at [URL]"
  → user reviews staging
  → user runs: /forge-deploy production
```

Production deploys are ALWAYS manual confirmation. The orchestrator never auto-deploys to production.

## Rules

1. **Staging before production** — always. No exceptions.
2. **Production needs confirmation** — never auto-deploy to production.
3. **Backup before deploy** — always backup the database before production deploys.
4. **Monitor after deploy** — watch error rates for 5 minutes minimum.
5. **Rollback is fast** — if anything looks wrong, rollback first, investigate later.
6. **Environment parity** — staging should mirror production as closely as possible.
7. **Secrets stay out of git** — environment variables only, never committed.
