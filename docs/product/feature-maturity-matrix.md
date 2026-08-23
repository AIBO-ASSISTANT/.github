# Feature Maturity Matrix

This matrix is the canonical source for implemented versus planned status. Update it in the same pull request that changes capability maturity.

## Status Definitions

| Status | Meaning |
| --- | --- |
| Implemented | Code exists, tests or validation exist, docs are aligned, and the feature can be used through its intended interface. |
| Partial | Meaningful implementation exists, but the workflow, tests, UI, docs, or production readiness are incomplete. |
| Planned | Direction is accepted, but implementation is not present. |
| Prototype | Useful experimental or deterministic baseline exists, but it is not mature enough for production claims. |
| Experimental | Exploratory work may exist and can change without compatibility promises. |

## Matrix

| Feature | Status | Evidence / notes |
| --- | --- | --- |
| Backend health endpoint | Implemented | `GET /api/v1/test`, request ID behavior, tests. |
| User signup/login | Implemented | Backend auth controller, validation, tests. |
| Browser refresh sessions | Implemented | HttpOnly cookie refresh, rotation, revocation, trusted browser headers. |
| Frontend auth lifecycle | Partial | In-memory access token, refresh helper, tab sync, login/signup screens; complete product integration still needed. |
| Task API | Implemented | CRUD-like task workflow, soft delete/restore, filtering, validation, tests. |
| Schedule API | Implemented / partial | Backend scheduling with conflict handling and tests; frontend schedule UI not complete. |
| Project management API | Implemented / partial | PostgreSQL project, column, task, assignment, ordering, and activity tests; frontend project UI not complete. |
| Dashboard | Partial | Frontend protected route and page scaffold exist; insight capabilities are not complete. |
| Chatbot interface | Planned | No complete frontend chatbot flow is present. |
| Intent classification endpoint | Implemented / prototype | Backend integrates with engine transport; engine classifier is deterministic. |
| Entity extraction | Prototype | Engine extracts supported fields from deterministic patterns. |
| Decision routing | Prototype | Engine maps supported intents to action candidates or clarification. |
| OpenAI/LLM integration | Planned | Not implemented. Do not claim active OpenAI usage. |
| Engine evaluation harness | Planned | Unit tests exist; formal evaluation corpus and metrics do not. |
| OpenAPI governance | Partial | Backend has `docs/openapi.yaml`; coverage needs alignment with all implemented routes. |
| CI workflows | Planned foundation | Reusable workflow templates are added here; service repos must adopt them. |
| Deployment automation | Planned | No production target or deploy workflow exists. |
| Structured logging | Partial | Backend has structured logs and redaction utilities; centralized logging is not implemented. |
| Metrics and tracing | Planned | No metrics or tracing stack is implemented. |
| Incident response process | Planned foundation | Governance docs exist; operational tooling does not. |
| Backup/recovery process | Planned | Philosophy documented; no tested backup automation. |
