# 🤖 Command: prod-deploy

> Deploys to production with safety nets.

## Instructions for Claude

1. Pre-flight:
   - Verify staging-deploy was successful
   - Verify pre-launch-checklist is complete
   - Confirm with user: "Ready to deploy to production?"
2. Deploy sequence:
   - Tag release: `git tag v[version]`
   - Run database migrations (with backup first)
   - Deploy application
   - Wait for health check (30s timeout)
   - Run smoke-tests against production URL
3. Post-deploy:
   - Monitor error rate for 5 minutes
   - If error rate spikes, auto-rollback
   - Notify team (Slack, email, etc.)

## On failure

Execute `rollback` command immediately.
