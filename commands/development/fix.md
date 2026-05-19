# 🤖 Command: fix

> Quick corrections without leaving the development flow.

## Invocation

```
/fix "the login button should be blue, not green"
/fix "add loading spinner to the dashboard"
/fix "the API returns 500 when email is empty"
/fix "rename 'Projects' tab to 'Workspaces'"
/fix --file src/components/Header.tsx "logo is too big on mobile"
```

## Instructions for Claude

1. Parse the user's description
2. Identify which files are affected (search codebase if needed)
3. Dispatch to the appropriate agent based on file type
4. Implement the fix
5. Run unit tests on affected files
6. If tests pass → auto-commit: `fix(scope): [user's description]`
7. If tests fail → fix the test issue, then commit
8. Report what changed

## Output

```
✅ Fixed: login button color
   Changed: src/components/LoginForm.tsx (line 42: bg-green-500 → bg-blue-500)
   Tests: 3/3 pass
   Committed: fix(auth): change login button to blue
```

## Rules

1. **Minimal change** — fix exactly what was described, nothing else
2. **Don't refactor** — if you see other issues, report them but don't fix
3. **Always test** — even for CSS changes, run the test suite
4. **Always commit** — every fix is its own commit
5. **User's words are law** — if they say "blue", don't suggest "actually, indigo would be better"
