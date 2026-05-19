# 🤖 Command: git-commit

> Generates a conventional commit message from staged changes.

## Instructions for Claude

1. Read staged changes: `git diff --cached`
2. Determine commit type:
   - `feat` — new feature
   - `fix` — bug fix
   - `docs` — documentation
   - `style` — formatting, no logic change
   - `refactor` — code restructuring
   - `test` — adding/fixing tests
   - `chore` — build, CI, dependencies
3. Determine scope (module/feature affected)
4. Write concise description (imperative mood, <72 chars)
5. Add body if the change is complex (what and why, not how)
6. Add `BREAKING CHANGE:` footer if applicable
7. Execute: `git commit -m "[type]([scope]): [description]"`

## Example output

```
feat(auth): add password reset via email

Users can now request a password reset link. The link expires after 1 hour.
Implements task MVP-012.
```
