# V1.0 Release Candidate Boundaries & Scope

## Overview

AIBO Assistant has successfully graduated from early MVP prototyping into a production-ready **V1.0 Release Candidate**. This document defines the verified capabilities of V1.0 and clarifies non-goals and post-V1.0 boundaries.

---

## Verified V1.0 Release Candidate Capabilities

| Capability Domain | Implemented & Verified Reality | Test Verification Evidence |
| --- | --- | --- |
| **Authentication & RBAC** | Short-lived JWTs, rotated HttpOnly refresh cookies, user profile management, session invalidation across tabs. | 343 Backend tests + E2E scenarios |
| **Task Management** | Full CRUD, soft delete, priority/status management, due-date indexing, single and multi-assignee tracking. | 343 Backend tests |
| **Project Kanban Boards** | Multi-column Kanban boards, custom column ordering, task assignment, real-time board updates. | E2E Scenario tests |
| **Time & Scheduling** | Schedule creation, conflict detection, day/week views, timezone preservation, task linkage. | E2E Scenario tests |
| **Cognitive Engine V1.0** | Multi-provider LLM Gateway (Gemini, OpenAI, Ollama), LangGraph state machine, NLU intent classification, temporal entity resolution, HMAC confirmation tokens. | 606 Engine Pytest tests |
| **Frontend Web App** | React 19 / Vite 8 SPA, full routing, Kanban boards, interactive schedule grid, AI chat drawer, Recharts dashboard. | 87 Vitest component tests |
| **Container Topology** | Multi-stage Dockerfiles, Docker Compose orchestrating Frontend, Backend, Engine, MongoDB replica set `rs0`, and Redis 7. | Docker healthchecks & preflight |

---

## Out-of-Scope for V1.0 (Deferred to V1.1 / V2.0)

To maintain architectural stability and security, the following capabilities are explicitly deferred:

1. **Unsupervised Autonomous Execution**: The AI Engine cannot execute destructive database mutations (deletions, reassignments, bulk drops) without user sign-off via HMAC confirmation tokens.
2. **Third-Party Calendar Bi-Directional Sync**: Google Calendar and Outlook integrations remain roadmap targets (V1.1+).
3. **Multi-Organization Enterprise Federation**: SAML/SSO and multi-tenant cross-organization RBAC are planned for V2.0.
4. **Direct Browser-to-Engine Access**: The AI Engine must never be directly accessible from the browser; all requests route strictly through `AIBO-BACKEND`.
