# Auth Lifecycle

```mermaid
sequenceDiagram
  participant Browser
  participant Frontend
  participant Backend
  participant SessionStore as MongoDB sessions
  Browser->>Frontend: Login/signup form
  Frontend->>Backend: POST /api/v1/auth/login or signup
  Backend->>SessionStore: create refresh session
  Backend-->>Frontend: access token JSON + HttpOnly refresh cookie
  Frontend->>Frontend: store access token in memory
  Frontend->>Backend: API call with Authorization bearer
  Backend-->>Frontend: response
  Frontend->>Backend: POST /api/v1/auth/refresh with cookie
  Backend->>SessionStore: rotate refresh session
  Backend-->>Frontend: new access token + rotated cookie
  Frontend->>Backend: POST /api/v1/auth/logout
  Backend->>SessionStore: revoke session
  Backend-->>Frontend: cleared cookie
```

## Implemented Rules

- Refresh token is stored in an HttpOnly cookie for browser clients.
- Access token is returned in JSON and stored in frontend memory.
- Refresh and logout require browser client expectations.
- Backend rotates refresh sessions and revokes logout sessions.
- Frontend clears local auth state on auth failure.

## Required Controls

- Production must use HTTPS.
- `AUTH_COOKIE_SAME_SITE=none` requires `AUTH_COOKIE_SECURE=true`.
- Wildcard CORS is not allowed in production.
- Refresh tokens must not be accepted from browser request bodies.
- Logs must redact cookies, tokens, authorization headers, and session secrets.
