# Environment Strategy

## Environment Taxonomy

| Environment | Purpose | Infrastructure & Lifecycle | Status |
| --- | --- | --- | --- |
| **Local Development** | Contributor feature development and debugging | Run natively (`uv`, `npm run dev`) or via [`docker-compose.yml`](file:///c:/Projects/AIBO_ASSISTANT/docker-compose.yml). Relies on local MongoDB replica set (`rs0`) and Redis 7. Mock LLM or API keys supported. | Active & Verified |
| **Continuous Integration (CI)** | Automated pull-request validation, linting, and regression testing | GitHub Actions runner environments executing isolated unit, integration, and security matrices across Node 22, Python 3.11, and Vite. | Active & Passing (1,128 tests) |
| **Staging / Pre-Production** | Production-mirror integration verification and canary testing | Multi-container Docker Compose deployment with TLS termination, rate-limiting, and staging LLM provider quotas. Validated via `scripts/phase11_smoke.ps1`. | Ready for Release Candidate |
| **Production** | User-facing runtime | Controlled Docker container deployment governed by [`docs/operations/V1.0_PRODUCTION_DEPLOYMENT_RUNBOOK.md`](file:///c:/Projects/AIBO_ASSISTANT/docs/operations/V1.0_PRODUCTION_DEPLOYMENT_RUNBOOK.md). | Production-Ready Architecture |

---

## Environment Variable Standards

1. **Explicit Templates**: Every repository maintains an authoritative `.env.example` that documents every required variable, acceptable values, and safe local defaults.
2. **Zero Plaintext Secrets in SCM**: Real credentials, encryption keys, and third-party tokens must never be committed to Git.
3. **Fail-Fast Validation**: Backend (`Zod` schema validation in `config.ts`) and Engine (`Pydantic BaseSettings` in `config.py`) crash immediately during boot if critical environment variables are absent or malformed.
4. **Environment Isolation**: Production tokens and database URIs must be completely isolated from staging and local environments.

---

## Critical Service Configuration Areas

### 1. Persistence & Caching
- `MONGODB_URI`: Authoritative connection string (e.g., `mongodb://localhost:27017/aibo?replicaSet=rs0`). Multi-document ACID transactions require replica set topology.
- `REDIS_URL`: Cache and distributed rate-limiting endpoint (e.g., `redis://localhost:6380`).

> [!NOTE]
> PostgreSQL has been completely retired. Any configuration containing `POSTGRES_URL` is obsolete and ignored by the V1.0 runtime.

### 2. Service Authentication & Cross-Boundary Secrets
- `JWT_SECRET`: High-entropy key used by the Backend to sign and verify user authentication tokens.
- `ENGINE_SECRET`: Shared pre-shared key (PSK) used for mutual authentication between Backend and Engine via the `X-Engine-Secret` header.
- `CONFIRMATION_HMAC_SECRET`: Cryptographic key used to sign high-risk action confirmation tokens.

### 3. AI Cognitive Engine & LLM Providers
- `AI_ENGINE_URL`: Internal URL for backend-to-engine orchestration (e.g., `http://engine:5001`).
- `AI_ENGINE_TIMEOUT_MS`: Monotonic timeout budget for engine calls (default: `30000` ms).
- `PRIMARY_LLM_PROVIDER`: Selected cognitive provider (`gemini`, `openai`, `ollama`, or `deterministic_mock`).
- `GEMINI_API_KEY`, `OPENAI_API_KEY`: Third-party provider API keys (optional if using local Ollama or mock mode).
- `OLLAMA_BASE_URL`: Local model daemon URL (e.g., `http://ollama:11434`).

### 4. Networking, CORS & Observability
- `PORT`: Service listen port (Backend: `5000`, Engine: `5001`).
- `CORS_ORIGIN`: Allowed origins for browser client communication (e.g., `http://localhost:5173`, `http://localhost:8080`).
- `NODE_ENV` / `ENVIRONMENT`: Runtime mode (`development`, `test`, `production`).
- `LOG_LEVEL`: Structured logging threshold (`debug`, `info`, `warn`, `error`).

