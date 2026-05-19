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

## Quick start

### 1. Start a new project

Copy the templates to your project and fill them in order:

```bash
cp -r templates/ my-project/.forge/
```

### 2. Fill in discovery & definition

Start with the basics — open each template and answer the questions:

```
my-project/.forge/0-discovery/vision.md          # What are you building and why?
my-project/.forge/0-discovery/target-users.md     # Who is it for?
my-project/.forge/1-definition/requirements.md    # What does it need to do?
my-project/.forge/1-definition/user-roles.md      # Who can do what?
```

### 3. Use commands during development

Commands are designed to be used with Claude Code or as prompts in Claude:

```bash
# Initialize project context
claude "Follow the instructions in commands/development/scaffold.md"

# Run e2e tests
claude "Follow the instructions in commands/quality/e2e-test.md"

# Code review
claude "Follow the instructions in commands/quality/code-review.md"
```

## Repository structure

```
project-forge/
├── README.md
├── docs/
│   └── framework.md              # Complete lifecycle map
├── templates/                    # 📝 Documents you fill in
│   ├── 0-discovery/
│   │   ├── vision.md
│   │   ├── target-users.md
│   │   ├── competitive-analysis.md
│   │   └── constraints.md
│   ├── 1-definition/
│   │   ├── requirements.md
│   │   ├── user-roles.md
│   │   ├── information-architecture.md
│   │   ├── user-flows.md
│   │   ├── data-model.md
│   │   ├── api-contract.md
│   │   └── wireframes.md
│   ├── 2-design/
│   │   ├── brand-identity.md
│   │   ├── design-tokens.md
│   │   ├── component-library.md
│   │   ├── responsive-strategy.md
│   │   ├── accessibility-spec.md
│   │   └── i18n-strategy.md
│   ├── 3-architecture/
│   │   ├── stack-selection.md
│   │   ├── project-structure.md
│   │   ├── infra-architecture.md
│   │   ├── auth-architecture.md
│   │   ├── integration-map.md
│   │   ├── error-handling-strategy.md
│   │   ├── security-baseline.md
│   │   ├── testing-strategy.md
│   │   └── ai-agents-design.md
│   └── 4-planning/
│       ├── roadmap.md
│       ├── task-breakdown.md
│       ├── mvp-definition.md
│       ├── risk-register.md
│       └── definition-of-done.md
└── commands/                     # 🤖 Automated commands
    ├── development/
    │   ├── context-init.md
    │   ├── scaffold.md
    │   ├── develop.md
    │   ├── db-migrate.md
    │   ├── seed-data.md
    │   ├── refactor.md
    │   ├── docs-gen.md
    │   └── changelog.md
    ├── quality/
    │   ├── e2e-test.md
    │   ├── code-review.md
    │   ├── unit-tests.md
    │   ├── integration-tests.md
    │   ├── security-review.md
    │   ├── performance-audit.md
    │   ├── accessibility-audit.md
    │   ├── dependency-audit.md
    │   ├── consistency-check.md
    │   └── test-coverage-report.md
    ├── staging/
    │   ├── env-config.md
    │   ├── ci-cd-pipeline.md
    │   ├── docker-setup.md
    │   ├── db-migration-plan.md
    │   ├── smoke-tests.md
    │   ├── load-testing.md
    │   ├── staging-deploy.md
    │   └── pre-launch-checklist.md
    ├── production/
    │   ├── prod-deploy.md
    │   ├── monitoring-setup.md
    │   ├── backup-strategy.md
    │   ├── incident-runbook.md
    │   ├── rollback.md
    │   └── hotfix.md
    ├── post-launch/
    │   ├── analytics-setup.md
    │   ├── feedback-loop.md
    │   ├── retrospective.md
    │   ├── version-bump.md
    │   ├── deprecation-plan.md
    │   └── tech-debt-audit.md
    └── transversal/
        ├── context-sync.md
        ├── progress-report.md
        ├── decision-log.md
        ├── debug.md
        ├── explain.md
        ├── standards-enforce.md
        ├── git-commit.md
        ├── pr-description.md
        └── secrets-scan.md
```

## Contributing

This is a living framework. If you find a phase or command that's missing, open an issue or PR.

## License

MIT
