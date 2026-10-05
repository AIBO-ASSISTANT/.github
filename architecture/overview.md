# AIBO Assistant — High-Level Architecture Overview

The AIBO Assistant ecosystem is structured as a resilient, decoupled multi-repository architecture designed for high-concurrency cognitive processing, deterministic safety, and reliable task/schedule/project management.

---

## 1. System Architecture Diagram

```mermaid
flowchart TD
  subgraph ClientLayer ["Client Layer"]
    Browser["User Browser Client<br/>(React 19 + Vite 8)"]
  end

  subgraph GatewayLayer ["Orchestration & Gateway Layer (Port 5000)"]
    Backend["AIBO-BACKEND (Express 5 + TypeScript)"]
    AuthMgr["Auth & Session Manager<br/>(JWT + HttpOnly Cookie)"]
    Router["Dual Orchestration Router<br/>(Canonical vs Legacy Mode)"]
    RealtimeHub["Realtime WebSocket Hub<br/>(Socket.io)"]
    DeadlineGov["Monotonic Deadline Governor<br/>(30s Ceiling Budget)"]
  end

  subgraph CognitiveLayer ["Cognitive AI Subsystem (Port 5001)"]
    Engine["AIBO-ENGINE-V1.0 (FastAPI + Pydantic 2)"]
    NLU["Understanding Service<br/>(Intent + Temporal Date Anchor)"]
    Planning["Planning Service & Action Builder<br/>(Task / Project / Schedule Breakdown)"]
    AuthPolicy["Action Authorizer & Policy Evaluator<br/>(Auto / Ask Permission / Require Confirm)"]
    LLMGateway["Exclusive LLM Gateway<br/>(Circuit Breaker + Fallback + Retries)"]
    StateStore["Durable State Engine Client<br/>(Record Verification & Tokens)"]
  end

  subgraph DataLayer ["Authoritative Persistence & Caching Layer"]
    Mongo[("MongoDB Replica Set (rs0)<br/>Users, Tasks, Schedules, Projects,<br/>Notifications, Durable State Records")]
    Redis[("Redis 7 Cache<br/>BullMQ Event Queues & Distributed Rate Limiters")]
  end

  subgraph ModelProviders ["Managed & Local Model Providers"]
    Gemini["Google Gemini 3.6 Flash"]
    OpenAI["OpenAI GPT-4o-mini"]
    Ollama["Local Ollama Qwen2.5:7b"]
  end

  Browser <-->|HTTP /api/v1 (Bearer Token) + WebSockets| Backend
  Backend --> AuthMgr
  Backend --> Router
  Backend --> RealtimeHub
  Backend --> DeadlineGov

  Router <-->|Internal HTTP + Shared HMAC Secret<br/>POST /orchestrate (Canonical) or /respond| Engine
  Engine --> NLU
  Engine --> Planning
  Engine --> AuthPolicy
  Engine --> StateStore
  Engine --> LLMGateway

  LLMGateway -.->|HTTPS API| Gemini
  LLMGateway -.->|HTTPS API| OpenAI
  LLMGateway -.->|HTTP localhost:11434| Ollama

  Backend <-->|Mongoose ODM (Transactions)| Mongo
  Backend <-->|ioredis| Redis
```

---

## 2. Core Subsystems

### 2.1 Web Frontend (`AIBO-FRONTEND`)
- **Technology**: React 19, Vite 8, TypeScript 6, React Router 7, Zustand 5, Recharts, Lucide-React.
- **Responsibility**: Single-page browser application providing interactive productivity surfaces:
  - **Dashboard**: Dynamic KPI statistics, weekly productivity charts, upcoming schedule feed.
  - **Scheduler**: Interactive calendar view, slot booking, date-picker navigation.
  - **Project Manager**: Dynamic Kanban board with configurable columns, drag-and-drop tasks, multi-assignee management.
  - **Diary & Journal**: Personal daily reflection, mood tracking, and historic entries.
  - **Settings Hub**: Tabbed configuration suite covering General, Notifications, Security & Sessions, Privacy, Appearance, and Connected Apps.
- **Security Boundary**: Access tokens are held exclusively in memory; session restoration uses an HttpOnly `aibo_refresh_token` cookie via `POST /api/v1/auth/refresh`.

### 2.2 Backend Gateway & Orchestrator (`AIBO-BACKEND`)
- **Technology**: Node.js 22+, Express 5, TypeScript 6, Mongoose 9.6, BullMQ, Redis, Pino.
- **Responsibility**:
  - Authenticated API gateway (`/api/v1/*`) with strict Zod validation schemas.
  - Authoritative persistence coordinator for all data models.
  - Security governor: Password hashing (Bcrypt), JWT signing, multi-tier rate limiting (Auth, Write, User, Global), and request sanitization.
  - Monotonic request deadline ceiling (30s) and correlation tracking (`x-request-id`, `x-correlation-id`).
  - Durable confirmation manager with atomic MongoDB claims preventing double-execution races.

### 2.3 Cognitive Brain (`AIBO-ENGINE-V1.0`)
- **Technology**: Python 3.11+, FastAPI 0.115, Pydantic 2.10, Structlog 24, Uvicorn 0.34.
- **Responsibility**:
  - Natural Language Understanding (NLU): Identifies user intent and resolves temporal references deterministically via `date_resolver.py`.
  - Planning Engine: Synthesizes multi-step project decompositions and calendar schedules.
  - Action Authorizer: Categorizes actions into `AUTO_EXECUTE`, `ASK_PERMISSION`, or `REQUIRE_CONFIRMATION`.
  - LLM Gateway: Unified model gateway routing between primary (Gemini), secondary fallback (GPT-4o-mini), and local offline (Ollama) with circuit breakers and exponential backoff retry policies.
  - **Zero Database Access**: The engine never connects to MongoDB or Redis directly; all external effects are executed through authenticated backend callbacks.

---

## 3. Key Architectural Choices & Invariants

1. **Server-Authoritative Confirmation Tokens**: Action batches requiring confirmation generate high-entropy HMAC-SHA256 tokens bound to the specific action parameters and user ID. Clients cannot inject altered payloads upon confirmation.
2. **Atomic Durable Confirmation Claims**: When confirming an action, MongoDB uses an atomic condition check (`state: AWAITING_CONFIRMATION` -> `state: EXECUTING`) to guarantee that concurrent confirmation requests or network replays cannot cause duplicate mutations.
3. **Monotonic Timeout Ceilings**: Request deadlines are strictly enforced down the pipeline. If a request has 5s remaining in its budget, LLM providers and database queries are clamped to that budget, ensuring fail-fast client predictability.
4. **Single-Database Simplicity**: All application domains (Users, Tasks, Schedules, Projects, Columns, Notifications) reside in MongoDB, enabling clean transactions without distributed two-phase commit overhead.
5. **Zero Mutation on Error**: On any validation, authorization, or timeout failure, the system guarantees 0 side effects in database storage.

---

## 4. Architectural Reference Documents

- [Service Boundaries](service-boundaries.md) — Trust zones and component limitations.
- [Repository Relationships](repository-relationships.md) — Cross-repo contract interaction model.
- [AI Lifecycle](ai-lifecycle.md) — Step-by-step cognitive request lifecycle.
- [Database Ownership](database-ownership.md) — Schema ownership and Mongoose models.
- [Deployment Topology](deployment-topology.md) — Multi-container Docker deployment.
- [Scalability Strategy](scalability-strategy.md) — Horizontal scaling and caching philosophy.
