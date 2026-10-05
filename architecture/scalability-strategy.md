# Scalability Strategy

## Core Principles

Scaling decisions in AIBO Assistant are driven by measured operational constraints, profiling data, and strict simplicity.

1. **Service Independence**: Services scale along distinct resource vectors (CPU-bound Python cognitive inference vs I/O-bound Node.js Express concurrency).
2. **Measure First**: Premature introduction of distributed message brokers, queues, or distributed locks is avoided until measured throughput limits are reached.
3. **Hermetic State Boundaries**: The AI Cognitive Engine is stateless; session context is passed in the request or hydrated from the backend datastore.

---

## Horizontal & Vertical Scaling Matrix

| Subsystem | Bottleneck Vector | Current Baseline | Scaling Strategy | Verification Target |
| --- | --- | --- | --- | --- |
| **`AIBO-FRONTEND`** | Static Asset Delivery | Nginx Alpine static file serving (Vite build) | Edge CDN caching (Cloudflare / CloudFront) for immutable hashed assets (`/assets/*`). | < 100ms P99 asset load |
| **`AIBO-BACKEND`** | Concurrent I/O & WebSocket Connections | Single/Clustered Node.js 22 worker processes | Node.js cluster mode / multiple container instances behind Nginx load balancer; Redis adapter for Socket.io broadcasting. | 2,000 req/sec sustained |
| **`AIBO-ENGINE-V1.0`** | CPU & LLM Provider Latency | Uvicorn ASGI workers with async HTTPX | Horizontal container replication; provider fallback circuits; local Ollama inference pooling. | < 500ms P95 classification |
| **`MongoDB (rs0)`** | Read/Write Disk I/O & Indexing | Primary node with local NVMe storage | Read preference (`secondaryPreferred`) for read-heavy feeds; targeted compound indexes on `(owner_id, is_deleted, status)`. | < 15ms P99 query latency |
| **`Redis 7`** | Memory & Rate-Limiting Contention | Single-instance Redis 7 | Redis cluster or sentinel topology with explicit TTL eviction policies. | < 2ms P99 response |

---

## Concurrency & Data Integrity Strategy

1. **Kanban Board Reordering**: Drag-and-drop board reordering uses fractional indexing or sparse integer positions with atomic MongoDB `$bulkWrite` operations, eliminating lock contention.
2. **Task State Transitions**: Status updates use atomic conditional updates (`findOneAndUpdate({ _id, version })`) to prevent race conditions during concurrent user and AI updates.
3. **Confirmation Token Idempotency**: High-risk action execution relies on atomic MongoDB single-use token claiming (`findOneAndUpdate({ token, claimed: false }, { $set: { claimed: true } })`), mathematically preventing replay attacks.
