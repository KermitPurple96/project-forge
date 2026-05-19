# 🤖 Command: dependency-audit

> Reviews project dependencies for security, licensing, and maintenance risks.

## When to use

Monthly. Before releases. When adding new dependencies.

## Instructions for Claude

1. Run security audit: `npm audit` / `pip audit` / `cargo audit`
2. Check for:
   - Known vulnerabilities (CVEs)
   - Outdated packages (major versions behind)
   - Abandoned packages (no updates in 2+ years, archived repos)
   - License incompatibilities (GPL in MIT project, etc.)
   - Duplicate packages (different versions of same lib)
   - Unnecessary dependencies (only used in one place, could be replaced)
3. For each issue, recommend: update, replace, or remove
4. Estimate effort for each fix

## Output

| Package | Issue | Severity | Recommendation |
|---------|-------|----------|----------------|
