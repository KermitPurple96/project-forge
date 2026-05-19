# 🤖 Command: deploy

> Automated deployment pipeline. Handles environment setup, infrastructure generation, deploy execution, and post-deploy validation — from dev to production.

## Invocation

```
/deploy staging                # Deploy to staging
/deploy production             # Deploy to production (requires staging first)
/deploy --setup                # First-time setup: generate all infra configs
/deploy --status               # Show deployment status of all environments
/deploy --rollback staging     # Rollback staging to previous version
/deploy --rollback production  # Rollback production to previous version
/deploy --logs staging         # Show recent deployment logs
```

---

## How AI handles deployments

The AI can't SSH into servers or access cloud consoles directly. Instead, it:

1. **Generates all configuration files** — Dockerfiles, CI/CD pipelines, deploy scripts, infra-as-code
2. **Runs local validation** — builds, tests, security scans before deploy
3. **Executes deploy via CLI tools** — most platforms have CLI tools (Vercel, Railway, Fly.io, AWS CLI, etc.)
4. **Verifies after deploy** — hits the health endpoint, runs smoke tests, checks error rates
5. **Guides manual steps** — when something needs the cloud console, gives exact instructions with screenshots descriptions

---

## First-time setup (`/deploy --setup`)

### Step 1: Read architecture

```
Read:
- stack-selection.md → hosting platform
- infra-architecture.md → what components exist
- integration-map.md → external services needed
- security-baseline.md → security requirements
```

### Step 2: Generate infrastructure files

Based on the hosting platform, generate the appropriate configs:

#### For Vercel (frontend)
```
vercel.json              — Build and route configuration
.env.production          — Production env vars (template)
```

#### For Railway / Render / Fly.io (backend)
```
Dockerfile               — Multi-stage production build
docker-compose.prod.yml  — Production compose (if self-hosted)
fly.toml                 — Fly.io config (if Fly.io)
railway.toml             — Railway config (if Railway)
render.yaml              — Render blueprint (if Render)
```

#### For AWS (full)
```
infrastructure/
├── terraform/           — Or CloudFormation/Pulumi
│   ├── main.tf
│   ├── variables.tf
│   └── outputs.tf
├── ecs-task-def.json    — If using ECS
└── buildspec.yml        — If using CodeBuild
```

#### For any platform
```
.github/workflows/
├── ci.yml               — Build + test on every PR
├── deploy-staging.yml   — Auto-deploy to staging on merge to main
└── deploy-prod.yml      — Manual trigger deploy to production
scripts/
├── deploy.sh            — Universal deploy script
├── rollback.sh          — Rollback script
├── smoke-test.sh        — Post-deploy validation
└── health-check.sh      — Health check script
```

### Step 3: Environment configuration

Generate environment files for each environment:

```bash
# Create env templates with descriptions
cat > .env.staging << 'ENV'
# === App ===
NODE_ENV=staging
APP_URL=https://staging.yourapp.com
PORT=3000

# === Database ===
DATABASE_URL=postgresql://user:pass@host:5432/dbname_staging

# === Auth ===
JWT_SECRET=           # Generate: openssl rand -hex 32
JWT_EXPIRES_IN=15m

# === External services ===
# STRIPE_SECRET_KEY=  # Get from: https://dashboard.stripe.com/test/apikeys
# RESEND_API_KEY=     # Get from: https://resend.com/api-keys

# === Monitoring ===
# SENTRY_DSN=         # Get from: https://sentry.io/settings/projects/
ENV
```

Tell the user exactly where to get each value:
```
"You need to fill in these values:
1. DATABASE_URL → Go to [hosting provider] dashboard → your database → connection string
2. JWT_SECRET → I'll generate this for you: [generate]
3. STRIPE_SECRET_KEY → Stripe Dashboard → Developers → API Keys → use test key for staging
..."
```

### Step 4: Database setup

```bash
# Generate migration for production
npx prisma migrate deploy    # or equivalent
# Generate seed for staging (not production)
npx prisma db seed           # staging only
```

### Step 5: CI/CD pipeline

Generate GitHub Actions (or equivalent) workflow:

```yaml
# .github/workflows/deploy-staging.yml
name: Deploy to Staging

on:
  push:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm ci
      - run: npm test
      - run: npm run build

  deploy:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      # Platform-specific deploy steps
      - run: ./scripts/deploy.sh staging
      - run: ./scripts/smoke-test.sh $STAGING_URL
```

### Step 6: Verify setup

```
"Infrastructure setup complete. Here's what I created:
- Dockerfile (optimized production build)
- CI/CD pipeline (auto-deploy to staging on merge)
- Deploy scripts (deploy.sh, rollback.sh, smoke-test.sh)
- Environment templates (.env.staging, .env.production)

Before first deploy, you need to:
1. Create accounts on [platform] (if not done)
2. Fill in the environment variables
3. Connect your GitHub repo to [platform]

Want me to walk you through each step?"
```

---

## Deploy to staging (`/deploy staging`)

### Pre-deploy checklist (automated)

```
┌─────────────────────────────────────────────┐
│          PRE-DEPLOY VALIDATION              │
│                                             │
│  1. Build passes locally               [?] │
│  2. All tests pass                      [?] │
│  3. Security scan clean                 [?] │
│  4. Secrets scan clean                  [?] │
│  5. No TODO/FIXME blocking deploy       [?] │
│  6. Database migrations ready           [?] │
│  7. Environment variables set           [?] │
│                                             │
│  Must be ALL PASS to proceed                │
└─────────────────────────────────────────────┘
```

Claude runs each check. If any fails, fix before proceeding.

### Deploy execution

```bash
# Option A: Platform CLI (preferred)
vercel deploy --env staging          # Vercel
railway up --environment staging     # Railway
fly deploy --config fly.staging.toml # Fly.io

# Option B: Docker-based
docker build -t myapp:staging .
docker push registry/myapp:staging
# Trigger deploy via platform API or CLI

# Option C: Git-based (push triggers CI/CD)
git tag staging-$(date +%Y%m%d-%H%M%S)
git push origin --tags
# CI/CD pipeline handles the rest
```

### Post-deploy validation

```bash
# Wait for deployment to be live (poll health endpoint)
echo "Waiting for staging to be healthy..."
for i in $(seq 1 30); do
  STATUS=$(curl -s -o /dev/null -w "%{http_code}" https://staging.yourapp.com/api/health)
  if [ "$STATUS" = "200" ]; then
    echo "✅ Staging is healthy"
    break
  fi
  sleep 10
done

# Run smoke tests
./scripts/smoke-test.sh https://staging.yourapp.com

# Run e2e tests against staging
STAGING_URL=https://staging.yourapp.com npx playwright test
```

### Report

```
## Deploy to Staging — Complete ✅

| Check | Status |
|-------|--------|
| Build | PASS |
| Tests | 47/47 PASS |
| Security scan | CLEAN |
| Deploy | SUCCESS |
| Health check | 200 OK (1.2s) |
| Smoke tests | 8/8 PASS |
| E2E tests | 12/12 PASS |

URL: https://staging.yourapp.com
Version: v1.2.0 (abc123f)
Time: 3m 42s

Ready for production? Run /deploy production
```

---

## Deploy to production (`/deploy production`)

### Gate: Staging must be deployed and validated first

```
If staging not deployed:
  "You need to deploy and validate staging first. Run /deploy staging"
  STOP.

If staging has unresolved issues:
  "Staging has [N] issues. Fix them before deploying to production."
  STOP.
```

### Production-specific checks

Everything from staging, PLUS:

```
┌─────────────────────────────────────────────┐
│        PRODUCTION GATE                      │
│                                             │
│  1. Staging deployed and validated      [?] │
│  2. Pre-launch checklist complete       [?] │
│  3. Database backup taken               [?] │
│  4. Rollback plan verified              [?] │
│  5. Monitoring configured               [?] │
│  6. Error tracking configured           [?] │
│  7. DNS configured                      [?] │
│  8. SSL certificate valid               [?] │
│                                             │
│  ⚠️  USER MUST CONFIRM:                    │
│  "Type 'deploy to production' to proceed"   │
└─────────────────────────────────────────────┘
```

### Deploy execution

Same as staging but targeting production environment.

**Always take a database backup before production deploy:**
```bash
# Backup current production DB
pg_dump $DATABASE_URL > backup-$(date +%Y%m%d-%H%M%S).sql
# Store in safe location (S3, GCS, etc.)
```

### Post-deploy monitoring

```bash
# Monitor for 5 minutes after deploy
echo "Monitoring production for 5 minutes..."
for i in $(seq 1 30); do
  # Check health
  HEALTH=$(curl -s https://yourapp.com/api/health | jq -r '.status')
  # Check error rate (via monitoring API)
  # Check response time
  echo "[$(date +%H:%M:%S)] health=$HEALTH"
  sleep 10
done
```

If error rate spikes or health check fails:
```
"⚠️ Error rate increased after deploy. Auto-rolling back..."
→ Execute rollback
→ Notify user
```

### Report

```
## Deploy to Production — Complete ✅

| Check | Status |
|-------|--------|
| Pre-deploy | ALL PASS |
| Database backup | ✅ backup-20260519-143022.sql |
| Deploy | SUCCESS |
| Health check | 200 OK (0.8s) |
| Smoke tests | 8/8 PASS |
| Error rate (5min) | 0.02% (normal) |
| Response time (p95) | 180ms |

URL: https://yourapp.com
Version: v1.2.0 (abc123f)
Previous: v1.1.0 (def456a)

Rollback available: /deploy --rollback production
```

---

## Rollback (`/deploy --rollback`)

```bash
# Identify previous version
PREV_VERSION=$(git describe --tags --abbrev=0 HEAD~1)

# Rollback application
vercel rollback                      # Vercel
railway rollback                     # Railway
fly releases rollback                # Fly.io

# Rollback database (if migration was destructive)
# ⚠️ Only if the migration included a rollback script
npx prisma migrate resolve --rolled-back $MIGRATION_NAME

# Verify rollback
./scripts/smoke-test.sh $PROD_URL

# Report
echo "Rolled back from v1.2.0 to $PREV_VERSION"
```

---

## Platform-specific guides

The AI adapts deploy commands to the hosting platform. Cheat sheet:

| Platform | Deploy command | Rollback | Logs |
|----------|---------------|----------|------|
| Vercel | `vercel --prod` | `vercel rollback` | `vercel logs` |
| Railway | `railway up` | `railway rollback` | `railway logs` |
| Fly.io | `fly deploy` | `fly releases rollback` | `fly logs` |
| Render | git push (auto) | Render dashboard | Render dashboard |
| AWS ECS | `aws ecs update-service` | Previous task def | `aws logs` |
| Heroku | `git push heroku main` | `heroku rollback` | `heroku logs` |
| Netlify | `netlify deploy --prod` | Netlify dashboard | `netlify logs` |
| Self-hosted | `docker compose up -d` | `docker compose down && previous` | `docker logs` |

---

## Rules

1. **Staging before production** — ALWAYS. No exceptions.
2. **Backup before production deploy** — ALWAYS. Non-negotiable.
3. **User confirms production** — never auto-deploy to production without explicit approval.
4. **Monitor after deploy** — 5 minutes minimum, watching error rates.
5. **Fast rollback** — if anything looks wrong, rollback first, investigate later.
6. **Infrastructure as code** — every config is version controlled, no manual console changes.
7. **Secrets never in code** — all secrets via env vars or secrets manager.
8. **Guide, don't assume** — if a step requires the cloud console, give exact instructions.
