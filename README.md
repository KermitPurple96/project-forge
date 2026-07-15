# Project Forge

A framework to build any software project from idea to production using Claude Code.

**Project Forge** gives you structured commands, templates, and agents that cover the entire software development lifecycle — from "I have an idea" to "it's deployed and monitored." You talk, Claude builds.

## The Process

```
  You have an idea
        │
        ▼
  ┌─────────────┐     "What's the purpose? Who's it for?
  │  forge-init  │      What should it feel like?"
  │  (30-60 min) │     Claude asks, researches, generates:
  └──────┬───────┘     → templates, agents, styles.html, roadmap
         │
         ▼
  ┌─────────────┐     Sprint-by-sprint execution:
  │  forge-dev   │     research → implement → test → commit → demo
  │  (hours-days)│     "continue" is all you need to say
  └──────┬───────┘
         │
         ▼
  ┌─────────────┐
  │ forge-deploy │     Staging → production with one command
  │  (minutes)   │
  └──────────────┘
```

## How forge-init works

An interactive wizard that walks you through 7 conversations. Each one fills templates automatically.

**Conversation 1 — Purpose & Users** (not "features" — purpose first)
> "Before we talk about features or tech — what's the core purpose? If this app existed perfectly tomorrow, what would change for the people using it?"

Claude probes the human need, researches competitors, identifies differentiation.

**Conversation 2 — Requirements & Flows**
> "Based on your vision, here are the core features I think you need for MVP. What would you add, remove, or change?"

Claude proposes features, data model, pages, user flows. You adjust.

**Conversation 3 — Design & Identity**
> "What vibe should your app have?"

Claude generates a **`styles.html`** reference page — a standalone file showing your color palette, typography, buttons, forms, cards, layouts. Opens it in your browser. You iterate until it looks right. No app code written yet.

**Conversation 4 — Architecture & Stack**

If you know what you want, say so. If not, Claude does **deep research**:
> "I researched 4 options for your backend. Here's the comparison..."

Comparison table with maturity, performance, ecosystem fit, budget compatibility. Then a clear recommendation with reasoning.

**Conversation 5 — Planning & Sprints**

Claude generates a full roadmap with sprint specs, task breakdowns, research items, demo checkpoints, and risk register. Each sprint has a "show the user what shipped" checkpoint.

**Conversations 6-7 — Agent generation & kickoff**

Claude generates project-specific AI agents (frontend, backend, database, infra) tuned to your stack, then presents a project summary for final approval.

## How development works

After init, you just say "continue":

```
/forge-dev              → picks up next task, implements, tests, commits
/forge-dev status       → shows sprint progress, what's next
/forge-dev fix "bug"    → quick fix → test → commit
/forge-dev change "X"   → modify something already built
```

At the end of each sprint, Claude opens the app in your browser and walks through a demo. You verify before moving on.

### The implementation flow

When building a feature (via `/implement`), Claude follows this process:

```
  ┌───────────────────┐
  │  Impact Analysis   │  What files change? What endpoints? What DB changes?
  │  (before any code) │  User reviews and approves the plan
  └────────┬──────────┘
           │
  ┌────────▼──────────┐
  │  Backend first     │  Database → API → business logic
  ├───────────────────┤
  │  Frontend second   │  Pages → components → state → API calls
  ├───────────────────┤
  │  Tests             │  Unit tests → integration tests
  ├───────────────────┤
  │  Verify in browser │  Open the feature, check it works
  └───────────────────┘
```

Every step gets its own commit. Blockers are logged, not silently skipped.

## All commands

### Core (the 3 you need)

| Command | What it does |
|---------|-------------|
| `/forge-init` | Interactive wizard → fills all templates, generates agents, creates styles.html |
| `/forge-dev` | Development autopilot — "continue" drives the next task |
| `/forge-deploy` | Staging → production with validation gates |

### Development

| Command | What it does |
|---------|-------------|
| `/orchestrator` | Sprint execution engine — manages task dispatch and agent coordination |
| `/implement "feature"` | Full-stack impact analysis → guided implementation → verification |
| `/deep-research "topic"` | Research technologies, compare options, produce recommendation |
| `/roadmap` | Create/track sprint roadmap with specs, blockers, and demo checkpoints |
| `/scaffold` | Generate initial project boilerplate from architecture decisions |
| `/agent-gen` | Generate stack-specific AI agents |
| `/db-migrate` | Database schema migrations |
| `/seed-data` | Generate realistic test data |
| `/docs-gen` | Auto-generate documentation from code |
| `/changelog` | Generate changelog from git history |
| `/version-bump` | Semantic versioning + tag + release |
| `/context-init` | Load project context into a new session |

### Quality

| Command | What it does |
|---------|-------------|
| `/code-review` | Parallel review agents — security, bloat detection, integrity audit |
| `/e2e-test` | Playwright browser tests — real UI interaction, not curl |
| `/bloat-audit` | Whole-repo scan for over-engineering (delete/stdlib/native/yagni/shrink) |
| `/tech-debt` | Scan `// debt:` comments into a tracked ledger with upgrade triggers |
| `/unit-tests` | Generate and run unit tests for changed code |
| `/integration-tests` | Cross-service integration verification |
| `/security-review` | OWASP top 10, secrets scan, dependency vulnerabilities |
| `/performance-audit` | N+1 queries, bundle size, load times, memory leaks |
| `/dependency-audit` | Outdated/vulnerable dependencies |

### Staging & Deploy

| Command | What it does |
|---------|-------------|
| `/forge-deploy setup` | First-time CI/CD and environment configuration |
| `/forge-deploy staging` | Build → validate → deploy to staging |
| `/forge-deploy production` | Gate check → deploy to production |
| `/forge-deploy rollback` | Rollback to previous version |
| `/smoke-tests` | Post-deploy health checks |
| `/pre-launch-checklist` | Final verification before go-live |

### Transversal (anytime)

| Command | What it does |
|---------|-------------|
| `/debug "issue"` | Diagnose and fix bugs systematically |
| `/explain "code"` | Explain how code works |
| `/context-sync` | Re-sync Claude's understanding of the project |
| `/git-commit` | Auto conventional commits |
| `/progress-report` | Sprint and project status report |
| `/secrets-scan` | Scan for leaked secrets in code and git history |

## Key principles

### Purpose before features
Every project starts with "what problem does this solve for real people?" — not "what framework should we use?" Technical decisions flow from user needs.

### Research before recommending
Claude doesn't guess. `/deep-research` compares options with real data — GitHub activity, bundle sizes, community sentiment, pricing — then makes a clear recommendation with reasoning.

### Analyze before coding
`/implement` maps the full impact (database, API, frontend, config) BEFORE touching any code. The user approves the plan, then Claude executes it.

### Show, don't tell
`styles.html` lets you see the design before app code exists. Sprint demo checkpoints open the app in your browser. Visual verification beats "the code looks correct."

### Minimum necessary code
Inspired by [ponytail](https://github.com/DietrichGebert/ponytail) — before adding anything, climb the ladder: Does it need to exist? → Does stdlib do it? → Does the platform handle it? → Can it be one line? → Only then: minimum code.

### Track what you defer
Mark deliberate shortcuts with `// debt: <what>, <upgrade trigger>`. The `/tech-debt` command harvests these into a ledger so deferrals don't silently become permanent.

## Prerequisites

For the best experience, install these Claude Code extensions:

| Extension | Purpose | Install |
|-----------|---------|---------|
| chrome-devtools-mcp | Browser inspection, screenshots, console monitoring | Claude Code MCP plugin |
| claude-mem | Persistent memory across sessions | `npx claudePlugins install thedotmack/claude-mem` |
| agent-browser | Browser automation for visual testing | `npx claudePlugins install vercel-labs/agent-browser` |

## Automation pipeline

```
                    ┌─────────────────┐
                    │   Orchestrator   │
                    └────────┬────────┘
                             │
              ┌──────────────┼──────────────┐
              ▼              ▼              ▼
        ┌──────────┐  ┌──────────┐  ┌──────────┐
        │ frontend │  │ backend  │  │ database │
        │  agent   │  │  agent   │  │  agent   │
        └────┬─────┘  └────┬─────┘  └────┬─────┘
             │              │              │
             ▼              ▼              ▼
        ┌─────────────────────────────────────┐
        │  After each task:                   │
        │  unit-tests → auto-commit           │
        ├─────────────────────────────────────┤
        │  After each sprint:                 │
        │  e2e → code-review → demo → next   │
        ├─────────────────────────────────────┤
        │  After each milestone:              │
        │  security → perf → changelog → tag  │
        └─────────────────────────────────────┘
```

## Repository structure

```
project-forge/
├── README.md
├── docs/
│   └── framework.md                 # Complete lifecycle reference
├── agents/                          # Pre-built agent definitions
│   ├── agent-map.md                 #   Project type → active agents + dispatch
│   ├── frontend.md                  #   UI components, state, routing
│   ├── backend.md                   #   APIs, business logic, auth
│   ├── database.md                  #   Schema, migrations, queries
│   ├── devops.md                    #   Docker, scripts, deploys
│   ├── qa.md                        #   Tests across all layers
│   ├── cli.md                       #   CLI tools, args, output
│   ├── desktop.md                   #   Electron/Tauri, IPC, packaging
│   ├── mobile.md                    #   React Native/Flutter, navigation
│   ├── library.md                   #   npm/PyPI packages, API design
│   └── data.md                      #   Pipelines, ETL, scheduling
├── commands/                        # 31 commands
│   ├── forge-init.md                #   Interactive project wizard
│   ├── forge-dev.md                 #   Development autopilot
│   ├── forge-deploy.md              #   Staging & production deploy
│   ├── development/                 #   Build-phase commands
│   │   ├── orchestrator.md          #     Sprint execution engine
│   │   ├── implement.md             #     Impact analysis → guided implementation
│   │   ├── deep-research.md         #     Tech comparison & recommendation
│   │   ├── roadmap.md               #     Sprint specs, blockers, demo checkpoints
│   │   ├── agent-gen.md             #     Generate stack-specific agents
│   │   ├── scaffold.md              #     Initial project boilerplate
│   │   ├── context-init.md          #     Load project context
│   │   ├── db-migrate.md            #     Database migrations
│   │   ├── seed-data.md             #     Generate test data
│   │   ├── docs-gen.md              #     Auto-generate documentation
│   │   ├── changelog.md             #     Generate changelog from git
│   │   └── version-bump.md          #     Semver + tag + release
│   ├── quality/                     #   Quality gates
│   │   ├── code-review.md           #     Parallel review + bloat detection
│   │   ├── e2e-test.md              #     Playwright browser tests
│   │   ├── bloat-audit.md           #     Over-engineering scan
│   │   ├── tech-debt.md             #     Deferred shortcut ledger
│   │   ├── unit-tests.md            #     After each task
│   │   ├── integration-tests.md     #     Cross-service verification
│   │   ├── security-review.md       #     OWASP + secrets + deps
│   │   ├── performance-audit.md     #     N+1, bundle size, memory
│   │   └── dependency-audit.md      #     Outdated/vulnerable deps
│   ├── staging/                     #   Deploy gates
│   │   ├── smoke-tests.md           #     Post-deploy health checks
│   │   └── pre-launch-checklist.md  #     Final verification
│   └── transversal/                 #   Available anytime
│       ├── debug.md                 #     Diagnose and fix bugs
│       ├── explain.md               #     Explain how code works
│       ├── context-sync.md          #     Re-sync Claude's context
│       ├── git-commit.md            #     Auto conventional commits
│       ├── progress-report.md       #     Sprint/project status
│       └── secrets-scan.md          #     Scan for leaked secrets
└── templates/                       # 35 templates (generated by forge-init)
    ├── 0-discovery/                 #   Vision, users, constraints
    ├── 1-definition/                #   Requirements, roles, data model, API
    ├── 2-design/                    #   Brand, colors, components, styles.html
    ├── 3-architecture/              #   Stack, infra, auth, security
    └── 4-planning/                  #   Roadmap, sprints, pipeline, risks
```

## License

MIT
