# 🤖 Command: version-bump

> Increments version, updates changelog, creates release.

## Instructions for Claude

1. Determine version increment based on changes since last release:
   - **Patch** (1.0.X): bug fixes only
   - **Minor** (1.X.0): new features, backwards compatible
   - **Major** (X.0.0): breaking changes
2. Run `changelog` command to generate changelog
3. Update version in: package.json, pyproject.toml, Cargo.toml (whatever applies)
4. Commit: `chore: bump version to vX.Y.Z`
5. Create git tag: `git tag vX.Y.Z`
6. Create GitHub release with changelog as body

## Arguments

- `--patch` / `--minor` / `--major` — override auto-detection
