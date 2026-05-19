# 🤖 Command: changelog

> Generates a changelog from git history.

## When to use

Before a release or version bump.

## Instructions for Claude

1. Read git log since the last tag/release: `git log $(git describe --tags --abbrev=0)..HEAD --oneline`
2. Categorize commits by conventional commit type:
   - ✨ Features (feat:)
   - 🐛 Bug fixes (fix:)
   - 📝 Documentation (docs:)
   - ♻️ Refactoring (refactor:)
   - 🔧 Chores (chore:)
   - ⚠️ Breaking changes (BREAKING CHANGE:)
3. Write human-readable descriptions (not just commit messages)
4. Prepend to CHANGELOG.md with the new version and date

## Arguments

- `--version [semver]` — specify the version number
- `--since [date|tag]` — custom start point
