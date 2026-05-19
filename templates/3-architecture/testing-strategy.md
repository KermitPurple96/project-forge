# 🔀 Testing Strategy

## Test types and coverage targets

| Type | Scope | Target coverage | Tools |
|------|-------|----------------|-------|
| Unit | Functions, utils, pure logic | 80%+ | Jest, Vitest, pytest |
| Integration | API endpoints, DB queries | Critical paths | Supertest, httpx |
| E2E | Full user flows | Happy paths + critical edges | Playwright, Cypress |
| Visual regression | UI consistency | Key components | Chromatic, Percy |

## What gets tested

- [ ] All utility/helper functions (unit)
- [ ] All API endpoints (integration)
- [ ] Auth flows: register, login, logout, reset (e2e)
- [ ] Core user flow: [describe] (e2e)
- [ ] Payment flow (e2e, if applicable)
- [ ] Error states and edge cases
- [ ] Permission boundaries (can't access other user's data)

## What does NOT get tested

<!-- Be explicit — not everything needs tests -->
- Styling / CSS
- Third-party library internals
- One-off scripts

## CI integration

- [ ] Tests run on every PR
- [ ] Tests must pass before merge
- [ ] Coverage reported (but not gating)
