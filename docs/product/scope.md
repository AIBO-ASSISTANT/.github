# AIBO Assistant — Product Scope & Boundary Definitions

This document defines the functional scope boundaries for AIBO V1.0, distinguishing implemented release capabilities from explicitly deferred future features.

---

## 1. In Scope for V1.0 (Release Candidate)

### 1.1 Authentication & Security
- Secure user signup, login, password change, and password reset flows.
- Short-lived JWT access tokens held in-memory in the web client.
- HttpOnly, SameSite, Secure refresh cookie sessions with automatic rotation and revocation.
- Role-based authorization and multi-tier rate limiting (Auth, Write, User, Global).

### 1.2 Productivity Management
- **Task Management**: Full task lifecycle (create, update, complete, soft-delete, restore, priority filtering).
- **Calendar & Schedules**: Daily/weekly schedule viewing, ISO-8601 slot booking, and automatic slot-overlap conflict detection (HTTP 409).
- **Project Boards**: Dynamic Kanban boards with customizable columns, drag-and-drop task movement, and multi-user assignment.
- **Personal Diary**: Daily mood tracking, reflective journal entries, and historic browsing.
- **User Settings Hub**: Comprehensive user preference configuration, notification settings, active session management, and appearance toggles.

### 1.3 Cognitive AI Assistance
- Natural language intent understanding and entity parsing.
- Deterministic temporal normalization anchored strictly to reference dates.
- Goal decomposition and multi-action breakdown proposals.
- Action risk classification (`AUTO_EXECUTE`, `ASK_PERMISSION`, `REQUIRE_CONFIRMATION`).
- Multi-provider LLM gateway supporting Google Gemini, OpenAI GPT-4o-mini, and local Ollama Qwen2.5 with circuit breaker and retry protections.
- Server-authoritative HMAC-SHA256 confirmation tokens and atomic MongoDB state claiming.
- Proactive background notification analyzer for impending deadlines and scheduling conflicts.

### 1.4 Architecture & Deployment
- Containerized multi-service deployment via `docker-compose.yml`.
- Dual-mode orchestration routing (`canonical` and `legacy`).
- Monotonic request deadline enforcement with zero mutation on timeout.
- In-process metrics snapshots and structured JSON logging.

---

## 2. Explicitly Deferred / Out of Scope for V1.0

The following capabilities are explicitly deferred to subsequent release cycles (V1.1, V1.2, V2.0):

- **Autonomous Mutation Without Confirmation**: Destructive actions (deletions, bulk updates) always require user confirmation.
- **Paid SaaS Billing & Subscription Management**: Billing gateways (Stripe, LemonSqueezy) are deferred.
- **Native Mobile Apps**: Native iOS/Android clients are deferred to V2.0 (web client is fully responsive).
- **External Third-Party Calendar Sync**: Direct bi-directional synchronization with Google Calendar or Microsoft Outlook is scheduled for V1.2.
- **External Multi-Tenant Cloud Deployment**: Managed multi-region Kubernetes or cloud infrastructure is an operator deployment task.
- **Voice Interface**: Speech recognition and synthesis are deferred to future milestones.
