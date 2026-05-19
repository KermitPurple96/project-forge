# 🔀 Project Structure

> Claude can generate this scaffold. Define the conventions here.

## Repository strategy

- [ ] Monorepo (all code in one repo)
- [ ] Multirepo (separate repos per service)
- [ ] Monorepo with packages (turborepo, nx, etc.)

## Folder structure

```
project-root/
├── src/
│   ├── app/           # or pages/ — routes and layouts
│   ├── components/    # Shared UI components
│   ├── features/      # Feature-based modules
│   ├── lib/           # Utilities, helpers, config
│   ├── hooks/         # Custom hooks (if React/Vue)
│   ├── services/      # API calls, external integrations
│   ├── types/         # Type definitions
│   └── styles/        # Global styles
├── server/            # Backend (if full-stack)
│   ├── routes/
│   ├── controllers/
│   ├── services/
│   ├── models/
│   └── middleware/
├── prisma/            # or db/ — schema and migrations
├── tests/
├── public/
├── docs/
└── scripts/
```

## Naming conventions

- **Files:** <!-- kebab-case, camelCase, PascalCase? -->
- **Components:** <!-- PascalCase -->
- **Functions:** <!-- camelCase -->
- **Constants:** <!-- UPPER_SNAKE_CASE -->
- **Database tables:** <!-- snake_case -->
- **API endpoints:** <!-- kebab-case -->
- **CSS classes:** <!-- BEM, Tailwind, CSS Modules? -->

## Import conventions

<!-- Absolute imports? Path aliases? -->
<!-- e.g. @/components/Button instead of ../../../components/Button -->

