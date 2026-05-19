# 🔀 Command: db-migration-plan

> Plans database migrations for environments with existing data.

## Instructions for Claude

1. Compare current schema with target schema (from data-model.md)
2. For each change, assess:
   - Is it additive (safe) or destructive (dangerous)?
   - Does it require data transformation?
   - Can it be done with zero downtime?
3. Generate migration plan:
   - Order of operations
   - Estimated duration
   - Rollback script for each step
   - Data backup requirements
4. Flag any change that:
   - Drops a column/table
   - Changes a column type
   - Adds a NOT NULL column without default
   - Touches >100K rows

## Output

Step-by-step migration plan with rollback scripts. Requires user approval before execution.
