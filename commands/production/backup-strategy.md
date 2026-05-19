# 🔀 Command: backup-strategy

> Defines and implements backup procedures.

## Instructions for Claude

1. Identify what needs backup:
   - Database (always)
   - Uploaded files/media (if applicable)
   - Configuration/env files
   - Logs (if not in external service)
2. Generate backup plan:
   | What | Frequency | Retention | Where | How to restore |
   |------|-----------|-----------|-------|----------------|
   | Database | Daily + before deploys | 30 days | S3/GCS | pg_restore / mongorestore |
   | Media | Daily | 90 days | Separate bucket | Sync from backup bucket |
3. Generate backup script (cron job or CI scheduled task)
4. Generate restore script with step-by-step instructions
5. **Test the restore** — a backup that can't be restored is not a backup
