# AIBO Assistant — Local Developer Onboarding & Environment Setup

This guide walks new engineers through setting up, running, and verifying the complete AIBO Assistant ecosystem on a local workstation.

---

## 1. Prerequisites & Toolchain

Ensure the following tools are installed on your workstation:
- **Node.js**: v22.17.0+ (LTS) with `npm` v10+
- **Python**: v3.11+ with `uv` (modern Python package manager)
- **MongoDB**: Community Edition v6.0+ (configured as replica set `rs0` for transactions)
- **Redis**: v7.0+
- **Docker & Docker Compose**: Docker Desktop / Engine v24+ with Compose v2
- **Git**: v2.40+

---

## 2. Workspace Organization

The root directory contains three primary application subprojects and central governance:

```text
AIBO_ASSISTANT/
├── .github/              # Central engineering governance, standards, architecture
├── AIBO-BACKEND/         # Node.js 22 / Express 5 API gateway & persistence manager
├── AIBO-FRONTEND/        # React 19 / Vite 8 web application
├── AIBO-ENGINE-V1.0/     # Canonical Python 3.11+ FastAPI cognitive brain
├── docker-compose.yml    # Multi-container orchestration stack
└── README.md             # Top-level workspace instructions
```

---

## 3. Fast Setup via Docker Compose (Recommended)

The fastest and most consistent way to run the entire verified stack (including Ollama local LLM, MongoDB replica set, Redis, and all microservices) is using Docker Compose.

> [!TIP]
> For a dedicated, comprehensive first-time Docker installation manual with Ollama model pulling and GPU tuning, read the **[Complete Docker Setup Guide](docker-setup.md)**.

```powershell
# 1. Clone the repository and navigate to root
cd c:\Projects\AIBO_ASSISTANT

# 2. Configure environment files from templates
Copy-Item .env.example .env
Copy-Item AIBO-BACKEND\.env.example AIBO-BACKEND\.env
Copy-Item AIBO-ENGINE-V1.0\.env.example AIBO-ENGINE-V1.0\.env
Copy-Item AIBO-FRONTEND\.env.example AIBO-FRONTEND\.env.local

# 3. Ensure ENGINE_SECRET and JWT_SECRET are set in the root .env

# 4. Start all services in detached mode
docker compose up -d --build

# 5. Pull the Ollama local AI model (first time only)
docker compose exec ollama ollama pull qwen2.5:7b

# 6. Check container health
docker compose ps
```

Once running:
- **Web UI**: [http://localhost:8080](http://localhost:8080)
- **Backend API**: [http://localhost:5000/api/v1/health](http://localhost:5000/api/v1/health)
- **Cognitive Engine**: [http://localhost:5001/health](http://localhost:5001/health)
- **Ollama API**: [http://localhost:11434](http://localhost:11434)

- **Engine Health**: [http://localhost:5001/health](http://localhost:5001/health)

---

## 4. Native Local Development Setup

If developing directly on the host machine:

### Step 1: Start Databases
```powershell
# MongoDB with replica set (required for durable confirmation transactions)
mongod --replSet rs0 --port 27017 --dbpath C:\data\db

# In a separate shell, initialize replica set if first time:
mongosh --eval "rs.initiate()"

# Start Redis
redis-server --port 6379
```

### Step 2: Start Cognitive Engine (`AIBO-ENGINE-V1.0`)
```powershell
cd AIBO-ENGINE-V1.0
uv sync
uv run uvicorn src.api.app:app --host 0.0.0.0 --port 5001 --reload
```

### Step 3: Start Backend API (`AIBO-BACKEND`)
```powershell
cd ../AIBO-BACKEND
npm install
npm run dev
```

### Step 4: Start Frontend Client (`AIBO-FRONTEND`)
```powershell
cd ../AIBO-FRONTEND
npm install
npm run dev
```

---

## 5. Verification & Test Suite Execution

Run the complete test battery to verify zero regressions:

```powershell
# 1. Cognitive Engine Tests (606 tests)
cd AIBO-ENGINE-V1.0
uv run pytest
uv run mypy src

# 2. Backend Unit & Integration Tests (343 tests)
cd ../AIBO-BACKEND
npm test
npm run build

# 3. Cross-Repository E2E Integration Suite (92 scenarios)
$env:PYTHON_EXEC = (Get-Command uv).Source
npm run test:e2e

# 4. Frontend Component & Unit Tests (87 tests)
cd ../AIBO-FRONTEND
npm test
npm run type-check
npm run lint
npm run build
```

---

## 6. Service-Specific Setup Guides

- [Complete Docker & Ollama Setup Guide](docker-setup.md)
- [Backend Developer Setup](backend-setup.md)
- [Cognitive Engine Developer Setup](engine-setup.md)
- [Frontend Developer Setup](frontend-setup.md)
- [Testing Architecture Guide](testing-guide.md)
- [Environment Configuration Guide](environment-setup.md)

