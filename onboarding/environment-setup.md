# Environment Setup Guide

This guide details the standard environment variable configurations for running AIBO Assistant services locally and in containerized environments.

---

## Backend (`AIBO-BACKEND/.env`)

Copy `AIBO-BACKEND/.env.example` to `AIBO-BACKEND/.env`:

```env
NODE_ENV=development
PORT=5000
MONGODB_URI=mongodb://127.0.0.1:27017/aibo?replicaSet=rs0
REDIS_URL=redis://127.0.0.1:6380
JWT_SECRET=super_secret_local_dev_key_min_32_characters_long!
ENGINE_SECRET=dev_shared_engine_pre_shared_key
AI_ENGINE_URL=http://127.0.0.1:5001
AI_ENGINE_TIMEOUT_MS=30000
CORS_ORIGIN=http://localhost:5173
AUTH_COOKIE_NAME=aibo_refresh_token
AUTH_COOKIE_PATH=/api/v1/auth
AUTH_COOKIE_SAME_SITE=lax
AUTH_COOKIE_SECURE=false
LOG_LEVEL=info
```

> [!NOTE]
> **PostgreSQL Retired**: All database connections use `MONGODB_URI`. Relational PostgreSQL variables are obsolete.

---

## Frontend (`AIBO-FRONTEND/.env`)

Copy `AIBO-FRONTEND/.env.example` to `AIBO-FRONTEND/.env.local`:

```env
VITE_APP_NAME="AIBO Assistant"
VITE_API_BASE_URL=/api/v1
VITE_SOCKET_URL=http://localhost:5000
```

---

## AI Cognitive Engine (`AIBO-ENGINE-V1.0/.env`)

`AIBO-ENGINE-V1.0` dependencies are formally managed via [`pyproject.toml`](file:///c:/Projects/AIBO_ASSISTANT/AIBO-ENGINE-V1.0/pyproject.toml). Configure `AIBO-ENGINE-V1.0/.env`:

```env
PORT=5001
HOST=0.0.0.0
ENVIRONMENT=development
ENGINE_SECRET=dev_shared_engine_pre_shared_key
PRIMARY_LLM_PROVIDER=gemini
GEMINI_API_KEY=your_gemini_api_key_here
OPENAI_API_KEY=your_openai_api_key_here
OLLAMA_BASE_URL=http://127.0.0.1:11434
LOG_LEVEL=INFO
```
