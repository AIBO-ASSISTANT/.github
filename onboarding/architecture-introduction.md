# Architecture Introduction for New Engineers

Welcome to AIBO Assistant! This document introduces new engineers to the core architectural topology, repository responsibilities, and fundamental engineering invariants.

---

## Ecosystem Repositories

| Repository | Tech Stack | Role & Boundary |
| --- | --- | --- |
| **`AIBO-FRONTEND`** | React 19, Vite 8, Zustand 5, Recharts | Single-Page Application (SPA) serving user interfaces, managing in-memory auth tokens, rendering Kanban boards, and handling real-time WebSocket events. |
| **`AIBO-BACKEND`** | Node.js 22+, Express 5, Mongoose, Redis | Authoritative API perimeter. Enforces authentication, tenant authorization, Zod schema validation, transactional MongoDB persistence, and cognitive engine orchestration. |
| **`AIBO-ENGINE-V1.0`** | Python 3.11+, FastAPI, Pydantic v2, LangGraph | Private cognitive service. Executes NLU intent classification, temporal entity extraction, LLM Gateway routing (Gemini, OpenAI, Ollama), and action planning. |
| **`.github`** | Markdown, YAML, Mermaid | Central engineering governance hub. Codifies architecture, security invariants, operational runbooks, CI/CD templates, and release standards. |

---

## The Request Flow in 3 Steps

1. **Client Interaction**: User triggers an action in the React 19 web app. The client sends an HTTP request or WebSocket message to `/api/v1/*` with an `X-Request-Id` and short-lived JWT.
2. **Perimeter Authorization & Orchestration**: Express 5 validates the JWT, checks rate limits, hydrations context, and (if natural language intent is required) forwards the payload to `AIBO-ENGINE-V1.0` via `POST /orchestrate` with an internal `X-Engine-Secret`.
3. **Execution & Confirmation**: The engine proposes actions. Safe actions execute immediately against MongoDB replica set (`rs0`). High-risk actions (deletions, reassignments) return an HMAC confirmation token requiring explicit user approval before execution.

---

## Golden Architectural Invariants

- **The Engine Never Writes to the Database**: The cognitive engine is completely stateless and has zero database credentials. Only `AIBO-BACKEND` writes to MongoDB.
- **The Browser Never Calls the Engine Directly**: The engine port (`5001`) is isolated within an internal network; all browser traffic terminates at `AIBO-BACKEND`.
- **Single Authoritative Datastore**: All domain entities reside in MongoDB. PostgreSQL has been retired.
