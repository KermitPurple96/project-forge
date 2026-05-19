# 🤖 Command: rollback

> Reverts to the previous stable version.

## Instructions for Claude

1. Identify current version and previous stable version
2. Rollback sequence:
   - Deploy previous version of application
   - Rollback database migration (if applicable and safe)
   - Verify health check passes
   - Run smoke-tests
3. Post-rollback:
   - Notify team
   - Document what went wrong
   - Do NOT attempt the failed deploy again without a fix
4. If database migration can't be rolled back:
   - The new code must handle both old and new schema
   - Flag this to the user immediately
