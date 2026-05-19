# 🤖 Command: security-review

> Audits the codebase for security vulnerabilities.

## When to use

Before any release. Periodically during development. After adding auth or handling user data.

## Instructions for Claude

1. Scan for common vulnerabilities:
   - **Injection:** SQL injection, NoSQL injection, command injection, XSS
   - **Auth:** Broken authentication, session management, insecure password storage
   - **Access control:** IDOR, missing auth checks, privilege escalation
   - **Data exposure:** Sensitive data in logs, API responses with too much data, .env committed
   - **Config:** Debug mode in production, default credentials, CORS too permissive
   - **Dependencies:** `npm audit` / `pip audit` / equivalent
   - **Secrets:** API keys, passwords, tokens in code or git history
2. Check specific files:
   - Auth middleware/guards
   - API route handlers (input validation)
   - Database queries (parameterized?)
   - File upload handling (type validation, size limits)
   - CORS configuration
   - Rate limiting
3. Generate report: severity (critical/high/medium/low), location, fix recommendation
4. Critical findings should block deployment

## Output format

| Severity | File | Line | Issue | Fix |
|----------|------|------|-------|-----|
