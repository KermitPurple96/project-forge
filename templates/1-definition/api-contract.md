# 🔀 API Contract

> Define your API endpoints. Claude can generate OpenAPI spec from this.

## Base URL

- **Development:** `http://localhost:3000/api`
- **Staging:** `https://staging.yourapp.com/api`
- **Production:** `https://yourapp.com/api`

## Authentication

<!-- How are requests authenticated? Bearer token, API key, session cookie? -->

```
Authorization: Bearer <jwt_token>
```

## Endpoints

### Auth

| Method | Endpoint | Description | Auth required |
|--------|----------|-------------|--------------|
| POST | /auth/register | Create new account | ❌ |
| POST | /auth/login | Login, returns JWT | ❌ |
| POST | /auth/refresh | Refresh token | ✅ |
| POST | /auth/logout | Invalidate token | ✅ |

### [Resource name]

| Method | Endpoint | Description | Auth required |
|--------|----------|-------------|--------------|
| GET | /resource | List all | ✅ |
| GET | /resource/:id | Get one | ✅ |
| POST | /resource | Create | ✅ |
| PUT | /resource/:id | Update | ✅ |
| DELETE | /resource/:id | Delete | ✅ |

## Error format

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Human-readable message",
    "details": {}
  }
}
```

## Status codes

| Code | Meaning |
|------|---------|
| 200 | Success |
| 201 | Created |
| 400 | Bad request / validation error |
| 401 | Not authenticated |
| 403 | Not authorized |
| 404 | Not found |
| 429 | Rate limited |
| 500 | Server error |
