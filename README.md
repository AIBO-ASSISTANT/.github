# AIBO Engineering Governance & Architecture

This repository serves as the engineering operating system, shared architectural standards, workflow governance, and cross-repository operational documentation for the **AIBO Assistant** multi-repository ecosystem:

- [`AIBO-BACKEND`](../AIBO-BACKEND) — Node 22 / Express 5 authenticated API gateway, task/schedule/project orchestration boundary, and state manager.
- [`AIBO-FRONTEND`](../AIBO-FRONTEND) — React 19 / Vite 8 / TypeScript web application and real-time interactive user interface.
- [`AIBO-ENGINE-V1.0`](../AIBO-ENGINE-V1.0) — Canonical cognitive AI engine, multi-provider LLM gateway, intent understanding, and planning pipeline.
- [`.github`](.) — Central engineering governance, community health, architecture specifications, standards, and onboarding workflows.

AIBO is an intelligent personal assistant that unifies natural language intent understanding, scheduling with conflict prevention, multi-user project boards, task management, personal journaling, and proactive notification intelligence with strict monotonic deadlines and durable confirmation safety.

---

## 1. Verified System Implementation Status (V1.0 Release Candidate)

| Subsystem / Area | Implementation Status | Test & Verification Evidence | Architecture & Production Notes |
| :--- | :---: | :--- | :--- |
| **Cognitive Engine** (`AIBO-ENGINE-V1.0`) | **Verified / Production Ready** | **606/606 Pytest tests passed**; MyPy 0 errors across 92 files | FastAPI 0.115, Pydantic 2.10, multi-provider LLM Gateway (Gemini 3.6 Flash, OpenAI GPT-4o-mini, Ollama Qwen2.5), circuit breaker, deterministic fallback, monotonic execution budgets, and HMAC-SHA256 confirmation tokens. |
| **Backend API Gateway** (`AIBO-BACKEND`) | **Verified / Production Ready** | **343/343 Jest tests passed** (31 suites); TypeScript 0 errors | Express 5.2, Node 22+, Mongoose 9.6, MongoDB 6/7 authoritative persistence, Redis 7 (BullMQ event queues & distributed rate limiters), Socket.io 4.8 real-time hub, and dual-mode orchestration. |
| **Web Frontend** (`AIBO-FRONTEND`) | **Verified / Production Ready** | **87/87 tests passed** (32 unit + 55 component); Vite build 833ms | React 19, Vite 8, React Router 7, Zustand 5, Recharts, dark/light theme tokens, Scheduler, Project Manager Kanban, Diary, Dashboard, and tabbed Settings Hub. |
| **Cross-Repo E2E Suite** | **Verified / Production Ready** | **92/92 E2E scenarios passed** (100% pass rate in 82s) | Real Engine + Backend + isolated MongoDB test harness testing normal dialogue, task/schedule CRUD, clarification, confirmation replay prevention, cancellation safety, error injection (429, 503, timeouts), and durable state survival across restarts. |
| **Deployment & Containers** | **Verified / Production Ready** | Multi-container `docker-compose.yml` validated via `docker compose config` | Containerized topology: `aibo-frontend` (Nginx 8080), `aibo-backend` (5000), `aibo-engine` (5001), `mongodb` (Replica set `rs0` on 27017), `redis` (6379), and `ollama` (11434). |
| **Observability & Diagnostics** | **Verified / Production Ready** | Dedicated health probes and in-process metrics snapshots | Structured JSON logging (Pino and Structlog), request ID & correlation propagation (`x-request-id`, `x-correlation-id`), `/api/v1/health/live`, `/ready`, `/dependencies`, and `/metrics`. |
| **Security Governance** | **Verified / Production Ready** | Zero DB mutation on security failure; 12 security E2E scenarios passed | Constant-time HMAC confirmation validation, shared `ENGINE_SECRET` boundary, HttpOnly cookie refresh sessions, JWT access tokens, role-based controls, input sanitization, and security scanners. |

See the complete [Feature Maturity Matrix](docs/product/feature-maturity-matrix.md) for granular domain breakdowns.

---

## 2. Multi-Repository Ecosystem

```
                                  ┌─────────────────────────────┐
                                  │      .github Governance     │
                                  │   (Standards, Architecture, │
                                  │    CI/CD Rules, Onboarding) │
                                  └──────────────┬──────────────┘
                                                 │ defines standards
                   ┌─────────────────────────────┼─────────────────────────────┐
                   ▼                             ▼                             ▼
    ┌─────────────────────────────┐ ┌─────────────────────────────┐ ┌─────────────────────────────┐
    │        AIBO-FRONTEND        │ │         AIBO-BACKEND        │ │       AIBO-ENGINE-V1.0      │
    │  (React 19 + Vite 8 + CSS)  │ │  (Express 5 + Mongoose + TS)│ │(FastAPI + Python + LLM Gate)│
    │  Port 8080 (Browser Client) │ │  Port 5000 (API & Gateway)  │ │  Port 5001 (Cognitive Brain)│
    └──────────────┬──────────────┘ └──────────────┬──────────────┘ └──────────────┬──────────────┘
                   │                               │                               │
                   │ HTTP /api/v1 + Bearer Token   │                               │
                   └──────────────────────────────►│ Internal HTTP + HMAC Secret   │
                                                   │ POST /orchestrate (Canonical) │
                                                   ├──────────────────────────────►│
                                                   │◄──────────────────────────────┤
                                                   │ Execution Callbacks           │
                                                   │                               │
                                                   ├──────────────┬────────────────┘
                                                   ▼              ▼
                                           ┌──────────────┐┌──────────────┐
                                           │   MongoDB    ││    Redis     │
                                           │ (Replica rs0)││  (BullMQ +   │
                                           │ Port 27017   ││  Rate Limit) │
                                           └──────────────┘└──────────────┘
```

| Repository | Responsibility Boundary | Primary Technologies | Authority & Access Limits |
| :--- | :--- | :--- | :--- |
| **`.github`** | Engineering governance, architecture standards, CI/CD policy, ADRs, security runbooks. | Markdown, YAML, GitHub Actions | Owns organization-wide engineering policy; does not contain application business logic. |
| **`AIBO-BACKEND`** | Authenticated API gateway, session lifecycle, MongoDB persistence, durable state coordinator, task/schedule/project management, real-time Socket.io hub. | Node.js 22+, Express 5, TypeScript 6, Mongoose 9.6, BullMQ, Redis, Pino | Authoritative for all persistence and user authorization. Only layer with direct database connections. |
| **`AIBO-FRONTEND`** | Web application, user interface, route composition, client-side auth refresh lifecycle, responsive views, real-time WebSocket events. | React 19, Vite 8, TypeScript 6, Zustand 5, Recharts, Lucide, Vitest | Presentation and user experience. Holds access token in memory; never writes to database directly. |
| **`AIBO-ENGINE-V1.0`** | Cognitive brain, NLU intent classification, entity extraction, planning, authorization policy evaluation, multi-provider LLM gateway, execution coordinator. | Python 3.11+, FastAPI, Pydantic 2, Structlog, Uvicorn, Gemini SDK, OpenAI SDK | Cognitive domain authority. Stateless cognitive coordinator; has **zero direct database access**. |

---

## 3. Architecture Highlights

### 3.1 Canonical Orchestration Pipeline
Cognitive interactions flow through the canonical lifecycle defined in [`V1.0_ORCHESTRATION_CONTRACT.md`](../docs/releases/V1.0_ORCHESTRATION_CONTRACT.md):
1. **Request Ingress & Budgeting**: The request enters `POST /api/v1/engine/orchestrate` with monotonic deadline ceilings (default 30s) and correlation tracking (`x-request-id`, `x-correlation-id`).
2. **Context Hydration**: Session history and semantic cognitive memory are retrieved.
3. **Understanding**: Intent classification and entity extraction anchor temporal expressions (`date_resolver.py`) relative to reference dates.
4. **Planning & Decision**: Single-task proposals or multi-action breakdown plans (`ActionBuilder`, `PlanningService`).
5. **Authorization Policy**: Actions evaluated into `AUTO_EXECUTE`, `ASK_PERMISSION`, or `REQUIRE_CONFIRMATION` based on risk level.
6. **Durable State Protection**: High-risk actions generate HMAC-SHA256 record-bound confirmation tokens stored durably in MongoDB.
7. **Atomic Execution**: On confirmation, backend atomically claims the pending state record, preventing race conditions or replay attacks.

### 3.2 Dual-Route Backward Compatibility
The system preserves 100% backward compatibility for legacy clients:
- **Canonical Route**: `POST /api/v1/engine/orchestrate` -> `POST /orchestrate`
- **Legacy Fallback Routes**: `POST /process` and `POST /respond`
- **Rollback Control**: Server controls `ORCHESTRATION_MODE=canonical` or `ORCHESTRATION_MODE=legacy`. Clients cannot force execution modes.

### 3.3 Authoritative Database Model
- **MongoDB**: Authoritative document store for Users, Sessions, Tasks, Schedules, Projects, Columns, Notifications, Assets, Activity Logs, and Durable Orchestration State Records.
- **Redis**: In-memory high-throughput cache for BullMQ background event queues and multi-tier rate limiters.
- **PostgreSQL**: Legacy relational references have been eliminated in favor of a unified Mongoose schema with atomic MongoDB transactions.

---

## 4. Technology Stack Matrix

| Layer | Runtime / Framework | Core Libraries & Tooling | Testing & Quality Tooling |
| :--- | :--- | :--- | :--- |
| **Frontend** | Node.js 22+, React 19, Vite 8 | React Router 7, Zustand 5, Axios, Recharts, Lucide-React, Socket.io-client | Vitest 5, Testing Library, Node Test Runner, ESLint 10, Prettier |
| **Backend** | Node.js 22+, Express 5 (ESM) | TypeScript 6, Mongoose 9.6, BullMQ 5, Redis (ioredis), Socket.io 4, Helmet, Pino, Zod 4 | Jest 30 (ts-jest), Supertest, Cross-Repo E2E Integration Runner, ESLint |
| **AI Engine** | Python 3.11+, FastAPI 0.115, Uvicorn | Pydantic 2.10, Structlog 24, google-generativeai, openai, httpx, python-dotenv | Pytest 9, Pytest-Asyncio, MyPy 1.14, Ruff 0.9, Deterministic Mock Provider |
| **Data Stores** | MongoDB 6+ (Replica Set `rs0`), Redis 7+ | Mongoose ORM, Redis CLI | Mongo Shell, In-Memory Mongo Integration Mock |
| **Containers** | Docker Engine 24+, Compose v2 | Multi-stage Alpine/Slim Dockerfiles, Nginx Reverse Proxy | `docker compose config`, Container Healthchecks |

---

## 5. Documentation Navigation Map

| Area | Primary Starting Document | Key Sub-Documents |
| :--- | :--- | :--- |
| **Architecture** | [architecture/overview.md](architecture/overview.md) | [service-boundaries.md](architecture/service-boundaries.md) • [repository-relationships.md](architecture/repository-relationships.md) • [ai-lifecycle.md](architecture/ai-lifecycle.md) • [deployment-topology.md](architecture/deployment-topology.md) • [database-ownership.md](architecture/database-ownership.md) • [scalability-strategy.md](architecture/scalability-strategy.md) |
| **Product & Scope** | [docs/product/vision.md](docs/product/vision.md) | [feature-maturity-matrix.md](docs/product/feature-maturity-matrix.md) • [scope.md](docs/product/scope.md) • [ROADMAP.md](ROADMAP.md) |
| **Developer Onboarding** | [onboarding/README.md](onboarding/README.md) | [local-setup.md](onboarding/local-setup.md) • [backend-setup.md](onboarding/backend-setup.md) • [engine-setup.md](onboarding/engine-setup.md) • [frontend-setup.md](onboarding/frontend-setup.md) • [testing-guide.md](onboarding/testing-guide.md) • [environment-setup.md](onboarding/environment-setup.md) |
| **Operations & Runbooks** | [../docs/operations/V1.0_OBSERVABILITY.md](../docs/operations/V1.0_OBSERVABILITY.md) | [V1.0_INCIDENT_RUNBOOK.md](../docs/operations/V1.0_INCIDENT_RUNBOOK.md) • [V1.0_PRODUCTION_DEPLOYMENT_RUNBOOK.md](../docs/operations/V1.0_PRODUCTION_DEPLOYMENT_RUNBOOK.md) • [V1.0_PRODUCTION_ROLLBACK_RUNBOOK.md](../docs/operations/V1.0_PRODUCTION_ROLLBACK_RUNBOOK.md) • [V1.0_CANONICAL_ROLLOUT_RUNBOOK.md](../docs/operations/V1.0_CANONICAL_ROLLOUT_RUNBOOK.md) |
| **Contracts & Releases** | [../docs/releases/V1.0_ORCHESTRATION_CONTRACT.md](../docs/releases/V1.0_ORCHESTRATION_CONTRACT.md) | [V1.0_PHASE10_FINAL_REPORT.md](../docs/releases/V1.0_PHASE10_FINAL_REPORT.md) • [V1.0_SECURITY_TRUST_BOUNDARY.md](../docs/releases/V1.0_SECURITY_TRUST_BOUNDARY.md) • [V1.0_LLM_GATEWAY_GOVERNANCE.md](../docs/releases/V1.0_LLM_GATEWAY_GOVERNANCE.md) |
| **Observability** | [observability/monitoring-strategy.md](observability/monitoring-strategy.md) | [health-check-standards.md](observability/health-check-standards.md) • [logging-standards.md](observability/logging-standards.md) • [incident-response.md](observability/incident-response.md) |
| **Security Governance** | [SECURITY.md](SECURITY.md) | [security/security-governance.md](security/security-governance.md) • [security/secrets-management.md](security/secrets-management.md) • [security/auth-token-policy.md](security/auth-token-policy.md) • [security/logging-redaction.md](security/logging-redaction.md) |
| **Engineering Standards**| [standards/engineering-principles.md](standards/engineering-principles.md) | [coding-standards.md](standards/coding-standards.md) • [code-review-standards.md](standards/code-review-standards.md) • [branching-strategy.md](standards/branching-strategy.md) • [release-process.md](standards/release-process.md) |

---

## 6. Quick Start & Verification Commands

### One-Command Full Stack (Docker Compose)
```powershell
# 1. Start all infrastructure and application services
docker compose up -d

# 2. Inspect container status
docker compose ps

# 3. Access endpoints
# Frontend: http://localhost:8080
# Backend API: http://localhost:5000/api/v1/health
# Engine Health: http://localhost:5001/health
```

### Native Verification Commands
```powershell
# Cognitive Engine (606 tests + MyPy type check)
cd AIBO-ENGINE-V1.0
uv run pytest
uv run mypy src

# Backend API & Cross-Repo E2E (343 Jest tests + 92 E2E scenarios + TS build)
cd ../AIBO-BACKEND
npm test
npm run build
npm run test:e2e

# Web Frontend (87 tests + typecheck + lint + production build)
cd ../AIBO-FRONTEND
npm test
npm run type-check
npm run lint
npm run build
```

---

## 7. Governance & Contribution Rules

1. **Pull Request Policy**: All PRs must target a specific feature/fix branch, include automated test coverage, provide green verification evidence, and use [PULL_REQUEST_TEMPLATE.md](PULL_REQUEST_TEMPLATE.md).
2. **Contract Freeze**: Any modification to `POST /orchestrate` or state-transition tables requires an approved Architecture Decision Record (ADR) in [adr/](adr/).
3. **Zero Secret Leakage**: Credentials and keys (`ENGINE_SECRET`, `JWT_SECRET`, API keys) must never be checked into version control. Environment files (`.env`) are strictly ignored across all subtrees.
4. **License**: This project is licensed under the MIT License. See [LICENSE](LICENSE) for details.
