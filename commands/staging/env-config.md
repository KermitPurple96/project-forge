# 🔀 Command: env-config

> Generates environment configuration files with validation.

## Instructions for Claude

1. Read `integration-map.md` and `stack-selection.md` for all required env vars
2. Generate per environment:
   - `.env.example` — all vars with descriptions, no real values
   - `.env.development` — local dev values (localhost URLs, test keys)
   - `.env.staging` — staging values (placeholders for secrets)
   - `.env.production` — production values (placeholders for secrets)
3. Generate validation schema (Zod, Joi, or equivalent):
   - Required vs optional vars
   - Type validation (URL, number, boolean, enum)
   - Fail fast on startup if missing required vars
4. Add `.env` to `.gitignore` if not already there
5. Document how to get each API key (link to provider dashboard)
