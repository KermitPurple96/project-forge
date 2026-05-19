# 🔀 Command: incident-runbook

> Generates playbooks for common production incidents.

## Instructions for Claude

Generate runbooks for each scenario:

### App is down (5xx errors)
1. Check health endpoint manually
2. Check server logs for errors
3. Check if recent deploy (rollback if yes)
4. Check database connection
5. Check memory/CPU usage
6. Restart application
7. If persists, scale up / failover

### Database connection issues
1. Check DB server status
2. Check connection pool (max connections exceeded?)
3. Check for long-running queries
4. Check disk space on DB server
5. Restart connection pool
6. If data loss suspected, stop writes and assess

### Auth is broken
1. Check JWT secret is set
2. Check OAuth provider status
3. Check certificate expiry
4. Review recent auth-related code changes

### Performance degradation
1. Check monitoring dashboard for anomalies
2. Identify slow endpoints (APM)
3. Check for N+1 queries
4. Check cache hit rate
5. Check for traffic spike
6. Scale horizontally if needed

### Security incident
1. Assess scope (what data, how many users)
2. Block the attack vector
3. Preserve evidence (don't delete logs)
4. Notify affected users (if personal data)
5. Post-mortem after resolution

For each runbook, include: who to contact, escalation path, communication template.
