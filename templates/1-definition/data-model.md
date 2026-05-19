# 🔀 Data Model

> Define your entities and relationships. Claude can generate the ERD and migrations from this.

## Entities

### User

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| id | UUID | ✅ | Primary key |
| email | string | ✅ | Unique |
| password_hash | string | ✅ | Bcrypt |
| name | string | ✅ | |
| role | enum | ✅ | admin, user, guest |
| created_at | timestamp | ✅ | |
| updated_at | timestamp | ✅ | |

### [Entity 2]

| Field | Type | Required | Notes |
|-------|------|----------|-------|
|       |      |          |       |

<!-- Repeat for each entity -->

## Relationships

| From | To | Type | Description |
|------|----|------|-------------|
| User | Post | 1:N | A user has many posts |
|      |      |      |             |

## Indexes

| Table | Fields | Type | Why |
|-------|--------|------|-----|
| users | email | unique | Login lookup |
|       |        |        |     |
