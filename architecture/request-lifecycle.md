# Request Lifecycle

```mermaid
sequenceDiagram
  participant Browser
  participant Frontend
  participant Backend
  participant DataStore as MongoDB/PostgreSQL
  Browser->>Frontend: User action
  Frontend->>Backend: HTTP request to /api/v1
  Backend->>Backend: request ID, logging, security middleware
  Backend->>Backend: auth and validation
  Backend->>DataStore: read/write through owned model/service
  DataStore-->>Backend: result
  Backend-->>Frontend: success/error envelope + requestId
  Frontend-->>Browser: UI update or error state
```

## Backend Request Rules

- Every response should include `requestId`.
- Validation failures must be structured.
- Authorization must happen before user-specific data access.
- Controllers should delegate business logic to services.
- Errors should be normalized through the global error handler.

## Frontend Request Rules

- Use the shared API client instead of raw Axios from components.
- Preserve `withCredentials` for browser refresh cookie behavior.
- Attach access tokens from memory only.
- Show recoverable errors without exposing internal details.
