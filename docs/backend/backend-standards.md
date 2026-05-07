# Backend Standards

`AIBO-BACKEND` owns the API boundary, auth/session lifecycle, validation, persistence, and engine orchestration.

## API Conventions

- All public service routes live under `/api/v1`.
- Responses should use the standard envelope:

```json
{
  "success": true,
  "message": "Operation completed",
  "data": {},
  "requestId": "..."
}
```

- Errors should use:

```json
{
  "success": false,
  "message": "Validation failed",
  "data": null,
  "requestId": "...",
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Validation failed",
    "details": []
  }
}
```

## Validation Standards

- Validate params, query, and body at the route boundary.
- Reject unknown dangerous fields.
- Keep validation error details field-oriented.
- Validate external service responses, including engine responses.

## Error Handling

- Throw operational errors with explicit status and code.
- Let the global error handler normalize responses.
- Avoid leaking internal exception details to clients.
- Include request IDs for correlation.

## Database Standards

- MongoDB owns users, sessions, tasks, and schedules.
- PostgreSQL owns project management data.
- Do not duplicate user data across PostgreSQL without an ADR.
- Migrations must preserve data integrity and include rollback notes when possible.
- Write operations must be authorization scoped.

## Security Standards

- Browser refresh tokens remain HttpOnly cookies.
- Access tokens remain short lived.
- Production CORS cannot be wildcard.
- Use rate limiters for auth and write-heavy routes.
- Redact tokens, cookies, passwords, authorization headers, and sensitive session fields from logs.
