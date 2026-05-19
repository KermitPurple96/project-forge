# 🔨 Project Forge

A framework to build any software project from idea to production using Claude.

**Project Forge** is a collection of **commands** (automated by Claude) and **templates** (guided documents you fill in) that cover the entire software development lifecycle — from "I have an idea" to "it's deployed and monitored".

## How it works

Every software project goes through phases. Project Forge gives you a structured path through all of them:

| Phase | Name | What you get |
|-------|------|-------------|
| 0 | **Discovery** | Clarify your vision, users, constraints |
| 1 | **Definition** | Requirements, roles, data model, API contract |
| 2 | **Design** | Brand, colors, components, accessibility |
| 3 | **Architecture** | Stack, infrastructure, auth, integrations |
| 4 | **Planning** | Roadmap, task breakdown, MVP scope, risks |
| 5 | **Development** | Scaffold, develop, migrate, seed, refactor |
| 6 | **Quality** | Tests (unit, e2e, integration), reviews, audits |
| 7 | **Staging** | CI/CD, Docker, env config, smoke tests |
| 8 | **Production** | Deploy, monitoring, backups, incident response |
| 9 | **Post-launch** | Analytics, feedback, versioning, tech debt |
| T | **Transversal** | Debug, progress reports, git helpers (any phase) |

## Two types of items

| Type | Icon | Description |
|------|------|-------------|
| **Template** | 📝 | A document with guided questions you fill in. Lives in `/templates/` |
| **Command** | 🤖 | An automated command Claude executes. Lives in `/commands/` |
| **Mixed** | 🔀 | Claude generates a draft, you validate and adjust |

## Quick start — 4 commands to go from idea to production

```bash
/forge-init          # Interactive wizard: define your project (~30-60 min)
/continue            # Build the next task (repeat until done)
/deploy staging      # Deploy to staging and validate
/deploy production   # Go live
```

That's it. Everything else is optional.

### The three speeds of development

| Phase | User effort | AI effort | Time |
|-------|------------|-----------|------|
| **Init** (`forge-init`) | High — answer questions, make decisions | Research, generate templates, scaffold | 30-60 min |
| **Development** (`continue`, `fix`, `status`) | Low — just say "continue" or correct things | Full — implement, test, commit, repeat | Hours-days |
| **Deploy** (`deploy staging/production`) | Minimal — confirm production deploy | Full — build, validate, deploy, monitor | Minutes |

### All commands at a glance

| Command | What it does |
|---------|-------------|
| `/forge-init` | Guided wizard → fills all templates, generates agents, scaffolds project |
| `/continue` | Implements next task from roadmap, tests, commits |
| `/continue sprint` | Runs all remaining tasks in current sprint (full-auto) |
| `/fix "description"` | Quick correction → fix, test, commit |
| `/status` | Shows progress, current sprint, what's next |
| `/orchestrator` | Advanced control over sprint execution |
| `/deploy staging` | Validate + deploy to staging |
| `/deploy production` | Gate check + deploy to production |
| `/deploy --rollback` | Rollback to previous version |
| `/code-review` | Review code changes for bugs and security |
| `/e2e-test` | Run strict end-to-end verification |
| `/secrets-scan` | Scan for leaked secrets in code and git history |

### Automation pipeline

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
        │  e2e → code-review → integration    │
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
│   └── framework.md
├── commands/                         # 🤖 Automated commands
│   ├── forge-init.md                 # 🚀 Project wizard (start here)
│   ├── deploy.md                     # 🚀 Staging & production deploy
│   ├── development/
│   │   ├── orchestrator.md           # Sprint execution engine
│   │   ├── agent-gen.md              # Generate stack-specific agents
│   │   ├── continue.md              # Continue next task
│   │   ├── fix.md                   # Quick corrections
│   │   ├── status.md                # Project progress
│   │   ├── scaffold.md, context-init.md, develop.md
│   │   ├── db-migrate.md, seed-data.md, refactor.md
│   │   └── docs-gen.md, changelog.md
│   ├── quality/
│   │   ├── e2e-test.md, code-review.md
│   │   ├── unit-tests.md, integration-tests.md
│   │   ├── security-review.md, performance-audit.md
│   │   └── accessibility-audit.md, dependency-audit.md, ...
│   ├── staging/                      # Pre-production
│   ├── production/                   # Production ops
│   ├── post-launch/                  # Post-launch iteration
│   └── transversal/                  # Any-phase commands
│       └── secrets-scan.md, debug.md, progress-report.md, ...
└── templates/                        # 📝 User fills in
    ├── 0-discovery/                  # Vision, users, constraints
    ├── 1-definition/                 # Requirements, roles, data model, API
    ├── 2-design/                     # Brand, colors, components
    ├── 3-architecture/               # Stack, infra, auth, security
    └── 4-planning/                   # Roadmap, sprints, pipeline config
```

## Contributing

This is a living framework. If you find a phase or command that's missing, open an issue or PR.

## License

MIT
