# 🤖 Command: docker-setup

> Generates optimized Docker configuration for the project.

## Instructions for Claude

1. Read `stack-selection.md` for runtime and dependencies
2. Generate:
   - `Dockerfile` — multi-stage build, minimal final image, non-root user
   - `docker-compose.yml` — app + database + cache + any other services
   - `docker-compose.dev.yml` — dev overrides (hot reload, volumes, debug ports)
   - `.dockerignore` — exclude node_modules, .git, tests, docs
3. Optimization checklist:
   - [ ] Multi-stage build (build stage + runtime stage)
   - [ ] Layer caching (COPY package*.json before COPY . .)
   - [ ] Minimal base image (alpine or slim)
   - [ ] Non-root user
   - [ ] Health check endpoint
   - [ ] Proper signal handling (SIGTERM)
4. Verify it builds and runs: `docker compose up --build`
