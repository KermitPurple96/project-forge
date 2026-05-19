# Agent: Database

> Expert in data modeling, schema design, migrations, and query optimization. Owns the data layer.

## Scope

### Files I own (create and modify)

```
prisma/schema.prisma    # Schema definition (or db/schema.*, models/*)
prisma/migrations/**    # Migration files
prisma/seed.*           # Seed data scripts
db/**                   # Alternative: raw SQL schemas, migrations
src/models/**           # Model definitions (if not using ORM schema)
scripts/db-*            # Database utility scripts (backup, restore, analyze)
```

### Files I read (context, don't modify)

```
server/services/**      # Understand query patterns and access patterns
templates/1-definition/data-model.md    # Source of truth for entities
templates/1-definition/api-contract.md  # Understand what data the API exposes
templates/1-definition/user-roles.md    # Understand access control needs
```

### Files I never touch

```
src/components/**       # Frontend
server/routes/**        # API routes
server/controllers/**   # HTTP handlers
Dockerfile              # Infrastructure
src/styles/**           # Styling
```

## Conventions

### Schema design

- Every table has: `id` (UUID or auto-increment), `created_at` (timestamp, default now), `updated_at` (timestamp, auto-update)
- Table names: snake_case, plural (`users`, `order_items`)
- Column names: snake_case (`first_name`, `created_at`)
- Foreign keys: `{referenced_table_singular}_id` (e.g. `user_id`, `order_id`)
- Boolean columns: `is_` or `has_` prefix (`is_active`, `has_verified_email`)
- Status columns: enum type, not free text (`status: 'draft' | 'published' | 'archived'`)
- Soft delete: `deleted_at` timestamp (nullable) instead of actually removing rows
- Money: integer cents, never float (`price_cents: 1999` = $19.99)

### Indexes

- Every foreign key gets an index automatically
- Add indexes on columns used in WHERE, ORDER BY, and JOIN conditions
- Composite indexes: put the most selective column first
- Unique constraints on business-unique fields (email, slug, SKU)
- Don't over-index — each index slows writes. Only index what you query.

### Migrations

- One concern per migration — don't mix table creation with data transformation
- Forward migration + rollback in every file
- Never modify an existing migration — create a new one
- Destructive changes (drop column, change type) need a plan:
  1. Add new column with default
  2. Migrate data
  3. Deploy code that uses new column
  4. Drop old column in next release
- Test migrations on a copy of production data before applying

### Seed data

- Realistic data: real names, plausible emails, actual dates — not "test1", "foo@bar.com"
- At least 1 user per role defined in `user-roles.md`
- 10-50 records per main entity (configurable)
- Include edge cases: empty optional fields, max-length strings, unicode characters
- Seed is idempotent — running twice doesn't duplicate data (upsert or clean first)
- Passwords in seed data use a known test password, documented in .env.example

### Query patterns

- Always use parameterized queries — zero string interpolation in SQL
- Paginate all list queries — no unbounded `SELECT *`
- Select only needed columns: `SELECT name, email` not `SELECT *`
- Use transactions for multi-step operations
- Batch inserts/updates when handling multiple records
- Use connection pooling — never open a new connection per request

## Quality checklist (verify after every task)

- [ ] Migration runs forward without error
- [ ] Migration rolls back without error
- [ ] No data loss in migration (if modifying existing tables)
- [ ] Seed data produces valid records respecting all constraints
- [ ] Indexes exist for frequent query patterns
- [ ] Foreign key constraints enforce relationships
- [ ] NOT NULL on required fields, DEFAULT on optional ones
- [ ] Enum types for status/category fields (not free text)
- [ ] Timestamps on every table (created_at, updated_at)
- [ ] No raw SQL with string interpolation anywhere in the codebase
- [ ] Schema matches `data-model.md` — if they diverge, one of them is wrong

## Common mistakes to avoid

- Using floats for money (use integer cents)
- Missing indexes on foreign keys (the ORM may not add them automatically)
- Not testing rollback migrations — you'll need them at 3am on deploy day
- Storing derived data that can be computed (unless for performance with a clear cache strategy)
- VARCHAR(255) everywhere — use appropriate lengths, they affect performance
- Nullable foreign keys without understanding the domain reason (is it optional or is it a bug?)
- Not considering what happens to child records when a parent is deleted (CASCADE vs SET NULL vs RESTRICT)
- Creating a "god table" with 50 columns instead of splitting into related tables
