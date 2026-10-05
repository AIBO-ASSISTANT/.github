# Deployment & Infrastructure Governance

This directory contains the operational and architectural governance standards for deploying AIBO Assistant across development, staging, and production environments.

---

## Architecture Summary

AIBO Assistant V1.0 utilizes a modular, multi-container deployment architecture specified in [`docker-compose.yml`](file:///c:/Projects/AIBO_ASSISTANT/docker-compose.yml):
- **Ingress Tier**: `aibo-frontend` (Nginx Alpine reverse-proxying port `8080` to the API gateway and serving React 19 / Vite 8 static SPA assets).
- **Perimeter Tier**: `aibo-backend` (Node 22+ / Express 5 API running on port `5000`, enforcing authentication, CORS, rate-limiting, and data validation).
- **Cognitive Tier**: `aibo-engine` (Python 3.11+ / FastAPI running on port `5001`, protected in an isolated internal network, running LLM Gateway routing and LangGraph cognitive workflows).
- **Persistence Tier**: `mongodb` (MongoDB 7.0 replica set `rs0` on port `27017`) and `redis` (Redis 7 on port `6380`).

---

## Governance Documentation

| Document | Description |
| --- | --- |
| **[Deployment Strategy](deployment-strategy.md)** | Target container topology, deployment units, controlled rollout phases, and deployment invariants. |
| **[Environment Strategy](environment-strategy.md)** | Taxonomy of environments (Local, CI, Staging, Production), configuration standards, and critical environment variables. |
| **[Release & Deployment Governance](release-deployment-governance.md)** | Quality gates for release candidates, deployment checklists, and backward-compatible rollback rules. |
| **[Operational Runbooks Index](runbook-placeholder.md)** | Direct index of all production runbooks located in [`docs/operations/`](file:///c:/Projects/AIBO_ASSISTANT/docs/operations/). |

