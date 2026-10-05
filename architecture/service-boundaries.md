# AIBO Assistant — Service Boundaries & Trust Zones

This document establishes the authoritative responsibility boundaries, trust zones, and operational invariants for each component in the AIBO ecosystem.

---

## 1. Trust Hierarchy & Network Topology

```
[ Public Internet ]
        │
        ▼ (Port 8080)
┌────────────────────────────────────────────────────────┐
│ PUBLIC TRUST ZONE: AIBO-FRONTEND (Client Browser)      │
│ - Untrusted execution environment                      │
│ - In-memory tokens only                                │
└───────────────────────┬────────────────────────────────┘
                        │
                        ▼ (Port 5000 / HTTPS)
┌────────────────────────────────────────────────────────┐
│ PERIMETER TRUST ZONE: AIBO-BACKEND (API Gateway)       │
│ - Authentication authority                             │
│ - Request validation & rate limiting                   │
│ - Only service with direct database credentials        │
└───────────────┬────────────────────────┬───────────────┘
                │                        │
                ▼ (Port 5001 / Private)  ▼ (Port 27017, 6379)
┌──────────────────────────────┐ ┌──────────────────────┐
│ COGNITIVE TRUST ZONE:        │ │ PERSISTENCE ZONE:    │
│ AIBO-ENGINE-V1.0             │ │ MongoDB (rs0)        │
│ - Zero direct DB access      │ │ Redis (BullMQ Cache) │
│ - Shared secret HMAC auth    │ │                      │
│ - LLM provider gateway       │ │                      │
└──────────────────────────────┘ └──────────────────────┘
```

---

## 2. Subsystem Boundaries

### 2.1 Web Frontend Boundary (`AIBO-FRONTEND`)
**Owns:**
- Client-side routing and page composition (React Router 7).
- User presentation and theme token styling (light/dark mode).
- In-memory access token storage (cleared on tab close/logout).
- Silent session refresh lifecycle via HttpOnly cookie (`POST /api/v1/auth/refresh`).
- Real-time event consumption (Socket.io notification and task updates).
- Client-side offline detection and optimistic state synchronization.

**Must NOT:**
- Store access tokens in `localStorage` or `sessionStorage` (XSS vulnerability).
- Store or inspect refresh tokens (handled strictly by browser cookie engine).
- Formulate raw MongoDB database queries or direct storage calls.
- Execute business logic authorization decisions (all permissions verified by backend).
- Bypass backend security gates to call `AIBO-ENGINE-V1.0` directly.

### 2.2 Backend Gateway Boundary (`AIBO-BACKEND`)
**Owns:**
- Public API surface (`/api/v1/*`) and OpenAPI specification compliance.
- User identity, Bcrypt password hashing, JWT generation, and refresh cookie rotation.
- Authoritative persistence for Users, Sessions, Tasks, Schedules, Projects, Columns, Notifications, and Activity Logs.
- Monotonic request deadline management (30-second ceiling) with reserve buffers.
- Dual-mode orchestration routing (`ORCHESTRATION_MODE=canonical` vs `legacy`).
- Distributed rate limiting and BullMQ background task processing via Redis.
- Atomic claiming of pending durable confirmation state records.
- Sanitization of outbound error payloads (zero internal stack/secret leakage).

**Must NOT:**
- Implement cognitive LLM prompting or natural language entity parsing directly in controllers.
- Trust client-supplied user identity when authenticated tokens specify otherwise.
- Allow mutation on unverified or expired confirmation tokens.
- Execute non-idempotent operations without transaction or atomic state guards.

### 2.3 Cognitive Brain Boundary (`AIBO-ENGINE-V1.0`)
**Owns:**
- Natural Language Understanding (NLU): Intent classification and entity extraction.
- Deterministic temporal resolution via `date_resolver.py` relative to anchor dates.
- Goal decomposition and planning proposals (`PlanningService`).
- Action authorization evaluation (`AUTO_EXECUTE`, `ASK_PERMISSION`, `REQUIRE_CONFIRMATION`).
- Multi-provider LLM gateway orchestration (Gemini, OpenAI, Ollama) with circuit breaker and fallback.
- High-entropy confirmation token synthesis (HMAC-SHA256).
- In-process metrics snapshots and health introspection (`/health`, `/ready`, `/metrics`).

**Must NOT:**
- Connect to MongoDB, Redis, or external persistence stores directly.
- Authenticate end users directly or issue user credentials.
- Execute mutations against external systems directly without invoking backend client callbacks.
- Retain unauthorized state beyond the request lifecycle.
- Fall back to non-deterministic mocks in production mode (`ENGINE_ENVIRONMENT=production`).

### 2.4 Central Governance Boundary (`.github`)
**Owns:**
- Architecture Decision Records (ADRs) and organizational standards.
- Reusable CI/CD workflow templates and branch protection policies.
- Ecosystem documentation, onboarding guides, and operational runbooks.
- Vulnerability disclosure and security compliance guidelines.

**Must NOT:**
- Host runtime application code or service-specific configuration files.
- Duplicate documentation that is authoritative within individual service repositories.
