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
/e2e-test "dropper injection"      # Search commits by message keyword
/e2e-test LoaderConfig.tsx         # Changes in specific file
/e2e-test --scope bl               # Only Black-Lotus
/e2e-test --scope df               # Only Devil-Fruit
/e2e-test --scope bd               # Only Black-Death
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
- Go unit tests (only tests backend logic, not user experience)
- Manual "the code looks correct" assertions
- `page.textContent("body")` checks (too brittle — use proper locators)

If Playwright is not installed, install it: `npx playwright install chromium`

## Verification tiers (ALL are mandatory)

### Tier 1: Build Integrity
- [ ] Backend compiles: `go build ./...` (0 errors)
- [ ] TypeScript compiles: `npx tsc --noEmit` (0 errors)
- [ ] Existing test suites pass: `go test ./...`, `pnpm test` / `npx vitest run`
- [ ] No regressions in test count (tests added, not removed)

### Tier 2: API Verification (via Playwright `request` context)
For every new/modified API endpoint, test using Playwright's `APIRequestContext` — NOT curl:

```typescript
test("API returns correct shape", async ({ playwright }) => {
  const api = await playwright.request.newContext({ ignoreHTTPSErrors: true });
  const res = await api.post("https://localhost:40443/api/v1/auth/login", {
    data: { username: "admin", password: "changeme" },
  });
  const { access_token } = await res.json();

  const campaignRes = await api.get("https://localhost:40443/api/v1/campaigns", {
    headers: { Authorization: `Bearer ${access_token}` },
  });
  expect(campaignRes.ok()).toBe(true);
  const data = await campaignRes.json();
  expect(data).toBeInstanceOf(Array);
});
```

- [ ] Endpoint responds (not 404/405)
- [ ] Valid request returns expected response shape
- [ ] Invalid request returns proper error (not 500)
- [ ] Response fields match TypeScript types

### Tier 3: UI Verification (Playwright browser tests — MANDATORY)
For every new/modified UI component, write Playwright tests that:

```typescript
test("Create dialog has APT dropdown", async ({ page }) => {
  await loginBL(page);
  await goTo(page, "/campaigns");

  // Click the actual button a user would click
  await page.getByRole("button", { name: /create/i }).first().click();
  await page.waitForTimeout(1000);

  // Verify the dialog opened
  await expect(page.getByRole("heading", { name: "Create Campaign" })).toBeVisible();

  // Verify dropdown has real options loaded from API
  const select = page.locator("#campaign-profile");
  await expect(select).toBeVisible();
  const options = select.locator("option");
  expect(await options.count()).toBeGreaterThan(1);
});
```

- [ ] Component renders without crash
- [ ] Interactive elements work (click, type, select)
- [ ] Data from API displays correctly (not just "page loaded")
- [ ] Form submission works end-to-end (fill → submit → verify created)
- [ ] Error states handled (empty inputs, server errors)

### Tier 4: Data Flow (create → API → DB → UI display)
- [ ] Create entity via Playwright `request` context
- [ ] Navigate to the entity in the browser
- [ ] Verify the data displays correctly in the UI
- [ ] Update and verify changes propagate
- [ ] Delete and verify removal

### Tier 5: Functional Verification
- [ ] The feature ACTUALLY WORKS for a real user
- [ ] Golden path: start to finish with real data
- [ ] The feature is DISCOVERABLE — a user can find it

## How to write Playwright tests

### File structure
Tests go in the project's E2E test directory (e.g., `pentestkit-qa/e2e/bl/`).
Each test file covers one feature area:

```
e2e/
  bl/
    helpers.ts          # Login, API helpers, common selectors
    01-navigation.spec.ts
    02-listeners.spec.ts
    11-campaign-apt.spec.ts   # Your new test
```

### Helper patterns

```typescript
// helpers.ts — shared across all test files
export const BL_URL = "http://localhost:5173";
export const BL_API = "https://localhost:40443/api/v1";

export async function loginBL(page: Page) {
  await page.goto(BL_URL);
  // Handle login form if present
  const loginForm = await page.$('input[type="text"]');
  if (loginForm) {
    await page.fill('input[type="text"]', "admin");
    await page.fill('input[type="password"]', "changeme");
    await page.click('button[type="submit"]');
    await page.waitForTimeout(2000);
  }
}

export async function goTo(page: Page, path: string) {
  await page.goto(`${BL_URL}/#${path}`, { waitUntil: "networkidle" });
  await page.waitForTimeout(1000);
}
```

### Test patterns

**Serial tests with shared state** (e.g., create → verify → delete):
```typescript
test.describe.configure({ mode: "serial" });
let entityId: string;

test("create entity", async ({ playwright }) => {
  const api = await playwright.request.newContext({ ignoreHTTPSErrors: true });
  // ... create via API ...
  entityId = resp.id;
});

test("entity visible in UI", async ({ page }) => {
  await loginBL(page);
  await goTo(page, "/entities");
  await expect(page.locator(`text=${entityId.slice(0, 8)}`)).toBeVisible();
});

test.afterAll(async ({ playwright }) => {
  // Cleanup
  const api = await playwright.request.newContext({ ignoreHTTPSErrors: true });
  await api.delete(`${BL_API}/entities/${entityId}`, { headers: { ... } });
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
npx playwright test e2e/bl/11-campaign-apt.spec.ts --reporter=list

# Run all E2E tests
npx playwright test e2e/bl/ --reporter=list

# Debug mode (headed browser)
npx playwright test --headed --debug
```

### Common pitfalls

1. **Portal/popup elements**: Dropdowns or calendars rendered via `createPortal` may not be inside the parent container. Use `page.locator()` or `page.getByRole()` which search the whole DOM.

2. **Strict mode violations**: `text=Create` may match multiple elements. Use `page.getByRole("heading", { name: "Create" })` or `.first()`.

3. **Self-signed certs**: `page.evaluate(fetch(...))` runs in the browser sandbox which rejects self-signed certs. Use `playwright.request.newContext({ ignoreHTTPSErrors: true })` for API calls.

4. **JWT expiry**: Tokens expire in 5 minutes. Re-authenticate in each test or use a helper that auto-refreshes.

5. **Operation ID**: Some APIs require `X-Operation-ID` header. Fetch it from `GET /operations` first.

6. **Duplicate keys**: Campaign/operation names must be unique. Use timestamps in names.

## Rules

1. **Playwright is mandatory**: Every UI feature gets a `.spec.ts` file. No exceptions.
2. **No mock data**: Test against the real running app.
3. **Fix failures**: If a test fails, fix the issue (code or test) before reporting.
4. **Test from user perspective**: Can a user actually USE this feature?
5. **Commit test files**: Tests are deliverables, not throwaway scripts.
6. **Test the DIFF**: Focus on what changed today, not the entire app.
7. **Services must be running**: If services are down, start them first. Don't skip tests.

## Reporting

Output a verification matrix after all tests pass:

```
## E2E Verification Results

| # | Test | Status | Detail |
|---|------|--------|--------|
| 1 | Campaigns page loads | PASS | Playwright 8.5s |
| 2 | APT dropdown populated | PASS | 15 profiles from DF |
| 3 | Calendar picker works | PASS | Portal popup, Today button |
| 4 | Campaign creation API | PASS | 201 Created |
| 5 | Coverage API shape | PASS | 12 phases, 129 available |
| 6 | Campaign detail UI | PASS | APT card + TTP dashboard |

Playwright: 8/8 passed (1.1m)
Go tests: 12/12 packages pass
TS build: 0 errors
Client tests: 901/901 pass
```
