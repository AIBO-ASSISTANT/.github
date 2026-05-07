# Validation and Error Standards

## Standard Error Shape

```json
{
  "success": false,
  "message": "Validation failed",
  "data": null,
  "requestId": "req-id",
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Validation failed",
    "details": [
      {
        "field": "email",
        "message": "Invalid email"
      }
    ]
  }
}
```

## Validation Policy

- Validate request body, params, query, headers when they influence behavior.
- Reject unknown dangerous fields.
- Cap pagination and input lengths.
- Return field-specific details where useful.
- Do not leak internal stack traces or database implementation details.

## Error Code Policy

- Codes must be stable and uppercase snake case.
- Codes should identify category and failure, not internal class names.
- Security-sensitive errors should avoid revealing resource existence.

## Response Status Policy

| Status | Use |
| --- | --- |
| `400` | invalid syntax, validation, malformed request |
| `401` | missing or invalid authentication |
| `403` | authenticated but not authorized |
| `404` | resource or route not found |
| `409` | conflict, duplicate, stale write, invalid state transition |
| `429` | rate limit exceeded |
| `500` | server failure |
| `503` | dependency unavailable or timed out |
