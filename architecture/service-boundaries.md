# Service Boundaries

## Frontend Boundary

`AIBO-FRONTEND` owns:

- UI routes and page composition.
- Browser-only auth lifecycle.
- In-memory access token handling.
- API client behavior and user-visible error states.
- Accessibility and interaction quality.

It must not:

- store refresh tokens
- construct database queries
- bypass backend authorization
- call databases directly
- treat planned backend endpoints as implemented

## Backend Boundary

`AIBO-BACKEND` owns:

- `/api/v1` HTTP API.
- auth, refresh rotation, logout, and current-user behavior.
- API validation and response envelopes.
- MongoDB and PostgreSQL data access.
- orchestration of engine calls.
- request logging, request IDs, rate limiting, security middleware.

It must not:

- embed frontend UI decisions
- implement engine internals inside controllers
- accept unvalidated engine payloads
- move data ownership without an ADR

## Engine Boundary

`AIBO-ENGINE` owns:

- deterministic intent classification.
- entity extraction.
- decision routing.
- canonical engine schemas.
- transport adapter for service-to-service classification.

It must not:

- authenticate users
- write application data
- decide backend authorization
- claim LLM behavior without implementation and evaluation

## Governance Boundary

`.github` owns cross-repo policy. It must not become a dumping ground for service-specific implementation details that belong in service repos.
