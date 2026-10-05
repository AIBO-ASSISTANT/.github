# Complete Docker & Containerized Environment Setup Guide

This guide is the authoritative, step-by-step manual for configuring, building, running, and troubleshooting the entire **AIBO Assistant** multi-container ecosystem from scratch using Docker and Docker Compose.

---

## 1. System Requirements & Architecture Overview

### Container Topology & Port Allocations

The containerized stack is orchestrated by the root [`docker-compose.yml`](file:///c:/Projects/AIBO_ASSISTANT/docker-compose.yml) and comprises six interconnected services across isolated network zones:

| Service Name | Container Image / Context | Host Port | Internal Port | Memory Req. | Purpose & Health Check |
| --- | --- | --- | --- | --- | --- |
| **`aibo-frontend`** | [`./AIBO-FRONTEND`](file:///c:/Projects/AIBO_ASSISTANT/AIBO-FRONTEND/Dockerfile) (Nginx + Vite build) | `8080` | `80` | ~128 MB | Serves static SPA; proxies `/api/` and `/socket.io/` to backend. Probes `wget http://127.0.0.1:80`. |
| **`aibo-backend`** | [`./AIBO-BACKEND`](file:///c:/Projects/AIBO_ASSISTANT/AIBO-BACKEND/Dockerfile) (Node 22 Express 5) | `5000` | `5000` | ~512 MB | API perimeter gateway; evaluates auth, transactions, and contracts. Probes `/api/v1/health/ready`. |
| **`aibo-engine`** | [`./AIBO-ENGINE-V1.0`](file:///c:/Projects/AIBO_ASSISTANT/AIBO-ENGINE-V1.0/Dockerfile) (Python 3.13 FastAPI) | `5001` | `5001` | ~512 MB | Cognitive NLU, LangGraph state machine, LLM Gateway. Probes `/ready`. |
| **`ollama`** | `ollama/ollama:latest` | `11434` | `11434` | 4 GB - 8 GB | Local LLM inference provider (`qwen2.5:7b` or `qwen2.5:3b`). Probes `ollama list`. |
| **`mongodb`** | `mongo:6` | `27017` | `27017` | ~512 MB | Authoritative persistent store running as replica set `rs0`. Auto-initiates `rs0` via `mongosh`. |
| **`redis`** | `redis:7-alpine` | `6380` | `6379` | ~128 MB | Ephemeral cache & token-bucket rate limiter. Probes `redis-cli ping`. |

### Host Prerequisites

1. **Docker Engine & Docker Compose**:
   - **Windows / macOS**: [Docker Desktop](https://www.docker.com/products/docker-desktop/) (v4.30+ recommended).
     - On Windows: Ensure **WSL 2 backend** is enabled in Docker Desktop settings (*Settings > General > Use the WSL 2 based engine*).
   - **Linux**: Docker Engine v24+ and Docker Compose v2.20+.
2. **Hardware Recommendations**:
   - **RAM**: Minimum 8 GB (16 GB strongly recommended if running Ollama with `qwen2.5:7b`).
   - **Disk Space**: At least 25 GB free disk space (to accommodate Docker images, MongoDB data, and Ollama model weights).
   - **GPU (Optional)**: NVIDIA GPU with CUDA drivers and [NVIDIA Container Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html) for hardware-accelerated local inference.

---

## 2. Pre-Flight Configuration (Environment Files)

`docker-compose.yml` enforces two fail-fast environment checks on startup:
1. `ENGINE_SECRET`: Shared pre-shared key (PSK) between Backend and Engine.
2. `JWT_SECRET`: Signing secret for user authentication tokens (minimum 32 characters).

### Step 1: Create the Root Compose `.env`

Create a `.env` file at the root of the repository (`c:\Projects\AIBO_ASSISTANT\.env`):

#### Windows (PowerShell):
```powershell
# In c:\Projects\AIBO_ASSISTANT
@"
ENGINE_SECRET=aibo_secure_shared_engine_token_2026_x9k2
JWT_SECRET=aibo_jwt_secret_token_min_32_characters_long_99!
RATE_LIMIT_MAX=1000
AUTH_RATE_LIMIT_MAX=1000
USER_RATE_LIMIT_MAX=120
ORCHESTRATION_MODE=canonical
REQUEST_MAX_TIMEOUT_MS=300000
ENGINE_TIMEOUT=300000
ENGINE_ENVIRONMENT=production
ENGINE_DURABLE_STATE_ENABLED=true
ENGINE_MODEL_ENABLED=true
ENGINE_LLM_DISAMBIGUATION_ENABLED=true
ENGINE_OLLAMA_BASE_URL=http://ollama:11434/v1
OLLAMA_KEEP_ALIVE=-1
"@ | Out-File -FilePath .env -Encoding utf8
```

#### Linux / macOS (Bash):
```bash
cat << 'EOF' > .env
ENGINE_SECRET=aibo_secure_shared_engine_token_2026_x9k2
JWT_SECRET=aibo_jwt_secret_token_min_32_characters_long_99!
RATE_LIMIT_MAX=1000
AUTH_RATE_LIMIT_MAX=1000
USER_RATE_LIMIT_MAX=120
ORCHESTRATION_MODE=canonical
REQUEST_MAX_TIMEOUT_MS=300000
ENGINE_TIMEOUT=300000
ENGINE_ENVIRONMENT=production
ENGINE_DURABLE_STATE_ENABLED=true
ENGINE_MODEL_ENABLED=true
ENGINE_LLM_DISAMBIGUATION_ENABLED=true
ENGINE_OLLAMA_BASE_URL=http://ollama:11434/v1
OLLAMA_KEEP_ALIVE=-1
EOF
```

### Step 2: Initialize Sub-Repository Environment Files

Ensure the sub-service `.env` files are initialized from their respective examples:

```powershell
# PowerShell
Copy-Item AIBO-BACKEND\.env.example AIBO-BACKEND\.env
Copy-Item AIBO-ENGINE-V1.0\.env.example AIBO-ENGINE-V1.0\.env
Copy-Item AIBO-FRONTEND\.env.example AIBO-FRONTEND\.env.local
```

```bash
# Bash
cp AIBO-BACKEND/.env.example AIBO-BACKEND/.env
cp AIBO-ENGINE-V1.0/.env.example AIBO-ENGINE-V1.0/.env
cp AIBO-FRONTEND/.env.example AIBO-FRONTEND/.env.local
```

> [!TIP]
> If you plan to use external cloud LLMs alongside or instead of Ollama, add your API keys to `AIBO-ENGINE-V1.0/.env`:
> ```env
> ENGINE_GEMINI_API_KEY=AIzaSy...
> ENGINE_OPENAI_API_KEY=sk-proj-...
> ```

---

## 3. First-Time Docker Build & Launch

### Step 1: Pre-Validate Compose Configuration

Verify that all variables resolve and there are no syntax errors:

```bash
docker compose config --quiet
```
*(If this command completes silently with exit code 0, your compose setup is 100% valid).*

### Step 2: Build the Container Images

Build all three custom multi-stage Docker images (`aibo-frontend`, `aibo-backend`, `aibo-engine`):

```bash
docker compose build --pull
```

> [!NOTE]
> The initial build downloads base images (`node:22-alpine`, `python:3.13-slim`, `nginx:alpine`, `mongo:6`, `redis:7-alpine`, `ollama/ollama:latest`) and compiles Vite and TypeScript. This step may take 2-4 minutes depending on your internet connection.

### Step 3: Launch the Stack in Detached Mode

```bash
docker compose up -d
```

### Step 4: Watch Initialization & Healthchecks

Monitor the container start order and wait until all containers report `(healthy)`:

```bash
docker compose ps
```

Expected output:
```text
NAME                     IMAGE                    STATUS                    PORTS
aibo_assistant-mongodb-1 mongo:6                  Up (healthy)              0.0.0.0:27017->27017/tcp
aibo_assistant-redis-1   redis:7-alpine           Up (healthy)              0.0.0.0:6380->6379/tcp
aibo_assistant-ollama-1  ollama/ollama:latest     Up (healthy)              0.0.0.0:11434->11434/tcp
aibo_assistant-engine-1  aibo_assistant-aibo-engine Up (healthy)            0.0.0.0:5001->5001/tcp
aibo_assistant-backend-1 aibo_assistant-aibo-backend Up (healthy)          0.0.0.0:5000->5000/tcp
aibo_assistant-frontend-1 aibo_assistant-aibo-frontend Up (healthy)        0.0.0.0:8080->80/tcp
```

---

## 4. Ollama First-Time Setup & Model Pulling

When the `ollama` container starts for the first time, its persistent volume (`ollama_data`) is empty. The model must be pulled inside the container.

### Step 1: Pull the Recommended Model

AIBO Engine V1.0 defaults to the **`qwen2.5:7b`** model (or **`qwen2.5:3b`** for lower-spec machines):

#### Recommended (Systems with 16 GB+ RAM / Dedicated GPU):
```bash
docker compose exec ollama ollama pull qwen2.5:7b
```

#### Lightweight Alternative (Systems with 8 GB RAM):
```bash
docker compose exec ollama ollama pull qwen2.5:3b
```

### Step 2: Verify the Model Download

Confirm that the model appears in Ollama's local storage:

```bash
docker compose exec ollama ollama list
```

Expected output:
```text
NAME            ID              SIZE      MODIFIED
qwen2.5:7b      843d063e1679    4.7 GB    Just now
```

### Step 3: Test Ollama Direct Inference

Verify that Ollama generates responses properly within the container network:

```bash
docker compose exec ollama ollama run qwen2.5:7b "Respond with: Ollama is fully functional in AIBO container."
```

### Step 4: Configure the Engine to Use Ollama

In `AIBO-ENGINE-V1.0/.env`, set:
```env
ENGINE_MODEL_PROVIDER=ollama
ENGINE_OLLAMA_ENABLED=true
ENGINE_OLLAMA_MODEL_NAME=qwen2.5:7b
ENGINE_OLLAMA_BASE_URL=http://ollama:11434/v1
```

Then restart the engine container to pick up the configuration:
```bash
docker compose restart aibo-engine
```

---

## 5. End-to-End Verification & Health Checks

### Automated Smoke Test

Run the repository's built-in smoke test suite:

#### Windows (PowerShell):
```powershell
powershell -ExecutionPolicy Bypass -File scripts/phase11_smoke.ps1
```

#### Linux / macOS (cURL):
```bash
# Frontend Ingress Check
curl -I http://localhost:8080

# Backend Readiness Check (Validates MongoDB & Engine connectivity)
curl -s http://localhost:5000/api/v1/health/ready | jq .

# Engine Readiness Check
curl -s http://localhost:5001/ready | jq .

# Ollama Model List
curl -s http://localhost:11434/api/tags | jq .
```

### Inspecting Datastores Directly

#### Verify MongoDB Replica Set `rs0`:
```bash
docker compose exec mongodb mongosh --quiet --eval "rs.status().members.map(m => ({ name: m.name, state: m.stateStr }))"
```
*(Should return state `PRIMARY`).*

#### Verify Redis Ping:
```bash
docker compose exec redis redis-cli ping
```
*(Should return `PONG`).*

---

## 6. Accessing the Application

Open your browser and navigate to:

- **Web Application**: [http://localhost:8080](http://localhost:8080)
- **Backend API**: [http://localhost:5000/api/v1](http://localhost:5000/api/v1)
- **Engine Health & Docs**: [http://localhost:5001/health](http://localhost:5001/health)
- **Ollama API**: [http://localhost:11434](http://localhost:11434)

Create an account on `http://localhost:8080/signup`, log in, and interact with the AI chat drawer to trigger the end-to-end cognitive workflow!

---

## 7. Daily Operational Commands Reference

### Viewing Logs

```bash
# Stream all logs with timestamps
docker compose logs -f -t

# Stream logs for a specific service
docker compose logs -f aibo-backend
docker compose logs -f aibo-engine
docker compose logs -f ollama
```

### Restarting & Rebuilding

```bash
# Restart a single service (e.g. after code edits)
docker compose restart aibo-engine

# Rebuild and recreate a service after dependency changes
docker compose up -d --build aibo-backend

# Stop the entire stack safely
docker compose stop

# Start stopped containers
docker compose start
```

### Clean Teardown & Reset

```bash
# Stop and remove containers and network (preserves volumes/data)
docker compose down

# Complete hard reset (WARNING: Wipes MongoDB data, Redis, and Ollama models)
docker compose down -v
```

---

## 8. Troubleshooting Common Docker Issues

### Issue 1: `ENGINE_SECRET must be set before starting the stack`
- **Cause**: The root `.env` file is missing or `ENGINE_SECRET` is unset.
- **Fix**: Ensure `c:\Projects\AIBO_ASSISTANT\.env` exists with non-empty values for `ENGINE_SECRET` and `JWT_SECRET`.

### Issue 2: Port Conflict Errors (`bind: address already in use`)
- **Symptoms**: `Error response from daemon: driver failed programming external connectivity on endpoint ...: bind: address already in use: 0.0.0.0:27017`
- **Cause**: A local instance of MongoDB, Redis, or Node is already running on the host machine.
- **Fix**:
  - Stop the local service (e.g., in Windows Services stop `MongoDB Server`, or `wsl --shutdown`).
  - Or terminate processes occupying the port:
    ```powershell
    # Windows
    Get-NetTCPConnection -LocalPort 27017 | Select-Object OwningProcess
    Stop-Process -Id <PID> -Force
    ```

### Issue 3: Ollama Generation Timeout
- **Cause**: Machine CPU is overloaded or 7B model requires more RAM than allocated to Docker.
- **Fix**:
  1. Switch to a smaller model: `docker compose exec ollama ollama pull qwen2.5:3b`.
  2. In Docker Desktop Settings > *Resources*, increase CPU allocation to at least 4 cores and RAM to at least 8 GB.

### Issue 4: MongoDB "Transaction numbers are only allowed on a replica set member"
- **Cause**: MongoDB started in standalone mode without initiating the replica set.
- **Fix**: Run the manual initiation command inside the container:
  ```bash
  docker compose exec mongodb mongosh --eval "rs.initiate({_id:'rs0',members:[{_id:0,host:'mongodb:27017'}]})"
  ```
