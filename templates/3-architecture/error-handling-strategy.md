# 🔀 Error Handling Strategy

## Error classification

| Level | When | Example | User sees | Logged |
|-------|------|---------|-----------|--------|
| Fatal | App can't continue | DB connection lost | Error page | ✅ + alert |
| Error | Action failed | Save failed | Toast/inline error | ✅ |
| Warning | Degraded but working | Cache miss, slow query | Nothing | ✅ |
| Info | Normal events | User login, API call | Nothing | Optional |

## User-facing error format

```
"Something went wrong while saving your changes. Please try again."
```

Rules:
- Never expose stack traces, SQL, or internal details to the user
- Always suggest a next action ("try again", "contact support")
- Use human language, not error codes

## API error format

```json
{
  "error": {
    "code": "RESOURCE_NOT_FOUND",
    "message": "The requested item does not exist",
    "status": 404
  }
}
```

## Logging

- **Tool:** <!-- Sentry, Datadog, console, Pino, Winston -->
- **Structured logging:** yes / no
- **Log levels used:** error, warn, info, debug
- **PII in logs:** <!-- never / masked / only in debug -->

## Retry strategy

<!-- Which operations should retry? How many times? Backoff? -->

