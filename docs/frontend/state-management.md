# Frontend State Management

## Current Standard

Use React state and focused context providers until a feature proves it needs more.

## Auth State

- Access tokens live in memory only.
- Refresh tokens are never stored in frontend-accessible storage.
- Session restore uses the backend refresh cookie through `POST /auth/refresh`.
- Sign-out clears local state before attempting backend revocation.
- Cross-tab sign-out should continue to use broadcast/storage synchronization.

## Server State

- Keep API access behind service modules.
- Do not duplicate backend validation rules as a source of truth, but provide user-friendly client validation where helpful.
- Avoid global caches until data sharing and invalidation needs are clear.

## Introducing a State Library

Add a state library only when:

- multiple distant routes need shared mutable client state
- server state caching becomes necessary
- the repository has documented conventions for usage
- tests cover critical state transitions
