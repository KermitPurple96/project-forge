---
description: Deep research on technologies, APIs, libraries, or approaches before implementation — compare options, read docs, find best practices
---

# Deep Research

Research technologies, libraries, APIs, or implementation approaches before writing code. Produces a structured comparison with a clear recommendation.

## Invocation

```
/deep-research "best auth library for Next.js"
/deep-research "how to implement WebSocket in Go"
/deep-research "payment processing options under $50/mo"
/deep-research --compare "Prisma vs Drizzle vs TypeORM"
/deep-research --api "Stripe Connect integration"
/deep-research --architecture "event sourcing vs CRUD for order management"
```

## Instructions for Claude

### Step 1: Understand the research question

Parse the query into:
- **Domain:** What area? (auth, database, payments, UI, deployment, etc.)
- **Constraints:** Budget, stack compatibility, team experience, timeline
- **Decision type:** Library choice, architecture pattern, API integration, or implementation approach

### Step 2: Research

Use ALL available tools:
- **WebSearch** for comparisons, benchmarks, community sentiment
- **context7 MCP** for official documentation of candidate libraries
- **WebFetch** for specific articles, blog posts, case studies
- **GitHub** (via `gh` CLI) for: stars, recent commits, open issues, maintenance status

For each candidate, collect:
- Official docs URL
- GitHub stars + last commit date
- Bundle size (frontend) or binary size (backend)
- Maintenance status (active / maintenance mode / abandoned)
- Known issues or gotchas
- Migration path if switching later

### Step 3: Compare

Present a structured comparison:

```
## Research: [Question]

### Candidates

| | Option A | Option B | Option C |
|---|---|---|---|
| Maturity | 5 years | 2 years | 8 months |
| Stars | 45k | 22k | 8k |
| Bundle size | 12KB | 5KB | 3KB |
| TypeScript | Native | Native | Partial |
| Last release | 2 weeks ago | 3 days ago | 1 month ago |
| Your stack compat | Excellent | Good | Needs adapter |

### Recommendation

**Use [Option B]** because:
1. [Specific reason tied to their requirements]
2. [Specific reason tied to their constraints]
3. [Specific advantage for their use case]

**Runner-up:** [Option A] — choose this if [condition].

**Avoid:** [Option C] — because [specific reason].

### Implementation notes
- Install: `npm install option-b`
- Key config: [specific setup steps]
- Gotchas: [things to watch for]
- Migration path: if you outgrow this, [what to switch to]
```

### Step 4: Save

Write research to `docs/research/[topic-slug].md` for future reference.

## Rules

1. **Never recommend without researching.** "I think X is good" is not research.
2. **Check recency.** A library with no commits in 12 months is a risk — flag it.
3. **Consider the user's stack.** Don't recommend a React library for a Vue project.
4. **Budget matters.** Don't recommend $99/mo services to someone on free tier.
5. **One clear recommendation.** Don't say "it depends" without explaining on what.
6. **Show your work.** Link to sources. Let the user verify.
