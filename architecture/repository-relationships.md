# Repository Relationships & Integration Contracts

This document formalizes the multi-repository contract interaction model, interface dependencies, and data-flow guarantees across the AIBO Assistant ecosystem.

---

## 1. Multi-Repository Ownership Matrix

| Repository | Owns | Does NOT Own |
| :--- | :--- | :--- |
| **`.github`** | Ecosystem standards, community health, architecture specs, ADR process, CI/CD templates, runbooks, and onboarding workflows. | Service runtime code, tests, or database schemas. |
| **`AIBO-BACKEND`** | API contracts (`/api/v1/*`), authentication/session lifecycle, MongoDB persistence, Redis caching/queues, durable confirmation atomic claim, and service orchestration. | Browser UI presentation, cognitive NLU logic, LLM gateway prompts. |
| **`AIBO-FRONTEND`** | Web application, route composition, user experience, in-memory token management, theme design system, and WebSocket client subscriptions. | Database writes, API authorization, refresh token cookie manipulation. |
| **`AIBO-ENGINE-V1.0`** | Intent classification, entity extraction, planning, action authorization policies, multi-provider LLM gateway, and confirmation token synthesis. | Persistent databases, user sessions, direct DB mutations, authentication issuance. |

---

## 2. Cross-Repository Integration Contracts

```
┌─────────────────┐                      ┌─────────────────┐                      ┌──────────────────┐
│  AIBO-FRONTEND  │                      │   AIBO-BACKEND  │                      │ AIBO-ENGINE-V1.0 │
└────────┬────────┘                      └────────┬────────┘                      └────────┬─────────┘
         │                                        │                                        │
         │ 1. POST /api/v1/engine/orchestrate     │                                        │
         ├───────────────────────────────────────►│                                        │
         │    (Bearer JWT + userMessage)          │ 2. POST /orchestrate                   │
         │                                        ├───────────────────────────────────────►│
         │                                        │    (Header: x-engine-secret,           │
         │                                        │     x-request-id, monotonic budget)    │
         │                                        │                                        │ 3. NLU, Plan, Auth
         │                                        │ 4. OrchestrationResult                 │
         │                                        │◄───────────────────────────────────────┤
         │                                        │    (status: awaiting_confirmation,     │
         │                                        │     confirmation.token: HMAC-SHA256)   │
         │ 5. Response Envelope                   │                                        │
         │◄───────────────────────────────────────┤                                        │
         │                                        │                                        │
         │ 6. POST /api/v1/engine/orchestrate     │                                        │
         │    (confirmation: {decision: confirm,  │                                        │
         │                    token: ...})        │ 7. Atomic Claim Pending State in Mongo │
         ├───────────────────────────────────────►│    (Transition: AWAITING -> EXECUTING) │
         │                                        │                                        │
         │                                        │ 8. POST /orchestrate (Execution Turn)  │
         │                                        ├───────────────────────────────────────►│
         │                                        │                                        │ 9. Execute Actions
         │                                        │ 10. Backend Client Callbacks           │
         │                                        │◄───────────────────────────────────────┤
         │                                        │     (POST /tasks, POST /schedules)     │
         │                                        │                                        │
         │                                        │ 11. Final Orchestration Result         │
         │                                        │◄───────────────────────────────────────┤
         │ 12. Final Execution Result             │                                        │
         │◄───────────────────────────────────────┤                                        │
```

### 2.1 Frontend ↔ Backend Contract
- **Protocol**: HTTPS / REST on port 5000, WebSockets (Socket.io) on port 5000.
- **Payload Format**: Standard API envelopes:
  ```json
  {
    "success": true,
    "data": { ... },
    "meta": { "timestamp": "...", "requestId": "..." }
  }
  ```
- **Authentication**: `Authorization: Bearer <access_token>` in request headers. Refresh token delivered automatically via secure `HttpOnly` cookie.

### 2.2 Backend ↔ Engine Contract
- **Protocol**: HTTP/1.1 on internal private network (Port 5001).
- **Authentication**: Required header `x-engine-secret` matching `ENGINE_SECRET`. Direct unauthenticated requests are rejected with HTTP 401.
- **Canonical Route**: `POST /orchestrate`
  - Accepts `OrchestrationRequest`: `user_message`, `conversation_id`, `project_id`, `reference_date`, `conversation_history`, `confirmation`.
  - Returns `OrchestrationResult`: `status` (`completed`, `awaiting_confirmation`, `needs_clarification`, `execution_failed`, `cancelled`), `response_text`, `proposed_actions`, `confirmation`, `latency_ms`.
- **Legacy Fallback Routes**: `POST /process` and `POST /respond` for backward compatibility during phased rollouts.
- **Notification Route**: `POST /notification/scan` for scheduled proactive task and schedule analysis.

---

## 3. Shared Operational Responsibilities

| Responsibility Area | Involving Repositories | Standard Invariant |
| :--- | :--- | :--- |
| **Authentication & Session** | Frontend + Backend | Frontend holds access token in memory only; backend rotates refresh tokens and enforces revocation. |
| **Cognitive Interaction** | Backend + Engine | Engine is the brain; backend is the authoritative effector. Zero direct DB access in engine. |
| **Durable Confirmation** | Backend + Engine | Engine issues signed HMAC-SHA256 tokens; backend persists and atomically claims confirmation records in MongoDB. |
| **Observability & Correlation** | All Repositories | `x-request-id` and `x-correlation-id` are propagated through Frontend -> Backend -> Engine -> Database queries. |
| **Failure Containment** | All Repositories | Timeouts and errors fail closed with zero database mutations. |

---

## 4. Deployment Dependencies

1. **MongoDB (Replica Set `rs0`)**: Must be healthy before Backend starts (required for Mongoose multi-document transactions).
2. **Redis (v7+)**: Must be healthy before Backend starts (required for BullMQ queue workers and rate limiters).
3. **Engine (`AIBO-ENGINE-V1.0`)**: Must pass `/ready` probe before Backend routes cognitive traffic.
4. **Backend (`AIBO-BACKEND`)**: Must pass `/api/v1/health/ready` probe before Frontend proxies requests.
5. **Frontend (`AIBO-FRONTEND`)**: Serves browser client on port 8080 and proxies `/api` to backend.
