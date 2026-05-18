# Repository Relationships

## Ownership Model

| Repository | Owns | Does not own |
| --- | --- | --- |
| `.github` | Standards, templates, ADR process, shared docs, CI/CD governance, security and operational policy. | Runtime implementation or service-specific business logic. |
| `AIBO-BACKEND` | API contracts, auth/session lifecycle, validation, persistence, service orchestration, backend tests. | Browser UI, engine internals, frontend state. |
| `AIBO-FRONTEND` | User experience, route composition, browser auth behavior, API client usage, accessibility. | API authorization, database writes, refresh token storage. |
| `AIBO-ENGINE` | Intent classification, entity extraction, routing, engine schemas, deterministic pipeline. | User sessions, persistence, UI decisions, backend authorization. |

## Contract Boundaries

- Frontend calls backend through `/api/v1`.
- Backend response envelope is the API contract for frontend.
- Backend calls engine through `POST /ai-engine/analyze` for classification and
  entities, then `POST /decision-engine/decide` for backend-ready actions.
- Engine transport responses are explicit pipeline contracts and must be
  validated by consumers before use.
- Database writes are never performed by the frontend or engine.

## Shared Responsibilities

| Topic | Shared by | Rule |
| --- | --- | --- |
| Auth UX | Frontend + backend | Frontend handles browser state; backend owns security controls. |
| Intent execution | Backend + engine | Engine proposes structured actions; backend validates and executes. |
| API docs | Backend + governance | Backend owns OpenAPI source; governance owns standards. |
| Release readiness | All repos | No release claim without validation evidence. |
| Security | All repos | Secrets, tokens, logs, and user data must follow shared security policy. |

## Deployment Dependencies

- Frontend depends on backend API availability.
- Backend depends on MongoDB, PostgreSQL, and optional engine availability for assistant analysis and decisions.
- Engine can run independently for pipeline and contract tests.
- Production topology is not implemented and must be defined before deployment automation.
