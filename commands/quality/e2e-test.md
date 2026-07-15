---
description: Run strict E2E verification for any feature — Playwright browser tests, real API calls, real UI interaction
---

# Strict E2E Verification

Run exhaustive end-to-end verification of features across the full stack. Tests verify real user flows through the actual browser — no mock data, no debug shortcuts, no curl-only verification.

## Invocation

```
/e2e-test                          # Interactive — asks what to test
/e2e-test today                    # Everything committed today
/e2e-test 2026-05-19               # Everything committed on that date
/e2e-test abc123f                  # Specific commit hash
/e2e-test abc123f..def456a         # Commit range
/e2e-test "auth refactor"          # Search commits by message keyword
/e2e-test UserSettings.tsx         # Changes in specific file
/e2e-test --last 5                 # Last 5 commits
```

## CRITICAL: Playwright-First Testing

**Every UI change MUST be verified through Playwright running in a real browser.** This is non-negotiable. The verification flow is:

1. Write a `.spec.ts` Playwright test file
2. Run it with `npx playwright test <file> --reporter=list`
3. Fix any failures in the test OR the code
4. Re-run until all green
5. Commit the test file alongside the feature

**DO NOT** substitute Playwright with:
- curl + grep (only tests API, not UI rendering)
- Unit tests (only tests backend logic, not user experience)
- Manual "the code looks correct" assertions
- `page.textContent("body")` checks (too brittle — use proper locators)

If Playwright is not installed, install it: `npx playwright install chromium`

**Minimum test principle:** A single assert-based smoke test is the minimum viable verification. Don't reach for test frameworks or elaborate harnesses for trivial one-liners — YAGNI applies to tests too. But every feature must have at least one real assertion.

## Verification tiers (ALL are mandatory)

### Tier 1: Build Integrity
- [ ] Backend compiles: `go build ./...` / `cargo build` / `python -m py_compile` / `npm run build` (0 errors)
- [ ] Frontend compiles: `npx tsc --noEmit` (0 errors, if TypeScript)
- [ ] Existing test suites pass: `go test ./...` / `pytest` / `pnpm test` / `cargo test`
- [ ] No regressions in test count (tests added, not removed)

### Tier 2: API Verification (via Playwright `request` context)
For every new/modified API endpoint, test using Playwright's `APIRequestContext` — NOT curl:

```typescript
test("API returns correct shape", async ({ playwright }) => {
  const api = await playwright.request.newContext({ ignoreHTTPSErrors: true });
  const res = await api.post(`${APP_API}/auth/login`, {
    data: { username: "admin", password: "changeme" },
  });
  const { access_token } = await res.json();

  const itemsRes = await api.get(`${APP_API}/items`, {
    headers: { Authorization: `Bearer ${access_token}` },
  });
  expect(itemsRes.ok()).toBe(true);
  const data = await itemsRes.json();
  expect(data).toBeInstanceOf(Array);
});
```

- [ ] Endpoint responds (not 404/405)
- [ ] Valid request returns expected response shape
- [ ] Invalid request returns proper error (not 500)
- [ ] Response fields match frontend types

### Tier 3: UI Verification (Playwright browser tests — MANDATORY)
For every new/modified UI component, write Playwright tests that:

```typescript
test("Settings page has theme selector", async ({ page }) => {
  await loginApp(page);
  await navigateTo(page, "/settings");

  // Click the actual button a user would click
  await page.getByRole("button", { name: /appearance/i }).first().click();
  await page.waitForTimeout(1000);

  // Verify the section opened
  await expect(page.getByRole("heading", { name: "Theme" })).toBeVisible();

  // Verify dropdown has real options
  const select = page.locator("#theme-select");
  await expect(select).toBeVisible();
  const options = select.locator("option");
  expect(await options.count()).toBeGreaterThan(1);
});
```

- [ ] Component renders without crash
- [ ] Interactive elements work (click, type, select)
- [ ] Data from API displays correctly (not just "page loaded")
- [ ] Form submission works end-to-end (fill, submit, verify created)
- [ ] Error states handled (empty inputs, server errors)

### Tier 4: Data Flow (create, read, update, delete)
- [ ] Create entity via Playwright `request` context
- [ ] Navigate to the entity in the browser
- [ ] Verify the data displays correctly in the UI
- [ ] Update and verify changes propagate
- [ ] Delete and verify removal

### Tier 5: Navigation & Discoverability
- [ ] All sidebar/menu pages load without errors
- [ ] Settings tabs render correctly
- [ ] CRUD flows complete start to finish
- [ ] The feature is discoverable — a user can find it without documentation

### Tier 6: Edge Cases
- [ ] Empty state (no data yet) renders a helpful message
- [ ] Very large data sets don't crash the UI (100+ items)
- [ ] Concurrent operations don't cause race conditions
- [ ] Browser refresh preserves expected state

### Tier 7: Cross-Browser (optional but recommended)
- [ ] Chromium (default)
- [ ] Firefox: `npx playwright test --project=firefox`
- [ ] WebKit: `npx playwright test --project=webkit`

## How to write Playwright tests

### File structure
Tests go in the project's E2E test directory. Each test file covers one feature area:

```
e2e/
  helpers.ts          # Login, API helpers, common selectors
  01-navigation.spec.ts
  02-auth.spec.ts
  03-crud-items.spec.ts
  04-settings.spec.ts
```

### Helper patterns

```typescript
// helpers.ts — shared across all test files
export const APP_URL = process.env.APP_URL || "http://localhost:5173";
export const APP_API = process.env.APP_API || "https://localhost:3000/api";

export async function loginApp(page: Page) {
  await page.goto(APP_URL);
  // Handle login form if present
  const loginForm = await page.$('input[type="text"]');
  if (loginForm) {
    await page.fill('input[type="text"]', process.env.TEST_USER || "admin");
    await page.fill('input[type="password"]', process.env.TEST_PASS || "changeme");
    await page.click('button[type="submit"]');
    await page.waitForTimeout(2000);
  }
}

export async function navigateTo(page: Page, path: string) {
  await page.goto(`${APP_URL}${path}`, { waitUntil: "networkidle" });
  await page.waitForTimeout(1000);
}
```

### Test patterns

**Serial tests with shared state** (e.g., create, verify, delete):
```typescript
test.describe.configure({ mode: "serial" });
let entityId: string;

test("create entity", async ({ playwright }) => {
  const api = await playwright.request.newContext({ ignoreHTTPSErrors: true });
  // ... create via API ...
  entityId = resp.id;
});

test("entity visible in UI", async ({ page }) => {
  await loginApp(page);
  await navigateTo(page, "/entities");
  await expect(page.locator(`text=${entityId.slice(0, 8)}`)).toBeVisible();
});

test.afterAll(async ({ playwright }) => {
  // Cleanup
  const api = await playwright.request.newContext({ ignoreHTTPSErrors: true });
  await api.delete(`${APP_API}/entities/${entityId}`, { headers: { ... } });
});
```

**Self-signed HTTPS**: Always use `ignoreHTTPSErrors: true` for API contexts.

**Unique test data**: Use `Date.now()` in names to avoid duplicate key conflicts:
```typescript
const name = `E2E Test ${Date.now()}`;
```

**Cleanup after tests**: Delete created entities in `afterAll`.

### Running tests

```bash
# Run specific test file
npx playwright test e2e/03-crud-items.spec.ts --reporter=list

# Run all E2E tests
npx playwright test e2e/ --reporter=list

# Debug mode (headed browser)
npx playwright test --headed --debug
```

### Common pitfalls

1. **Portal/popup elements**: Dropdowns or calendars rendered via `createPortal` may not be inside the parent container. Use `page.locator()` or `page.getByRole()` which search the whole DOM.

2. **Strict mode violations**: `text=Create` may match multiple elements. Use `page.getByRole("heading", { name: "Create" })` or `.first()`.

3. **Self-signed certs**: `page.evaluate(fetch(...))` runs in the browser sandbox which rejects self-signed certs. Use `playwright.request.newContext({ ignoreHTTPSErrors: true })` for API calls.

4. **Token expiry**: Tokens expire. Re-authenticate in each test or use a helper that auto-refreshes.

5. **Duplicate keys**: Entity names must be unique. Use timestamps in names.

6. **Hash routing**: If the app uses hash routing (`/#/path`), adjust `navigateTo` accordingly: `${APP_URL}/#${path}`.

## Rules

1. **Playwright is mandatory**: Every UI feature gets a `.spec.ts` file. No exceptions.
2. **No mock data**: Test against the real running app.
3. **Fix failures**: If a test fails, fix the issue (code or test) before reporting.
4. **Test from user perspective**: Can a user actually USE this feature?
5. **Commit test files**: Tests are deliverables, not throwaway scripts.
6. **Test the DIFF**: Focus on what changed today, not the entire app.
7. **Services must be running**: If services are down, start them first. Don't skip tests.
8. **Minimum test, not zero test**: A single smoke assert beats no test. But don't build a framework for one check.

## Reporting

Output a verification matrix after all tests pass:

```
## E2E Verification Results

| # | Test | Status | Detail |
|---|------|--------|--------|
| 1 | Dashboard loads | PASS | Playwright 2.1s |
| 2 | User list populated | PASS | 15 users from API |
| 3 | Create user form | PASS | Fill + submit + verify |
| 4 | Edit user | PASS | Update name, verify saved |
| 5 | Delete user | PASS | Remove + verify gone |
| 6 | Settings tabs | PASS | All 4 tabs render |

Playwright: 6/6 passed (45s)
Backend tests: 14/14 pass
Frontend build: 0 errors
Frontend tests: 120/120 pass
```
