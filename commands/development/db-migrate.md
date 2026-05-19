# 🤖 Command: db-migrate

> Generates and runs database migrations when the data model changes.

## When to use

After modifying `data-model.md` or when a task requires schema changes.

## Instructions for Claude

1. Compare current DB schema with `data-model.md`
2. Generate migration file with the diff
3. Review the migration for:
   - Data loss risk (dropping columns/tables)
   - Default values for new required fields
   - Index additions/removals
4. Run migration on development database
5. Verify with a quick query that the schema is correct
6. Update seed data if affected

## Safety checks

- NEVER run destructive migrations without explicit user confirmation
- Always generate a rollback migration alongside the forward migration
- Flag any migration that touches >1000 rows with a warning
