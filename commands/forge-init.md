# 🤖 Command: forge-init

> The project initialization wizard. Guides the user through every decision needed to go from idea to development-ready. This is the ONLY command a user needs to run to start a new project.

## Invocation

```
/forge-init                        # Start full wizard from scratch
/forge-init --resume               # Resume a partially completed init
/forge-init --from brief.md        # Start from a project brief document
/forge-init --skip-research        # Skip competitive analysis (faster)
/forge-init --minimal              # Only ask essential questions (MVP mode)
```

## What this command produces

By the end of `forge-init`, the project will have:

```
.forge/
├── 0-discovery/       ✅ All templates filled
├── 1-definition/      ✅ All templates filled
├── 2-design/          ✅ All templates filled
├── 3-architecture/    ✅ All templates filled
├── 4-planning/        ✅ Roadmap, sprints, pipeline configured
├── agents/            ✅ Stack-specific agents generated
└── init-complete      ✅ Marker file
```

Plus a scaffolded project ready for `/orchestrator` or `/continue`.

---

## Instructions for Claude

### Overview of the flow

```
Phase A: UNDERSTAND (5-10 min)
  → What are you building? Who for? Why?
  
Phase B: RESEARCH (2-5 min, automated)
  → Competitive analysis, tech research
  
Phase C: DEFINE (10-15 min)
  → Features, roles, data model, API, flows
  
Phase D: DESIGN (5-10 min)
  → Brand, colors, components, responsive
  
Phase E: ARCHITECT (5-10 min)
  → Stack, infrastructure, auth, integrations
  
Phase F: PLAN (2-5 min, mostly automated)
  → Roadmap, sprints, pipeline, agents
  
Phase G: SCAFFOLD (automated)
  → Generate project, install deps, initial commit
```

Total: ~30-60 minutes of user interaction, then development-ready.

---

### Phase A: UNDERSTAND

> Goal: Go from vague idea to clear project definition.

Start with an open question, then drill down. Don't dump a form — have a conversation.

**Opening:**
```
"Tell me about your project. What do you want to build, and what problem does it solve?"
```

Wait for the user's response. Then ask follow-ups based on what's missing:

**Round 1 — Vision** (only ask what they didn't already cover):
- What type of product? (web app, mobile app, API, CLI, SaaS platform, marketplace, tool...)
- Who's going to use it? (developers, businesses, consumers, internal team...)
- What's the one thing it MUST do well?
- Is there a deadline or budget constraint?

**Round 2 — Scope** (based on their answers):
- Is this a solo project or team?
- MVP first or full product?
- Public or internal?
- Monetization? (free, freemium, subscription, one-time, none)

**Round 3 — References** (quick):
- Any products you want it to feel like? (UI inspiration, competitors, "like X but for Y")
- Any tech preferences or requirements? (must use React, must be Python, etc.)

**Rules for this phase:**
- Ask 2-3 questions at a time MAX — don't overwhelm
- Adapt based on answers — if they're technical, skip basic questions
- If they provide a brief document (`--from brief.md`), extract answers and only ask gaps
- Fill `vision.md`, `target-users.md`, `constraints.md` from conversation

---

### Phase B: RESEARCH (automated)

> Goal: Understand the competitive landscape and technical options.

Run this automatically after Phase A. Tell the user:
```
"Let me research the landscape for a few minutes..."
```

**B.1 — Competitive analysis:**
- Search for similar products/solutions
- Analyze 3-5 competitors: features, pricing, weaknesses
- Identify the user's differentiator
- Fill `competitive-analysis.md`

**B.2 — Technical research:**
- Research best stack options for this type of project
- Research key libraries/services needed (auth, payments, email, etc.)
- Research hosting options appropriate for scale and budget
- Note findings for Phase E

**B.3 — Present findings:**
```
"Here's what I found:
- 3 main competitors: [X], [Y], [Z]. Your differentiator is [...]
- For this type of project, I'd recommend [stack options]. Here's why..."
```

Ask user if they want to adjust direction based on findings.

---

### Phase C: DEFINE (interactive)

> Goal: Turn the vision into concrete, buildable requirements.

**C.1 — Requirements gathering:**

Ask the user to describe the main features. Use follow-up questions to extract:
- User stories: "As a [role], I want [action] so that [benefit]"
- Classify as: MVP (must-have) vs v1.0 (important) vs future (nice-to-have)
- Fill `requirements.md`

```
"Let's map out the features. Tell me what a user should be able to do, 
and I'll organize them by priority. Start with the most important one."
```

**C.2 — User roles:**

Based on the requirements, propose roles:
```
"Based on what you described, I see these roles:
- Admin: manages everything
- User: the main persona, does [X]
- Guest: can view but not create
Does that match your thinking? Any roles missing?"
```

Generate permission matrix. Fill `user-roles.md`.

**C.3 — Pages & navigation:**

Based on requirements and roles, propose the information architecture:
```
"Here's the site map I'd suggest:
/ → Landing page
/dashboard → Main view after login
/dashboard/[feature] → Each main feature
/settings → User preferences
/admin → Admin panel (admin only)

Does this make sense? Any pages missing?"
```

Fill `information-architecture.md`.

**C.4 — User flows:**

For the 3-5 most critical flows, walk through step by step:
```
"Let's trace the main user journey:
1. User arrives at landing page
2. Signs up with email
3. Sees onboarding wizard
4. Creates their first [thing]
5. ...
Anything you'd change?"
```

Fill `user-flows.md`.

**C.5 — Data model (automated from requirements):**

Generate the data model from everything gathered:
```
"Based on your features, here's the data model I'd suggest:
- User (id, email, name, role, created_at)
- [Entity] (id, user_id, title, ...)
- [Entity] (...)

Relationships:
- User has many [entities]
- [Entity] belongs to [entity]

Does this look right?"
```

Fill `data-model.md`.

**C.6 — API contract (automated):**

Generate from data model + requirements:
```
"And here are the API endpoints:
- POST /auth/register
- POST /auth/login
- GET /[resources]
- POST /[resources]
- ...
"
```

Fill `api-contract.md`. Wireframes are optional — ask if they want to describe any screens.

---

### Phase D: DESIGN (interactive)

> Goal: Define the visual identity and component needs.

**D.1 — Brand basics:**
```
"Let's define the look and feel.
- Do you have a name yet? 
- How should it feel? (e.g., professional, playful, minimal, bold)
- Any colors in mind, or should I suggest a palette?"
```

**D.2 — Color palette (automated if no preference):**

If user has no colors, generate a palette based on:
- Industry (fintech → blues, health → greens, creative → vibrant)
- Tone (professional → muted, playful → saturated)
- Accessibility (ensure WCAG AA contrast)

Present 2-3 palette options:
```
"Here are 3 palette options:

Option A (Clean & Professional):
  Primary: #2563EB  Secondary: #7C3AED  
  
Option B (Warm & Approachable):
  Primary: #EA580C  Secondary: #0891B2

Option C (Minimal & Modern):
  Primary: #18181B  Secondary: #6366F1

Which direction appeals to you? Or mix and match."
```

Fill `design-tokens.md`.

**D.3 — Component inventory (automated):**

Based on the pages and features defined, auto-generate the component checklist:
```
"Based on your features, you'll need these components: [list].
I'll generate the full component library spec."
```

Fill `component-library.md`.

**D.4 — Quick decisions:**
- Responsive? (mobile-first? which devices?)
- Dark mode?
- Multi-language?
- Accessibility level? (recommend AA)

Fill remaining design templates.

---

### Phase E: ARCHITECT (interactive + automated)

> Goal: Lock in technical decisions.

**E.1 — Stack recommendation:**

Based on ALL previous context, recommend a stack with reasoning:
```
"For a [type of app] with [these requirements], here's what I recommend:

Frontend: Next.js + Tailwind + Zustand
  → Why: SSR for SEO, rapid UI with Tailwind, simple state management

Backend: Node.js + Express (or built into Next.js API routes)
  → Why: Same language as frontend, fast to develop, your team knows JS

Database: PostgreSQL + Prisma
  → Why: Relational data model, great ORM, easy migrations

Hosting: Vercel (frontend) + Railway (backend + DB)
  → Why: Free tier to start, easy scaling, git-based deploys

Do you agree, or want to change anything?"
```

If user has strong preferences (from Phase A), respect them.

Fill `stack-selection.md`.

**E.2 — Auto-generate remaining architecture:**
- `project-structure.md` — from stack
- `auth-architecture.md` — from user-roles + stack
- `infra-architecture.md` — from stack + hosting
- `integration-map.md` — from requirements (payments, email, etc.)
- `error-handling-strategy.md` — from stack best practices
- `testing-strategy.md` — from stack + project type
- `security-baseline.md` — standard template

Present a summary, ask for approval.

---

### Phase F: PLAN (mostly automated)

> Goal: Break everything into executable sprints.

**F.1 — Generate roadmap:**
- Phase 1: MVP (from mvp-definition)
- Phase 2: v1.0 (important features)
- Phase 3: Growth (nice-to-have + scale)

**F.2 — Generate task breakdown with sprints:**
- Break MVP into atomic tasks
- Group into sprints (max 8 tasks each)
- Assign agents to each task
- Estimate hours

**F.3 — Configure pipeline:**
```
"How do you want to run development?

For Sprint 1, I'd suggest semi-auto (you approve each task) since 
it's the foundation. After that, we can go full-auto. Sound good?"
```

Fill `pipeline-config.md`.

**F.4 — Generate agents:**
Run `agent-gen` automatically based on the chosen stack.

---

### Phase G: SCAFFOLD (fully automated)

> Goal: Generate the actual project and be ready to develop.

1. Run `scaffold` command — create project from architecture
2. Initial commit: `feat: initial project scaffold [forge-init]`
3. Display final summary:

```
## 🔨 Project Forge — Init Complete

### Your project: [name]
[One-line description]

### Stack
Frontend: [X]  |  Backend: [Y]  |  Database: [Z]  |  Hosting: [W]

### Roadmap
- MVP: [N] tasks across [M] sprints (~[X]h estimated)
- v1.0: [N] tasks across [M] sprints
- Total: [N] tasks

### Agents generated
- frontend-agent (React + Tailwind)
- backend-agent (Express + REST)
- database-agent (PostgreSQL + Prisma)
- infra-agent (Docker + GitHub Actions)

### Next steps
Run `/continue` to start Sprint 1, or `/orchestrator --plan` to review the plan first.
```

---

## Minimal mode (`--minimal`)

For quick prototypes, skip:
- Competitive analysis
- Detailed design (use defaults)
- Accessibility/i18n specs
- Risk register

Only ask: What + Who + Core features + Stack preference → scaffold.
~10 minutes to development-ready.

---

## Resume mode (`--resume`)

If `forge-init` is interrupted:
1. Read `.forge/` directory
2. Identify which templates are filled vs empty
3. Pick up from the first unfilled phase
4. Skip completed phases

---

## Rules

1. **Conversational, not a form** — adapt questions based on answers, skip what's already clear
2. **2-3 questions at a time** — never dump 10 questions at once
3. **Show your work** — when auto-generating (data model, stack), show the user and ask for approval
4. **Research is real** — actually search the web for competitors and tech options
5. **Opinionated defaults** — if the user says "I don't know", pick the best option and explain why
6. **The output is complete** — when forge-init finishes, EVERY template in phases 0-4 is filled
7. **Respect expertise** — if the user is technical, go faster and skip explanations
8. **Don't lose context** — if the conversation is long, periodically summarize decisions made so far
