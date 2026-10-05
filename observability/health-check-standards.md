# AIBO Assistant — Health Check & Probe Standards

This document specifies the health, readiness, dependency, and metrics endpoints implemented across the AIBO ecosystem.

---

## 1. Health Probe Catalog

| Endpoint | Target Service | Probe Role | Evaluation Criteria |
| :--- | :--- | :--- | :--- |
| `GET /api/v1/health/live` | `AIBO-BACKEND` | Liveness | Returns HTTP 200 if the Node.js event loop is responsive. |
| `GET /api/v1/health/ready`| `AIBO-BACKEND` | Readiness | Returns HTTP 200 if MongoDB, Redis, and Engine dependencies are connected and reachable. |
| `GET /api/v1/health/dependencies` | `AIBO-BACKEND` | Diagnostics | Returns granular status for MongoDB, Redis, and Engine services with latency measurements. |
| `GET /api/v1/health/metrics` | `AIBO-BACKEND` | Metrics | Returns in-process metrics snapshot: active connections, requests per route, error counts, latency percentiles. |
| `GET /health` | `AIBO-ENGINE-V1.0` | Liveness | Returns HTTP 200 if the FastAPI application is alive. |
| `GET /ready` | `AIBO-ENGINE-V1.0` | Readiness | Returns HTTP 200 if model configuration and environment keys are loaded. |
| `GET /metrics` | `AIBO-ENGINE-V1.0` | Metrics | Returns cognitive pipeline metrics: NLU latency, provider call counts, confirmation claims. |

---

## 2. Docker Compose Healthcheck Configuration

The deployment orchestrator (`docker-compose.yml`) leverages these probes to govern container startup and dependency ordering:

### Backend Probe:
```yaml
healthcheck:
  test: ["CMD", "node", "-e", "fetch('http://localhost:5000/api/v1/health/ready').then(r => r.ok ? process.exit(0) : process.exit(1)).catch(() => process.exit(1))"]
  interval: 10s
  timeout: 5s
  retries: 5
  start_period: 15s
```

### Engine Probe:
```yaml
healthcheck:
  test: ["CMD", "python", "-c", "import urllib.request; urllib.request.urlopen('http://localhost:5001/ready', timeout=2)"]
  interval: 10s
  timeout: 5s
  retries: 5
  start_period: 15s
```

### Frontend Probe:
```yaml
healthcheck:
  test: ["CMD-SHELL", "wget -q --spider http://127.0.0.1:80 || exit 1"]
  interval: 10s
  timeout: 5s
  retries: 3
  start_period: 5s
```

---

## 3. Security & Information Disclosure Rules

1. Health checks must **never** return raw database connection strings, passwords, or API keys.
2. If a dependency check fails, the payload returns `{ "status": "degraded", "dependency": "mongodb", "error": "connection_timeout" }` with HTTP 503, sanitized against stack trace leakage.
3. `/metrics` endpoints are intended for internal network or authenticated scraper access.
