# 🤖 Command: seed-data

> Generates realistic test data for development and testing.

## When to use

After scaffold or db-migrate, or when you need fresh test data.

## Instructions for Claude

1. Read `data-model.md` for entities and relationships
2. Read `user-roles.md` for role types
3. Generate seed data:
   - At least 1 user per role
   - 10-50 records per main entity (configurable)
   - Realistic names, emails, dates (not "test1", "foo", "bar")
   - Respect all constraints and relationships
   - Include edge cases (empty fields where optional, max-length strings)
4. Write seed script in the project's language/ORM
5. Run the seed and verify data is correctly inserted

## Arguments

- `--count [N]` — records per entity (default: 20)
- `--clean` — wipe existing data before seeding
