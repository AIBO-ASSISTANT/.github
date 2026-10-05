# Request Lifecycle

## Overview

This sequence diagram and specification detail the end-to-end request lifecycle across the AIBO Assistant architecture.

```mermaid
sequenceDiagram
  autonumber
  participant Browser as Browser Client (React 19)
  participant Ingress as Nginx Reverse Proxy (8080)
  participant Backend as AIBO-BACKEND (Express 5 / Node 22)
  participant AuthGuard as Auth & Security Middleware
  participant DataStore as MongoDB (rs0) & Redis 7
  participant Engine as AIBO-ENGINE-V1.0 (FastAPI / 5001)

  Browser->>Ingress: User interaction / API call with X-Request-Id
  Ingress->>Backend: Reverse-proxy HTTP request to /api/v1/*
  Backend->>AuthGuard: Verify JWT Cookie/Bearer & Token-Bucket Rate Limits
  AuthGuard-->>Backend: Authorized User Context
  alt Standard CRUD Operation
    Backend->>DataStore: Mongoose CRUD operation / Redis cache query
    DataStore-->>Backend: Document result or cache hit
    Backend-->>Ingress: Normalized { status, data, requestId } envelope
    Ingress-->>Browser: HTTP 200 OK + JSON payload
  else Cognitive Orchestration Call
    Backend->>Engine: POST /orchestrate with Engine-Secret & monotonic deadline
    Engine->>Engine: NLU classification, Entity extraction, LangGraph state machine
    Engine-->>Backend: OrchestrationResult (actions, confidence, plan)
    Backend->>Backend: Validate permissions & evaluate action risk
    alt High-Risk Action Proposed (e.g. Delete, Reassign)
      Backend-->>Ingress: RequiresConfirmation payload with HMAC token
      Ingress-->>Browser: UI renders interactive Confirmation Dialog
    alt Safe Read / Mutate Action
      Backend->>DataStore: Execute atomic MongoDB transaction
      DataStore-->>Backend: Committed state
      Backend-->>Ingress: Executed action response
      Ingress-->>Browser: UI updates state optimistically
    end
  end
```

---

## Backend Request Rules

1. **Request Tracking**: Every request binds an incoming `X-Request-Id` or generates a UUIDv4, attaching it to structured logs and the outbound response envelope.
2. **Schema Validation**: All request payloads are strictly validated using `Zod` schemas before entering business service logic.
3. **Authorization Before Data Access**: Tenant and ownership checks execute before reading or mutating any user-owned documents.
4. **Normalized Envelopes**: All successful responses return `{ status: "success", data: ..., requestId: "..." }`. Errors return `{ status: "error", code: "...", message: "...", requestId: "..." }`.

---

## Frontend Request Rules

1. **Centralized API Client**: All network traffic routes through the pre-configured Axios/Fetch client (`src/services/api/apiClient.ts`).
2. **Memory-Only Access Tokens**: Access tokens are kept strictly in memory (Zustand auth store); refresh tokens reside in `HttpOnly`, `SameSite=Lax/Strict` cookies.
3. **Optimistic Updates**: Board and task mutations update local client state immediately, rolling back gracefully if the server returns an error.
