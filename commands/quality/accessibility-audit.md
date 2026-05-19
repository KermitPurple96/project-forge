# 🤖 Command: accessibility-audit

> Checks the codebase against accessibility standards.

## When to use

Before launch. After UI changes. Periodically.

## Instructions for Claude

1. Read `accessibility-spec.md` for target WCAG level
2. Scan all components/pages for:
   - Images without `alt` text
   - Form inputs without `<label>` or `aria-label`
   - Buttons/links without accessible text (icon-only without aria-label)
   - Missing ARIA landmarks (main, nav, aside, etc.)
   - Color contrast violations (check design-tokens.md values)
   - Missing focus styles / keyboard navigation broken
   - Dynamic content not announced (missing aria-live)
   - Heading hierarchy (h1 → h2 → h3, no skipping)
   - Click handlers on non-interactive elements (divs, spans)
3. Generate fix for each issue found
4. Run automated check if axe-core or similar is installed

## Output

Report with: issue, WCAG criterion violated, file, line, fix.
