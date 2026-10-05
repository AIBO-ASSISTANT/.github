# AIBO Assistant — Monitoring & Observability Strategy

This document defines the metrics collection, tracing propagation, and alerting architecture across the AIBO ecosystem.

---

## 1. Observability Architecture

```
┌─────────────────┐       x-request-id        ┌──────────────────┐       x-correlation-id       ┌──────────────────┐
│  AIBO-FRONTEND  │ ────────────────────────► │   AIBO-BACKEND   │ ───────────────────────────► │ AIBO-ENGINE-V1.0 │
│ (Browser Client)│ ◄──────────────────────── │  (Node.js / Pino)│ ◄─────────────────────────── │ (FastAPI/Struct) │
└─────────────────┘       Latency Timers      └────────┬─────────┘       Latency Percentiles    └──────────────────┘
                                                       │
                                                       ├─► MongoDB Slow Query Logger (>250ms)
                                                       ├─► Backend Slow Request Logger (>1000ms)
                                                       └─► In-Process Metrics Snapshot (/metrics)
```

---

## 2. In-Process Metrics Catalog

Both `AIBO-BACKEND` and `AIBO-ENGINE-V1.0` expose bounded in-memory metrics snapshots without requiring external daemon infrastructure:

### Backend Metrics (`GET /api/v1/health/metrics`):
- `http_requests_total`: Aggregated by HTTP method, path prefix, and status code.
- `http_request_duration_ms`: Rolling latency percentiles (p50, p95, p99).
- `auth_failures_total`: Failed logins, expired tokens, revoked refresh attempts.
- `slow_queries_total`: Database queries exceeding `SLOW_QUERY_THRESHOLD_MS` (250ms).
- `slow_requests_total`: HTTP requests exceeding `SLOW_REQUEST_THRESHOLD_MS` (1000ms).
- `active_socket_connections`: Current live Socket.io clients connected.

### Engine Metrics (`GET /metrics`):
- `orchestration_requests_total`: Counts by status (`completed`, `awaiting_confirmation`, `needs_clarification`, `execution_failed`).
- `llm_gateway_calls_total`: Counts by provider (`gemini`, `openai`, `ollama`, `deterministic_mock`) and attempt type (`primary`, `retry`, `fallback`).
- `circuit_breaker_trips_total`: Events where providers tripped into temporary open state.
- `durable_confirmation_claims_total`: Successful atomic state claims vs. replay rejections.

---

## 3. Distributed Request Correlation

1. **`x-request-id`**: Assigned at perimeter ingress (or generated as a high-entropy UUID if missing). Returned in response headers and logged with every event.
2. **`x-correlation-id`**: Preserved across asynchronous multi-turn dialogues, durable confirmations, and background worker queues to correlate the entire conversational session.

---

## 4. Operational Alerting Thresholds

| Alert Name | Trigger Condition | Severity | Recommended Action |
| :--- | :--- | :---: | :--- |
| `HighErrorRate5xx` | HTTP 5xx errors > 2.0% over 5m window | P1 - Critical | Inspect backend Pino logs; check MongoDB/Redis connection health. |
| `CognitiveEngineDown` | Engine readiness probe failing > 30s | P1 - Critical | Check `aibo-engine` container logs and local Ollama / API keys. |
| `DatabaseDegraded` | Mongo query latency p95 > 500ms over 5m | P2 - Major | Check slow query logs, missing indexes, or container memory limits. |
| `LLMProviderFailover` | Primary LLM falling back to secondary > 10% | P3 - Warning | Check primary provider API quota or network latency. |
| `AuthAnomalySpike` | Auth failures > 50 in 1m from single IP | P3 - Warning | Trigger rate limiting blacklist; review security logs. |
