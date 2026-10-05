# AIBO Assistant — Security Governance & Trust Boundary Specification

This document details the security principles, cryptographic standards, perimeter controls, and threat mitigation models governing the AIBO ecosystem.

---

## 1. Security Trust Boundary Architecture

```
[ Untrusted Client ]
       │
       ▼ (1. HTTPS Request + Bearer JWT / HttpOnly Cookie)
┌────────────────────────────────────────────────────────┐
│ Perimeter Boundary (AIBO-BACKEND)                      │
│ - Strict Zod validation prior to controller dispatch   │
│ - Helmet security headers (CSP, HSTS, frameguard)      │
│ - Multi-tier IP & User rate limiting (Redis backed)    │
│ - Identity anchoring: req.user.id overrides body IDs   │
└───────────────────────┬────────────────────────────────┘
                        │
                        ▼ (2. HTTP + x-engine-secret Header)
┌────────────────────────────────────────────────────────┐
│ Cognitive Boundary (AIBO-ENGINE-V1.0)                  │
│ - Constant-time HMAC comparison (prevents timing leaks)│
│ - Action proposal allowlisting                         │
│ - Server-held cryptographic confirmation tokens        │
│ - Prompt injection sanitization                        │
└────────────────────────────────────────────────────────┘
```

---

## 2. Core Security Controls & Threat Mitigations

| Threat Vector | Mitigation Mechanism | Verification Evidence |
| :--- | :--- | :--- |
| **Client Identity Spoofing** | Authenticated JWT claims (`req.user.id`) unconditionally overwrite any user ID parameters supplied in request bodies. | E2E Scenario `SEC-03` |
| **Cross-Project / User Access** | MongoDB queries enforce explicit compound tenancy filters: `{ _id: taskId, user: req.user.id }`. | E2E Scenarios `SEC-04`, `SEC-07` |
| **Confirmation Token Replay** | Tokens are single-use; MongoDB executes atomic claim transitions (`AWAITING` -> `EXECUTING`). Replayed tokens return HTTP 409/400. | E2E Scenarios `SEC-06`, `STATE-04` |
| **Confirmation Tampering** | Confirmation tokens are HMAC-SHA256 digests of `stateRecordId` + parameters signed with `ENGINE_SECRET`. Tampered payloads fail verification. | E2E Scenario `SEC-08` |
| **Prompt Injection Escalation** | LLM outputs are treated as untrusted proposals; all actions pass through `ActionAuthorizer` policy rules before execution. | E2E Scenario `SEC-09` |
| **Direct Engine Perimeter Bypass** | Direct HTTP requests to `AIBO-ENGINE-V1.0` lacking valid `x-engine-secret` are rejected with HTTP 401. | E2E Scenario `SEC-10` |
| **Secret Leakage in Logs** | Pino and Structlog filters automatically redact sensitive keys (`password`, `token`, `secret`, `authorization`, `cookie`). | E2E Scenario `LLM-13` |
| **Zero Mutation on Error** | Any authentication, authorization, or timeout failure guarantees zero database mutations. | E2E Scenario `SEC-11` |

---

## 3. Rate Limiting Governance

Configured in `AIBO-BACKEND` via `express-rate-limit` with Redis stores:

| Limiter Scope | Window | Max Requests | Purpose |
| :--- | :---: | :---: | :--- |
| **Authentication Limiter** | 15 minutes | 1,000 requests | Protects `/api/v1/auth/login` and `/signup` against brute force. |
| **Refresh Limiter** | 15 minutes | 60 requests | Prevents excessive token refresh polling. |
| **Write Mutation Limiter** | 1 minute | 120 requests | Guards against database spam on task/schedule creations. |
| **User Request Limiter** | 1 minute | 120 requests | Enforces per-user fair queuing. |
| **Global Perimeter Limiter** | 15 minutes | 1,000 requests | DoS defense for general endpoints. |

---

## 4. Vulnerability Disclosure & Audit Policy

1. **Private Vulnerability Reporting**: Security vulnerabilities must be reported privately via the process defined in [../SECURITY.md](../SECURITY.md) and must **never** be posted to public issue trackers.
2. **Automated Secret Scanning**: Pre-commit hooks and CI workflows execute static regex scans to prevent accidental check-ins of high-confidence keys or credentials.
