# AIBO Assistant — Product Roadmap & Sequencing

The strategic engineering roadmap is maintained in [../../ROADMAP.md](../../ROADMAP.md). This document outlines product sequencing and quality gates.

---

## 1. Product Evolution Sequencing

### Phase 1: Core Productivity & Cognitive Foundations (Completed in V1.0)
- Deliver unified web user experience across Tasks, Schedules, Project Boards, Diary, and Settings.
- Establish canonical cognitive orchestration boundary (`POST /orchestrate`) with strict monotonic deadline budgets.
- Integrate multi-provider LLM Gateway (Gemini 3.6 Flash, OpenAI GPT-4o-mini, Ollama Qwen2.5) with automatic fallback and circuit breaker.
- Enforce durable confirmation state machine in MongoDB preventing double-execution races and replay attacks.
- Verify 100% test pass rate across 1,128 automated tests and 92 E2E scenarios.

### Phase 2: Operations & Observability Hardening (V1.1 Target)
- Expose connected Grafana dashboard visualizations and Prometheus metric scrapers.
- Automated snapshot backup tooling and point-in-time recovery runbooks.
- W3C OpenTelemetry distributed tracing across HTTP, WebSockets, and engine transport.

### Phase 3: Connected Ecosystem Integrations (V1.2 Target)
- Bi-directional calendar synchronization with Google Calendar and Microsoft 365.
- Browser Web Push notifications for urgent task reminders.
- Hands-free voice interface prototype for conversational task capture.

### Phase 4: Mobile & Collaborative Autonomous Agents (V2.0 Target)
- Native cross-platform mobile client for iOS and Android.
- Multi-agent collaborative reasoning loops for deep project synthesis and delegation.

---

## 2. Product Quality Gates

1. **Explicit Confirmation on High-Risk Actions**: Destructive mutations (deletion of tasks, projects, or schedule items) must never occur autonomously; they require cryptographic, server-held confirmation tokens.
2. **Deterministic Temporal Consistency**: Temporal references ("tomorrow", "next week") must resolve deterministically against anchor dates (`date_resolver.py`), preventing calendar drift.
3. **Fail-Safe Zero-Mutation Guarantee**: If a network error, provider outage, or deadline expiration occurs, no partial or dirty mutations may be written to MongoDB.
4. **Transparent Assistant Communication**: The assistant must truthfully report execution outcomes, conflicts, or errors, and must never claim success when an action was blocked or failed.
