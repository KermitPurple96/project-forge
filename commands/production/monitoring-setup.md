# 🔀 Command: monitoring-setup

> Configures monitoring, alerting, and observability.

## Instructions for Claude

1. Read `stack-selection.md` for monitoring tools
2. Set up:
   - **Uptime monitoring:** Health check endpoint polled every 60s
   - **Error tracking:** Sentry or equivalent, captures unhandled exceptions
   - **Performance:** Response times, DB query times, queue depths
   - **Resource:** CPU, memory, disk usage
   - **Business metrics:** Signups, active users, core actions per hour
3. Configure alerts:
   - 🔴 Critical: downtime, error rate >5%, DB connection lost
   - 🟡 Warning: error rate >1%, response time >2s, disk >80%
   - 🔵 Info: deploy completed, daily summary
4. Set up dashboard with key metrics
5. Document where to look when things go wrong
