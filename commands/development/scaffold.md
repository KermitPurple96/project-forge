# 🤖 Command: scaffold

> Generates the initial project boilerplate from the architecture templates.

## When to use

Once, at the very beginning of development, after templates in phases 0-4 are filled.

## Instructions for Claude

1. Read all filled templates in phases 0-4
2. Based on `stack-selection.md` and `project-structure.md`, generate:
   - Project initialization (e.g. `npx create-next-app`, `cargo init`, `django-admin startproject`)
   - Folder structure as defined
   - Linter/formatter config (ESLint, Prettier, Ruff, etc.)
   - Git config (.gitignore, .editorconfig)
   - Environment config (.env.example with all needed vars from integration-map.md)
   - README.md with setup instructions
   - Docker setup if specified (Dockerfile, docker-compose.yml)
   - CI pipeline skeleton (GitHub Actions workflow)
   - Database schema/migration from data-model.md
   - Base component structure from component-library.md
3. Install dependencies
4. Verify the project builds and runs
5. Make initial commit: `feat: initial project scaffold`

## Expected output

A running project with the defined structure, ready for feature development.
