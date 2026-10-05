# Structured Logging Standards

## Overview

AIBO Assistant enforces structured JSON logging across all backend and cognitive engine services to guarantee traceability, ease troubleshooting, and prevent sensitive data leakage.

Logging practices adhere to the authoritative [V1.0 Observability Contract](file:///c:/Projects/AIBO_ASSISTANT/docs/operations/V1.0_OBSERVABILITY.md).

---

## Service Logging Frameworks

- **Backend (`AIBO-BACKEND`)**: Uses `pino` for asynchronous, zero-overhead structured JSON logging in production and formatted logs in development.
- **Engine (`AIBO-ENGINE-V1.0`)**: Uses Python `structlog` configured to output JSON strings with bound request and correlation contexts.

---

## Canonical Log Envelope

Every log entry emitted within a request lifecycle MUST include the following structured fields:

```json
{
  "timestamp": "2026-10-05T10:00:00.123Z",
  "level": "info",
  "service": "aibo-backend",
  "environment": "production",
  "request_id": "req_01h9x4b9zk3g8n1m2a3c4d5e6f",
  "correlation_id": "req_01h9x4b9zk3g8n1m2a3c4d5e6f",
  "event": "orchestration.completed",
  "duration_ms": 342.15,
  "status": 200,
  "user_id": "651f1a2b3c4d5e6f7a8b9c0d",
  "project_id": "651f1a2b3c4d5e6f7a8b9c0e",
  "mode": "canonical",
  "provider": "gemini"
}
```

---

## Standard Event Vocabulary

To ensure consistent log querying across components, engineers must use the standardized event taxonomy:

| Category | Standard Event Names |
| --- | --- |
| **HTTP Transport** | `request.received`, `request.completed`, `request.failed`, `request.cancelled` |
| **Cognitive Orchestration** | `orchestration.started`, `orchestration.completed`, `orchestration.failed` |
| **LLM Gateway** | `llm.request_started`, `llm.request_completed`, `llm.request_timeout`, `llm.provider_failed` |
| **Confirmations** | `confirmation.created`, `confirmation.claimed`, `confirmation.rejected`, `confirmation.expired` |
| **Action Execution** | `action.proposed`, `action.authorized`, `action.executing`, `action.completed`, `action.failed` |
| **Durable State** | `durable_state.read`, `durable_state.write`, `durable_state.unavailable` |

---

## Redaction & Security Invariants

> [!CAUTION]
> **Strict Redaction Policy**: The logging subsystem automatically strips or hashes sensitive fields before emission. Under no circumstances may plaintext secrets appear in logs.

1. **Header Redaction**: `Authorization`, `Cookie`, `Set-Cookie`, and `X-Engine-Secret` headers are redacted to `[REDACTED]`.
2. **Payload Redaction**: Passwords, refresh tokens, HMAC confirmation tokens, and credit card numbers are recursively scrubbed from request and response bodies.
3. **No PII or Prompts in Metrics**: Metrics snapshots (`/api/v1/health/metrics` and `/metrics`) must never use user IDs, conversation text, or raw queries as label dimensions.
