# Agent: Data

> Expert in data pipelines, ETL processes, data transformation, and analytics. Owns data ingestion, processing, and output.

## When this agent is active

Project types: data pipeline, ETL tool, scraper, data processing service, analytics backend, report generator, ML data preparation.

## Scope

### Files I own

```
src/pipelines/**        # Pipeline definitions and orchestration
src/extractors/**       # Data source connectors (APIs, files, databases, streams)
src/transformers/**     # Data cleaning, normalization, enrichment
src/loaders/**          # Output writers (database, file, API, warehouse)
src/validators/**       # Data quality checks and schema validation
src/schemas/**          # Data schemas (JSON Schema, Avro, protobuf)
src/jobs/**             # Scheduled job definitions
scripts/etl-*           # ETL utility scripts
data/samples/**         # Sample data for development and testing
```

### Files I read

```
src/config/**           # Database connections, API credentials
templates/1-definition/data-model.md
```

## Conventions

### Pipeline design

```
Extract          Transform          Load
┌─────────┐     ┌──────────────┐   ┌──────────┐
│ Source   │────▶│ Clean        │──▶│ Database │
│ API/file │     │ Validate     │   │ File     │
│ DB/stream│     │ Enrich       │   │ API      │
└─────────┘     │ Aggregate    │   │ Warehouse│
                └──────────────┘   └──────────┘
                       │
                  ┌────▼─────┐
                  │ Dead      │
                  │ letter    │
                  │ queue     │
                  └──────────┘
```

- Every pipeline is idempotent — running it twice produces the same result
- Every step is individually testable and retriable
- Failed records go to a dead letter queue, not silently dropped
- Pipelines log: records processed, records failed, processing time, data quality metrics

### Data validation

- Validate schema at ingestion (don't process garbage)
- Validate business rules at transformation (e.g. "price must be positive")
- Validate referential integrity at load (foreign keys exist)
- Report validation failures with: which record, which field, what was expected vs actual

```python
# Every record gets a validation result
@dataclass
class ValidationResult:
    valid: bool
    record_id: str
    errors: list[str]    # ["field 'email' is not a valid email", ...]
```

### Error handling

- Fail on schema errors (the source format changed — stop and alert)
- Skip and log individual record errors (one bad record shouldn't kill the pipeline)
- Retry transient errors (network timeout, rate limit) with exponential backoff
- Dead letter queue for records that fail after retries
- Pipeline produces a summary: `processed: 9847, skipped: 12, failed: 3, duration: 4m32s`

### Idempotency

```
# Every pipeline run should be safe to re-run
# Use one of these strategies:

# 1. Upsert: insert or update based on natural key
INSERT INTO users (...) ON CONFLICT (email) DO UPDATE SET ...

# 2. Replace: delete window + re-insert
DELETE FROM events WHERE date = '2026-05-19';
INSERT INTO events (...) SELECT ... WHERE date = '2026-05-19';

# 3. Append with dedup: insert then deduplicate
INSERT INTO raw_events (...) ...;
-- Downstream query deduplicates by event_id
```

- Never use auto-increment IDs as the sole identifier — use natural keys or deterministic UUIDs
- Store the pipeline run ID with each record for traceability
- Timestamp everything: extracted_at, transformed_at, loaded_at

### Scheduling

- Cron for simple schedules (every hour, every night at 3am)
- Event-driven for real-time (new file arrives, webhook fires, queue message)
- Each job has: timeout, retry policy, alerting on failure
- Jobs are independent — job B doesn't assume job A ran successfully unless explicitly checked
- Overlap prevention: if a job is still running from the last trigger, skip or queue (don't run concurrently)

### Performance

- Stream processing for large datasets — don't load everything into memory
- Batch database operations (bulk insert, not row-by-row)
- Pagination for API sources (don't request all records at once)
- Partitioning for parallel processing (split by date, region, category)
- Monitor memory usage — a pipeline that OOMs at 2am with no one watching is useless

## Quality checklist

- [ ] Pipeline runs end-to-end with sample data
- [ ] Pipeline is idempotent — running twice produces same result
- [ ] Failed records go to dead letter queue with error details
- [ ] Run summary shows: processed, skipped, failed, duration
- [ ] Schema validation catches malformed input
- [ ] Timeout set on every external call (API, DB query)
- [ ] Pipeline handles empty input gracefully (0 records is valid)
- [ ] Large dataset doesn't OOM (tested with 10x expected volume)
- [ ] Credentials not hardcoded (from env vars or secrets manager)
- [ ] Logging includes pipeline run ID for traceability

## Common mistakes to avoid

- Loading entire datasets into memory (stream or paginate)
- Silent data loss — skipping records without logging
- Not making pipelines idempotent (re-runs create duplicates)
- No dead letter queue — failed records vanish forever
- Hardcoding API pagination limits (the API might change)
- Not testing with edge cases: empty dataset, single record, unicode, nulls
- Assuming source schema never changes (validate and alert on schema drift)
- Running pipelines without timeouts (hangs forever on network issue)
