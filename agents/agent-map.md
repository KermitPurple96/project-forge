# Agent Map

> Defines which agents are active per project type and how the orchestrator dispatches tasks.

## Project types → active agents

| Project type | Active agents | Notes |
|-------------|---------------|-------|
| **Web fullstack** | frontend, backend, database, devops, qa | The classic combo |
| **SPA + external API** | frontend, devops, qa | No backend/db — consumes third-party APIs |
| **API / backend only** | backend, database, devops, qa | Headless service, no UI |
| **CLI tool** | cli, devops, qa | Command-line interface |
| **CLI + backend** | cli, backend, database, devops, qa | CLI that talks to a server |
| **Desktop app** | desktop, frontend, devops, qa | Electron, Tauri, native |
| **Desktop + backend** | desktop, frontend, backend, database, devops, qa | Desktop app with API |
| **Mobile app** | mobile, frontend, devops, qa | React Native, Flutter, native |
| **Mobile + backend** | mobile, frontend, backend, database, devops, qa | Mobile app with API |
| **Library / package** | library, devops, qa | npm, PyPI, crate, gem |
| **Data pipeline** | data, database, devops, qa | ETL, processing, analytics |
| **Data + API** | data, backend, database, devops, qa | Pipeline with API layer |
| **Fullstack + mobile** | frontend, mobile, backend, database, devops, qa | Web + mobile sharing backend |

**devops** and **qa** are always active — every project needs building, deploying, and testing.

## Dispatch rules

The orchestrator determines the agent for a task based on:

1. **Explicit assignment** — task-breakdown.md says `Agent: frontend`
2. **File pattern match** — the task description mentions files that an agent owns
3. **Task type inference** — "add API endpoint" → backend, "create screen" → mobile/frontend

### Universal file patterns (all project types)

| File pattern | Primary agent | Secondary |
|-------------|---------------|-----------|
| `Dockerfile`, `docker-compose*` | devops | — |
| `Makefile`, `scripts/**` | devops | — |
| `.env.example` | devops | — |
| `tests/**`, `**/*.test.*`, `**/*.spec.*` | qa | — |
| `e2e/**` | qa | — |
| `README.md`, `docs/**` | library (if active), else devops | — |
| `CHANGELOG.md` | library (if active), else devops | — |

### Web patterns

| File pattern | Primary agent | Secondary |
|-------------|---------------|-----------|
| `src/components/**` | frontend | qa |
| `src/pages/**`, `src/app/**` | frontend | — |
| `src/hooks/**`, `src/stores/**` | frontend | — |
| `src/styles/**`, `src/assets/**` | frontend | — |
| `src/types/**` | backend | frontend |
| `src/services/**` | backend | frontend |
| `server/**` | backend | qa |
| `prisma/**`, `db/**` | database | backend |

### CLI patterns

| File pattern | Primary agent | Secondary |
|-------------|---------------|-----------|
| `src/cli/**`, `src/commands/**` | cli | qa |
| `src/args/**`, `src/output/**` | cli | — |
| `bin/**`, `completions/**` | cli | — |

### Desktop patterns

| File pattern | Primary agent | Secondary |
|-------------|---------------|-----------|
| `src/main/**`, `src/preload/**` | desktop | — |
| `src/ipc/**`, `src/tray/**`, `src/menu/**` | desktop | — |
| `src/platform/**` | desktop | — |
| `ios/**`, `android/**`, `resources/**` | desktop | — |

### Mobile patterns

| File pattern | Primary agent | Secondary |
|-------------|---------------|-----------|
| `src/screens/**` | mobile | qa |
| `src/navigation/**` | mobile | — |
| `src/native/**`, `src/permissions/**` | mobile | — |
| `ios/**`, `android/**` | mobile | — |

### Library patterns

| File pattern | Primary agent | Secondary |
|-------------|---------------|-----------|
| `src/lib/**`, `src/index.*` | library | qa |
| `src/errors/**`, `src/constants/**` | library | — |
| `examples/**`, `benchmarks/**` | library | — |

### Data patterns

| File pattern | Primary agent | Secondary |
|-------------|---------------|-----------|
| `src/pipelines/**` | data | qa |
| `src/extractors/**`, `src/loaders/**` | data | — |
| `src/transformers/**`, `src/validators/**` | data | — |
| `src/jobs/**`, `src/schemas/**` | data | — |

## Multi-agent task sequencing

| Task type | Sequence |
|-----------|----------|
| New web feature (fullstack) | database → backend → frontend → qa |
| New API endpoint | database → backend → qa |
| New CLI command | cli → qa |
| New screen (mobile) | mobile → frontend → qa |
| New desktop feature | desktop → frontend → qa |
| New pipeline stage | data → database → qa |
| New library function | library → qa |
| Infrastructure change | devops (solo) |
| Bug fix (any layer) | [affected agent] → qa |

## Conflict resolution

If two agents need the same file:
1. Primary agent (per table above) makes the change
2. Secondary agent reviews and adjusts
3. Conflicts → orchestrator flags to user

Never: two agents editing the same file simultaneously. Always sequential.
