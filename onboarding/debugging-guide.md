# Developer Debugging & Troubleshooting Guide

This guide provides systematic troubleshooting procedures for diagnosing issues across the AIBO Assistant stack.

---

## 1. Request Tracing & Correlation

1. **Capture the `X-Request-Id`**: Inspect the browser DevTools Network tab or API response JSON for the `requestId` field.
2. **Filter Backend Logs**: Search structured logs for `"request_id": "<ID>"`. This reveals route entry, authenticated user, validation outcome, and response duration.
3. **Trace Engine Cognitive Flow**: In engine logs, the same ID is bound as `"correlation_id": "<ID>"`. Inspect NLU intent classification scores, entity resolution, and LLM Gateway latency.

---

## 2. Authentication & Session Failures

| Symptom | Probable Cause | Diagnostic & Fix |
| --- | --- | --- |
| **HTTP 401 on API Calls** | Expired or missing access token. | Confirm frontend in-memory token is populated. Check if refresh call succeeds via `/api/v1/auth/refresh`. |
| **Refresh Cookie Rejected** | CORS misconfiguration or cookie SameSite mismatch. | Verify `CORS_ORIGIN` matches frontend host. For local dev (`http`), ensure `AUTH_COOKIE_SECURE=false`. |
| **Session Invalidated Across Tabs** | BroadcastChannel logout triggered. | Check if another browser tab issued a logout command. |

---

## 3. Cognitive Engine & Orchestration Issues

| Symptom | Probable Cause | Diagnostic & Fix |
| --- | --- | --- |
| **HTTP 502 / 504 from Backend on Chat** | Engine unreachable or timeout exceeded. | Confirm `aibo-engine` container is running on port 5001. Test engine liveness: `curl http://localhost:5001/health`. |
| **Invalid Engine Secret** | `ENGINE_SECRET` mismatch between Backend and Engine. | Verify both `.env` files share identical `ENGINE_SECRET` strings. |
| **LLM Provider Outage** | External provider rate-limiting or quota exhaustion. | Engine automatically trips circuit breakers. Check logs for `llm.provider_failed`. Fallback to Ollama or mock mode if necessary. |
| **Missing Confirmation Dialog in UI** | High-risk action missing HMAC token. | Inspect engine response for `requires_confirmation: true`. Verify backend generated `confirmation_token`. |

---

## 4. Datastore & Persistence Issues

| Symptom | Probable Cause | Diagnostic & Fix |
| --- | --- | --- |
| **Transaction Error: "Transaction numbers are only allowed on a replica set member"** | MongoDB running in standalone mode instead of replica set `rs0`. | Multi-document ACID transactions require a replica set. Connect via `mongosh` and verify `rs.status()`. In Docker, ensure MongoDB initialized with `--replSet rs0`. |
| **Redis Connection Refused** | Redis daemon not running on port 6380. | Check Redis container status: `docker ps -f name=redis` or test `redis-cli -p 6380 ping`. |
| **Tenant Data Isolation Leak** | Missing `ownerId` scoping in Mongoose queries. | Ensure Mongoose queries explicitly include `{ ownerId: req.user.id }` or check project membership. |
