# 🤖 Command: forge-init

> The single entry point for starting a new project. An interactive wizard that walks the user through every decision, does research, and generates all templates automatically.

## Invocation

```
/forge-init                    # Start the full wizard from scratch
/forge-init --resume           # Resume an interrupted init (reads .forge/ state)
/forge-init --phase 2          # Jump to a specific phase (if previous phases are filled)
/forge-init --fast             # Quick mode: skip optional sections, use defaults
/forge-init --from brief.md    # Start from a project brief document the user already wrote
```

## The problem this solves

Without this command, the user has to:
1. Open 25+ template files
2. Read each one
3. Fill them in manually
4. Hope they didn't miss anything
5. Hope the answers are consistent across templates

With this command:
1. User answers questions in conversation
2. Claude fills all templates automatically
3. Claude does research where needed
4. Claude ensures consistency across all decisions
5. Everything is generated in one session

---

## Instructions for Claude

### Overview: the 7 conversations

The wizard is structured as 7 sequential conversations. Each one covers a group of templates. After each conversation, Claude writes the templates and confirms before moving on.

The user can stop at any point — progress is saved to `.forge/` and resumed with `--resume`.

```
Conversation 1: Vision & Users         → 0-discovery/*
Conversation 2: Requirements & Flows   → 1-definition/*
Conversation 3: Design & Identity      → 2-design/*
Conversation 4: Architecture & Stack   → 3-architecture/*
Conversation 5: Planning & Sprints     → 4-planning/*
Conversation 6: Agent Generation       → .forge/agents/*
Conversation 7: Review & Kickoff       → final validation
```

---

### Conversation 1: Vision & Users

**Goal:** Understand WHAT they're building, WHO it's for, and WHAT constraints exist.

Start with an open question:

> "Tell me about your project. What are you building and why?"

Let the user talk freely. Then ask targeted follow-ups for anything they didn't cover. Don't ask everything at once — have a conversation.

**Must-extract information:**

```
□ What is the product? (one sentence)
□ What type of project? (determines which agents and templates are active)
    - Web app (fullstack)
    - Web app (frontend only, external API)
    - API / backend service (no UI)
    - CLI tool
    - Desktop app (Electron, Tauri, native)
    - Mobile app (React Native, Flutter, native)
    - Library / package (npm, PyPI, crate)
    - Data pipeline / ETL
    - Combination (e.g. web + mobile sharing a backend)
□ What problem does it solve?
□ Who is the primary user? (specific persona, not "everyone")
□ Who is the secondary user? (if any)
□ What does success look like in 6 months?
□ What is this NOT? (explicit scope boundaries)
□ Budget range (none / free tier only / <$50/mo / <$500/mo / enterprise)
□ Timeline (hobby/no deadline / 1 month / 3 months / specific date)
□ Team (solo / 2-3 devs / team / hiring)
□ Any technical constraints? (must use X, must integrate with Y)
□ Legal/compliance? (GDPR, HIPAA, accessibility requirements)
```

**The project type determines everything downstream:**
- Which agents are generated (see `agents/agent-map.md`)
- Which templates are relevant (CLI tools don't need `responsive-strategy.md`)
- Which stack options are recommended
- How the pipeline checkpoints work

**Templates that are SKIPPED per project type:**

| Template | Skip when |
|----------|-----------|
| `2-design/*` (all) | CLI tool, API only, data pipeline, library |
| `information-architecture.md` | CLI tool, data pipeline, library |
| `wireframes.md` | CLI tool, API only, data pipeline, library |
| `responsive-strategy.md` | CLI tool, desktop, API only, data pipeline, library |
| `i18n-strategy.md` | Unless explicitly multilingual |
| `api-contract.md` | CLI tool (unless it has a server), library |
| `user-flows.md` | Library, data pipeline |

Don't ask about skipped templates — just don't generate them.
□ Legal/compliance? (GDPR, HIPAA, accessibility requirements)
```

**Research step:** After the user describes their vision, search for:
- Competitors / similar products
- Common technical approaches
- Potential pitfalls

Present findings: "I found these similar products: [X, Y, Z]. Here's how they approach it and where they fall short. This could inform our differentiation."

**Output:** Generate and write:
- `templates/0-discovery/vision.md`
- `templates/0-discovery/target-users.md`
- `templates/0-discovery/competitive-analysis.md`
- `templates/0-discovery/constraints.md`

Show a summary and ask: "Does this capture your vision correctly? Anything to change before we move to requirements?"

---

### Conversation 2: Requirements & Flows

**Goal:** Define WHAT the system does, WHO can do what, and HOW data flows.

Start from the vision and generate a first draft:

> "Based on your vision, here are the core features I think you need for MVP: [list]. What would you add, remove, or change?"

**Must-extract information:**

```
□ Core features (MVP — ruthlessly minimal)
□ v1.0 features (important but not launch-blocking)
□ Future features (parking lot)
□ User roles and what each can do
□ Auth method (email/password, OAuth, magic link, invite-only)
□ Main pages/screens the app needs
□ For each page: purpose, tabs/sections, key actions
□ The core user flow step by step (onboarding → main action → outcome)
□ Key entities (what data exists: users, posts, orders, etc.)
□ Relationships between entities
□ API style preference (REST, GraphQL, tRPC) — or let Claude recommend
```

**Interactive approach:**

For the data model, try this pattern:
> "Let me describe the main things your app deals with. You have Users who [do X]. Each user can create [Y], which has [fields]. A [Y] belongs to one user but can be shared with... does that sound right?"

Build the data model conversationally, not by asking "what are your entities?"

For the page structure, try:
> "Let me map out the screens I think you need:
> - Landing page → Login/Register → Dashboard
> - Dashboard has: [tab1], [tab2], [tab3]
> - Settings: profile, billing, notifications
> Does this match your mental model?"

**Output:** Generate and write:
- `templates/1-definition/requirements.md`
- `templates/1-definition/user-roles.md`
- `templates/1-definition/information-architecture.md`
- `templates/1-definition/user-flows.md`
- `templates/1-definition/data-model.md`
- `templates/1-definition/api-contract.md`
- `templates/1-definition/wireframes.md` (text-based descriptions)

---

### Conversation 3: Design & Identity

**Goal:** Define HOW it looks and feels.

Start with mood, not specifics:

> "What vibe should your app have? Some examples:
> - Clean and minimal (like Notion, Linear)
> - Bold and energetic (like Vercel, Stripe)
> - Warm and friendly (like Slack, Headspace)
> - Professional and serious (like Bloomberg, Salesforce)
>
> Or point me to a site/app you love the look of."

**Must-extract information:**

```
□ Visual mood / inspiration (sites, apps, brands they like)
□ Primary brand color (or let Claude suggest based on mood)
□ Light mode, dark mode, or both?
□ Typography preference (modern sans / classic serif / monospace-tech)
□ Content density (spacious / balanced / compact)
□ Mobile-first or desktop-first?
□ Accessibility level (A / AA / AAA)
□ Languages to support (just English? multi-language?)
```

**For aspects the user doesn't have opinions on**, don't ask — decide and explain:

> "Since you want a clean, minimal look, I'll go with:
> - Colors: deep blue primary (#1a365d), soft gray backgrounds
> - Typography: Inter for UI, serif for marketing pages
> - Spacing: generous (8px base grid)
> - Border radius: medium (8px) for a friendly feel
>
> I can adjust any of these."

**Research step:** If the user names inspiration sites, research their design patterns.

**Output:** Generate and write:
- `templates/2-design/brand-identity.md`
- `templates/2-design/design-tokens.md`
- `templates/2-design/component-library.md` (checklist based on features)
- `templates/2-design/responsive-strategy.md`
- `templates/2-design/accessibility-spec.md`
- `templates/2-design/i18n-strategy.md`

---

### Conversation 4: Architecture & Stack

**Goal:** Choose the TECHNOLOGY and STRUCTURE.

**Don't ask "what framework do you want."** Instead, recommend based on everything learned so far:

> "Based on what you need, here's the stack I recommend and why:
>
> **Frontend:** Next.js + Tailwind + shadcn/ui
> → You need SEO (landing page), dynamic dashboard, and fast iteration. Next.js handles both. Tailwind + shadcn gives you a component library without building from scratch.
>
> **Backend:** Next.js API routes (or separate Express if you prefer)
> → Your API is straightforward CRUD. Keeping it in Next.js simplifies deployment.
>
> **Database:** PostgreSQL + Prisma
> → Your data is relational (users → posts → comments). Prisma gives you type-safe queries and easy migrations.
>
> **Hosting:** Vercel (frontend + API) + Supabase (DB)
> → Stays within your free tier budget. Scales when needed.
>
> **Alternative I considered:** [X] — but ruled it out because [Y].
>
> Want to change anything?"

**Must-extract information:**

```
□ Stack agreement or overrides from user
□ Any existing code or systems to integrate with
□ Third-party services needed (payments, email, maps, AI, etc.)
□ Auth architecture (JWT, sessions, OAuth providers)
□ Deployment strategy (serverless, containers, VPS)
□ Monorepo vs multirepo
```

**Research step:** For any unfamiliar tech or integration, research:
- Best libraries for the chosen stack
- Common gotchas and pitfalls
- Pricing and limits of services

**Output:** Generate and write:
- `templates/3-architecture/stack-selection.md`
- `templates/3-architecture/project-structure.md`
- `templates/3-architecture/infra-architecture.md`
- `templates/3-architecture/auth-architecture.md`
- `templates/3-architecture/integration-map.md`
- `templates/3-architecture/error-handling-strategy.md`
- `templates/3-architecture/security-baseline.md`
- `templates/3-architecture/testing-strategy.md`
- `templates/3-architecture/ai-agents-design.md` (only if project uses AI)

---

### Conversation 5: Planning & Sprints

**Goal:** Turn everything into an executable plan.

No questions needed here — Claude generates from the accumulated context:

> "Here's your development plan. I've broken your MVP into 3 sprints:"
>
> **Sprint 1: Foundation** (5 tasks, ~12h)
> - Database schema + migrations
> - Auth system (register, login, JWT)
> - Base layout + navigation
> - ...
>
> **Sprint 2: Core features** (6 tasks, ~16h)
> - [main feature CRUD]
> - ...
>
> **Sprint 3: Polish & integration** (4 tasks, ~10h)
> - ...
>
> "Each sprint, I'll run tests and review automatically. Want to adjust the grouping or priority?"

**Output:** Generate and write:
- `templates/4-planning/roadmap.md`
- `templates/4-planning/task-breakdown.md` (with sprints and agent assignments)
- `templates/4-planning/mvp-definition.md`
- `templates/4-planning/risk-register.md`
- `templates/4-planning/definition-of-done.md`
- `templates/4-planning/pipeline-config.md`

---

### Conversation 6: Agent Generation

**Goal:** Create project-specific agents.

No questions — Claude runs `agent-gen` automatically based on the chosen stack:

> "I'm generating your development agents based on your Next.js + Prisma + PostgreSQL stack..."

Execute the `agent-gen` command. Show the result:

> "Created 4 agents:
> - **frontend-agent:** Next.js + Tailwind + shadcn/ui expert
> - **backend-agent:** API routes + Prisma expert
> - **database-agent:** PostgreSQL + Prisma schema expert
> - **infra-agent:** Vercel + GitHub Actions expert
>
> Agent map written. Orchestrator is ready."

---

### Conversation 7: Review & Kickoff

**Goal:** Final confirmation before development starts.

Present a complete project summary:

```
## Project Summary: [Name]

**Vision:** [one sentence]
**Stack:** [frontend] + [backend] + [database] on [hosting]
**Users:** [primary persona] + [secondary if any]
**MVP scope:** [N features] across [N pages]
**Plan:** [N sprints], estimated [N hours]
**Automation:** [mode] with auto-commit and auto-tests

### Templates generated: 25/25 ✅
### Agents created: 4 ✅
### Pipeline configured ✅

Ready to start development?
→ Run `/orchestrator` to begin Sprint 1
→ Run `/forge-dev` for simplified development mode
```

---

## Conversation style rules

1. **Ask one question at a time** unless they're tightly related
2. **Propose, don't interrogate** — "I'd suggest X because Y. Sound right?" beats "What do you want for X?"
3. **Research before recommending** — don't guess at competitors, tech choices, or pricing. Look it up.
4. **Defaults are your friend** — for anything the user doesn't care about, pick a sensible default and say so
5. **Summarize after each conversation** — show what you captured before writing templates
6. **Let the user skip** — "I don't care, you choose" is a valid answer. Respect it.
7. **Save progress continuously** — write templates after each conversation, not at the end
8. **Be specific, not generic** — "Your landing page needs a hero, pricing section, and FAQ" not "What pages do you need?"

## State file

After each conversation, write progress to `.forge/init-state.md`:

```markdown
# Init State

- [x] Conversation 1: Vision & Users (completed 2026-05-19)
- [x] Conversation 2: Requirements & Flows (completed 2026-05-19)
- [ ] Conversation 3: Design & Identity
- [ ] Conversation 4: Architecture & Stack
- [ ] Conversation 5: Planning & Sprints
- [ ] Conversation 6: Agent Generation
- [ ] Conversation 7: Review & Kickoff
```

This enables `--resume` to pick up where it left off.
