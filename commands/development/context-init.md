# 🤖 Command: context-init

> Initializes Claude's understanding of the project before any development work.

## When to use

Run this at the start of every development session, or when Claude loses context.

## Instructions for Claude

1. Read the project structure: `find . -type f -not -path '*/node_modules/*' -not -path '*/.git/*' | head -100`
2. Read key config files: `package.json`, `tsconfig.json`, `.env.example`, `prisma/schema.prisma` (or equivalent)
3. Read the filled templates in `.forge/` (or wherever the user keeps them)
4. Summarize:
   - What this project is (from vision.md)
   - Current stack (from stack-selection.md or detected from configs)
   - Current state (what's built, what's pending from roadmap.md)
   - Key conventions (from project-structure.md)
5. Confirm understanding with the user before proceeding

## Expected output

A concise summary: "This is [project], built with [stack]. Currently at [phase]. Next task: [X]."
