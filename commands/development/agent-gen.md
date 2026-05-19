# 🤖 Command: agent-gen

> Generates project-specific agents based on the selected tech stack. Each agent is an expert in one layer of the architecture.

## Invocation

```
/agent-gen                     # Generate all agents from stack-selection.md
/agent-gen --layer frontend    # Regenerate only the frontend agent
/agent-gen --list              # List current agents and their scope
/agent-gen --update            # Update agents after stack changes
```

## Instructions for Claude

### Step 1: Read project context

1. Read `stack-selection.md` — determines which agents to create
2. Read `project-structure.md` — determines file paths and conventions
3. Read `design-tokens.md` — frontend agent needs this
4. Read `data-model.md` — database agent needs this
5. Read `api-contract.md` — backend agent needs this
6. Read `error-handling-strategy.md` — all agents need this
7. Read `testing-strategy.md` — all agents need this

### Step 2: Determine which agents to generate

Map the stack to agents:

| Stack layer | Agent created | When |
|-------------|--------------|------|
| Frontend framework (React, Vue, Svelte, etc.) | `frontend-agent` | Always if frontend exists |
| Backend framework (Express, Django, FastAPI, etc.) | `backend-agent` | Always if backend exists |
| Database (PostgreSQL, MongoDB, etc.) | `database-agent` | Always if database exists |
| Infrastructure (Docker, CI/CD, cloud) | `infra-agent` | If Docker or cloud config exists |
| AI/ML (agents, LLMs, pipelines) | `ai-agent` | Only if ai-agents-design.md is filled |
| Mobile (React Native, Flutter) | `mobile-agent` | Only if mobile framework in stack |

### Step 3: Generate agent files

Create agents in `.forge/agents/` directory. Each agent file defines:

```markdown
# Agent: [name]

## Identity
- **Role:** Expert in [layer]
- **Stack:** [specific tech]
- **Scope:** Files matching [glob patterns]

## Scope rules
- Files I own (I create and modify these)
- Files I read (I need context from these but don't modify them)
- Files I never touch

## Conventions
- [From project-structure.md, specific to this layer]

## Patterns
- [Common patterns used in this layer]

## Quality checklist
- [What this agent verifies after implementing]
```

### Agent templates

---

#### frontend-agent.md

```markdown
# Agent: Frontend

## Identity
- **Role:** Frontend developer specializing in {{framework}}
- **Stack:** {{framework}} + {{styling}} + {{state_management}}
- **Scope:** `{{frontend_dirs}}`

## Scope rules

### Files I own
- `src/components/**` — UI components
- `src/features/**` — feature modules
- `src/hooks/**` — custom hooks
- `src/stores/**` — state management
- `src/styles/**` — global styles
- `src/app/**` or `src/pages/**` — routes and layouts

### Files I read (don't modify)
- `src/types/**` — shared type definitions
- `src/services/**` — API client (backend-agent owns this)
- `{{design_tokens_path}}` — design tokens

### Files I never touch
- Server/backend code
- Database schemas/migrations
- CI/CD configs
- Docker files

## Conventions
- Components: PascalCase, one component per file
- Hooks: `use` prefix, camelCase
- State: {{state_management}} patterns
- Styling: {{styling}} approach
- Imports: {{import_convention}}

## Implementation checklist
After implementing any task, verify:
- [ ] Component renders without errors
- [ ] All 5 states handled: loading, empty, success, error, edge case
- [ ] Responsive on defined breakpoints
- [ ] Keyboard navigable
- [ ] Types complete (no `any`)
- [ ] No console.log or debug code left
- [ ] New routes registered in router
- [ ] New options added to relevant dropdowns/menus

## Testing
- Unit: {{test_framework}} for logic and hooks
- Component: render tests with user interactions
- Location: `__tests__/` alongside source or `tests/` directory
```

---

#### backend-agent.md

```markdown
# Agent: Backend

## Identity
- **Role:** Backend developer specializing in {{framework}}
- **Stack:** {{language}} + {{framework}} + {{api_style}}
- **Scope:** `{{backend_dirs}}`

## Scope rules

### Files I own
- `server/routes/**` or `src/routes/**` — API routes
- `server/controllers/**` — request handlers
- `server/services/**` — business logic
- `server/middleware/**` — auth, validation, error handling
- `src/services/**` — API client definitions (shared with frontend)
- `src/types/**` — shared type definitions

### Files I read (don't modify)
- Database models/schema (database-agent owns this)
- Frontend components
- Infrastructure configs

### Files I never touch
- UI components
- CSS/styles
- Docker/CI configs (unless adding env vars)

## Conventions
- Routes: RESTful, {{api_style}}
- Error handling: {{error_strategy}}
- Validation: at route boundary, before handler
- Auth: middleware-based, check on every protected route
- Naming: {{naming_convention}}

## Implementation checklist
After implementing any task, verify:
- [ ] Endpoint responds correctly (2xx for valid, 4xx for invalid)
- [ ] Input validated at boundary
- [ ] Auth/authz enforced
- [ ] Error responses follow standard format
- [ ] No sensitive data in responses (passwords, internal IDs)
- [ ] Types match frontend expectations
- [ ] Database queries parameterized
- [ ] Resources cleaned up (connections, file handles)

## Testing
- Unit: business logic, validators, transformers
- Integration: endpoint tests with real/test DB
- Location: alongside source (`*.test.*`) or `tests/` directory
```

---

#### database-agent.md

```markdown
# Agent: Database

## Identity
- **Role:** Database engineer specializing in {{database}}
- **Stack:** {{database}} + {{orm}}
- **Scope:** `{{db_dirs}}`

## Scope rules

### Files I own
- `prisma/schema.prisma` or `db/schema.*` — schema definition
- `prisma/migrations/` or `db/migrations/` — migration files
- `prisma/seed.*` or `db/seed.*` — seed data
- `src/models/**` — model definitions (if not using ORM schema)

### Files I read (don't modify)
- `data-model.md` — source of truth for entities
- Backend services (to understand query patterns)

### Files I never touch
- Frontend code
- API routes
- Infrastructure configs

## Conventions
- Table names: {{table_naming}} (snake_case / PascalCase)
- Column names: {{column_naming}}
- Always add created_at, updated_at
- Always use UUID or auto-increment for PKs (project decision)
- Foreign keys named: `{referenced_table}_id`
- Indexes on all foreign keys and frequent query fields

## Implementation checklist
After implementing any task, verify:
- [ ] Migration runs forward without error
- [ ] Migration rolls back without error
- [ ] No data loss in migration (if modifying existing tables)
- [ ] Seed data updated to include new entities
- [ ] Indexes added for query patterns
- [ ] Constraints enforce business rules (unique, not null, check)
- [ ] Types in ORM match data-model.md

## Testing
- Migration: apply and rollback cleanly
- Seed: produces valid, realistic data
- Queries: return expected results with test data
```

---

#### infra-agent.md

```markdown
# Agent: Infrastructure

## Identity
- **Role:** DevOps/infrastructure engineer
- **Stack:** {{hosting}} + {{ci_cd}} + {{containerization}}
- **Scope:** project root configs, CI/CD, Docker

## Scope rules

### Files I own
- `Dockerfile`, `docker-compose*.yml`, `.dockerignore`
- `.github/workflows/` or `.gitlab-ci.yml`
- `scripts/` — deployment, backup, utility scripts
- `.env.example` — environment variable documentation
- `nginx.conf`, `Caddyfile` — reverse proxy configs

### Files I read (don't modify)
- `package.json` / `go.mod` / `requirements.txt` — dependency info
- `stack-selection.md` — infrastructure decisions
- Backend code (to understand health check endpoints)

### Files I never touch
- Source code (components, routes, models)
- Database schemas
- Test files

## Conventions
- Docker: multi-stage builds, non-root user, health checks
- CI: fail fast, cache dependencies, parallel jobs
- Scripts: bash with error handling (`set -euo pipefail`)
- Secrets: NEVER in code, always env vars or secrets manager

## Implementation checklist
After implementing any task, verify:
- [ ] Docker builds successfully
- [ ] Docker compose starts all services
- [ ] CI pipeline passes
- [ ] Health check endpoint responds
- [ ] Environment variables documented in .env.example
- [ ] No secrets in any committed file
- [ ] Scripts have proper error handling
```

### Step 4: Write agents to project

```bash
mkdir -p .forge/agents
# Write each agent file
# e.g. .forge/agents/frontend-agent.md
#      .forge/agents/backend-agent.md
#      .forge/agents/database-agent.md
#      .forge/agents/infra-agent.md
```

### Step 5: Generate orchestrator agent map

Create `.forge/agents/agent-map.md`:

```markdown
# Agent Map

| File pattern | Primary agent | Secondary agent |
|-------------|---------------|-----------------|
| src/components/** | frontend-agent | — |
| src/features/** | frontend-agent | — |
| src/services/** | backend-agent | frontend-agent |
| src/types/** | backend-agent | frontend-agent |
| server/** | backend-agent | — |
| prisma/** | database-agent | backend-agent |
| db/** | database-agent | backend-agent |
| Dockerfile | infra-agent | — |
| .github/** | infra-agent | — |
| scripts/** | infra-agent | — |
```

This map tells the orchestrator which agent(s) to dispatch for each task based on the files involved.

### Step 6: Verify

1. List all generated agents
2. Verify scope rules don't have gaps (every project file should be owned by an agent)
3. Verify scope rules don't have overlaps (no two agents own the same files)
4. Report the agent roster to the user

## Output format

```
## Agents Generated

| Agent | Stack | Files owned | Status |
|-------|-------|-------------|--------|
| frontend-agent | React + Tailwind + Zustand | 42 files | ✅ Created |
| backend-agent | Node.js + Express + REST | 28 files | ✅ Created |
| database-agent | PostgreSQL + Prisma | 8 files | ✅ Created |
| infra-agent | Docker + GitHub Actions | 6 files | ✅ Created |

Agent map written to `.forge/agents/agent-map.md`
Orchestrator ready — run `/orchestrator` to start.
```

## Rules

1. **One owner per file** — no ambiguity about who modifies what
2. **Read access is broad** — agents should understand context beyond their scope
3. **Agents follow project conventions** — they read project-structure.md and apply it
4. **Agents are regenerated when stack changes** — if you add Redis, rerun agent-gen
5. **Agent quality checklists are mandatory** — the orchestrator runs them after every task
