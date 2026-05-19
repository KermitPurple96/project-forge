# Agent Map

> The orchestrator uses this map to dispatch tasks to the right agent based on which files are involved.

## Dispatch rules

| File pattern | Primary agent | Secondary agent | Notes |
|-------------|---------------|-----------------|-------|
| `src/components/**` | frontend | qa | UI components |
| `src/features/**` | frontend | qa | Feature modules |
| `src/pages/**`, `src/app/**` | frontend | — | Routes and layouts |
| `src/hooks/**` | frontend | qa | Custom hooks |
| `src/stores/**` | frontend | — | Client-side state |
| `src/styles/**`, `src/assets/**` | frontend | — | Design assets |
| `src/types/**` | backend | frontend | Shared types — backend defines, frontend consumes |
| `src/services/**` | backend | frontend | API client layer |
| `server/routes/**` | backend | qa | API routes |
| `server/controllers/**` | backend | qa | Request handlers |
| `server/services/**` | backend | qa | Business logic |
| `server/middleware/**` | backend | — | Auth, validation, error handling |
| `server/validators/**` | backend | — | Input validation schemas |
| `prisma/**`, `db/**` | database | backend | Schema, migrations, seeds |
| `src/models/**` | database | backend | Model definitions |
| `Dockerfile`, `docker-compose*` | devops | — | Container config |
| `.dockerignore` | devops | — | Build exclusions |
| `scripts/**` | devops | — | Deployment, backup, utility scripts |
| `Makefile` | devops | — | Task runner |
| `.env.example` | devops | backend | Env var documentation |
| `nginx.conf`, `Caddyfile` | devops | — | Reverse proxy |
| `tests/**`, `**/*.test.*`, `**/*.spec.*` | qa | — | All test files |
| `e2e/**` | qa | — | End-to-end tests |
| `fixtures/**` | qa | — | Test data |
| `public/**` | frontend | — | Static assets |

## Multi-agent tasks

Some tasks require coordinated work from multiple agents. The orchestrator handles sequencing:

| Task type | Agent sequence | Why |
|-----------|---------------|-----|
| New API endpoint + UI | database → backend → frontend → qa | Schema first, then API, then UI, then tests |
| New feature (full stack) | database → backend → frontend → qa | Same flow, larger scope |
| Database schema change | database → backend → qa | Schema, update queries, update tests |
| New page/screen (no API change) | frontend → qa | UI only, test it |
| Infrastructure change | devops | Solo, verify with health check |
| Bug fix (backend) | backend → qa | Fix, then write regression test |
| Bug fix (frontend) | frontend → qa | Fix, then write regression test |

## Conflict resolution

If two agents need to modify the same file:
1. The **primary agent** (per the table above) makes the change
2. The secondary agent reviews and adjusts if needed
3. If they disagree, the orchestrator flags it to the user

Never: two agents editing the same file at the same time. Always sequential.
