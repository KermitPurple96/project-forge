# 🤖 Command: tech-debt-audit

> Scans the codebase for accumulated technical debt.

## Instructions for Claude

1. Scan for:
   - `TODO`, `FIXME`, `HACK`, `XXX`, `TEMP` comments
   - Duplicated code blocks (>10 lines appearing 3+ times)
   - Files >500 lines
   - Functions >100 lines
   - Cyclomatic complexity >10
   - Outdated dependencies (>2 major versions behind)
   - Dead code (unused exports, unreachable branches)
   - Inconsistent patterns (old vs new way of doing same thing)
   - Missing error handling
   - Hardcoded values that should be config
2. Categorize by:
   - **Impact:** How much does it slow development?
   - **Risk:** Could it cause bugs or outages?
   - **Effort:** How long to fix? (hours)
3. Generate prioritized list: high impact + low effort first
4. Estimate total debt in developer-hours
