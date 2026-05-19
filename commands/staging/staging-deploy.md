# 🤖 Command: staging-deploy

> Deploys to staging environment and validates.

## Instructions for Claude

1. Run pre-deploy checks:
   - All tests pass
   - Build succeeds
   - No critical security findings
   - Migrations are ready
2. Deploy to staging:
   - Run database migrations
   - Deploy application
   - Wait for health check to pass
3. Run smoke-tests against staging URL
4. Report deployment status

## On failure

- Rollback to previous version
- Report what failed and where
- Do NOT proceed to production
