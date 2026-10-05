# Cognitive Engine Developer Setup (`AIBO-ENGINE-V1.0`)

This document provides setup, testing, and operational guidance for the canonical cognitive brain service: `AIBO-ENGINE-V1.0`.

---

## 1. Prerequisites

- **Python**: v3.11+ (Tested on v3.13)
- **uv**: v0.11+ (High-performance Python package and virtualenv manager)

---

## 2. Installation & Running

```bash
cd AIBO-ENGINE-V1.0

# Install dependencies and sync virtual environment
uv sync

# Copy environment template
cp .env.example .env

# Start FastAPI development server with hot-reload
uv run uvicorn src.api.app:app --host 0.0.0.0 --port 5001 --reload
```

The engine listens on `http://localhost:5001`.

---

## 3. Core Verification Commands

| Command | Purpose | Expected Output |
| :--- | :--- | :--- |
| `uv run pytest` | Runs all 606 unit, integration, and cognitive evaluation tests. | `606 passed` |
| `uv run mypy src` | Type-checks all 92 source files under strict typing rules. | `Success: no issues found in 92 source files` |
| `uv run ruff check src` | Lints source files for formatting and idiomatic Python. | Clean code / fast analysis |

---

## 4. Key Environment Variables

Configure these in `AIBO-ENGINE-V1.0/.env`:

```ini
ENGINE_ENVIRONMENT=development
ENGINE_HOST=0.0.0.0
ENGINE_PORT=5001
ENGINE_SECRET=development_shared_secret
ENGINE_MODEL_PROVIDER=ollama
ENGINE_MODEL_NAME=qwen2.5:7b
ENGINE_OLLAMA_BASE_URL=http://localhost:11434/v1
ENGINE_OPENAI_API_KEY=sk-...
ENGINE_OPENAI_MODEL_NAME=gpt-4o-mini
ENGINE_GEMINI_API_KEY=AQ...
ENGINE_BACKEND_BASE_URL=http://localhost:5000
ENGINE_ORCHESTRATION_TIMEOUT_SECONDS=30.0
ENGINE_CONFIRMATION_TTL_SECONDS=300.0
```

---

## 5. Architectural Invariants

1. **Zero Database Connections**: The engine never connects to MongoDB or Redis directly. Any action requiring persistence is dispatched through authenticated backend callbacks.
2. **Deterministic Temporal Anchoring**: All relative dates are anchored to the request's `reference_date` using `date_resolver.py`.
3. **Multi-Provider Resilience**: The LLM Gateway provides automatic fallback between Gemini, OpenAI, and Ollama with circuit breakers and deadline-aware retry limits.
4. **Deterministic Mocks in CI**: When `ENGINE_MODEL_PROVIDER=deterministic_mock` is set, the engine executes with zero cost and zero external network latency.
