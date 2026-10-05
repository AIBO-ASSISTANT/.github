# Operational Runbooks Directory

This document catalogs the authoritative runbooks governing AIBO Assistant V1.0 operations, deployment, incident response, and rollback.

---

## Authoritative Operations Runbooks

All production procedures are codified in the root [`docs/operations/`](file:///c:/Projects/AIBO_ASSISTANT/docs/operations/) directory:

| Runbook | Purpose & Scope | Primary Triggers |
| --- | --- | --- |
| **[V1.0 Production Deployment Runbook](file:///c:/Projects/AIBO_ASSISTANT/docs/operations/V1.0_PRODUCTION_DEPLOYMENT_RUNBOOK.md)** | Controlled release rollout sequence, image verification, dependency boot order, preflight checks, and smoke tests. | Scheduled release windows, RC promotion. |
| **[V1.0 Production Rollback Runbook](file:///c:/Projects/AIBO_ASSISTANT/docs/operations/V1.0_PRODUCTION_ROLLBACK_RUNBOOK.md)** | Emergency rollback steps, image reversion, schema backward compatibility, and cache eviction. | Deployment stop conditions, elevated 5xx error spikes, unrecoverable provider failure. |
| **[V1.0 Canonical Rollout Runbook](file:///c:/Projects/AIBO_ASSISTANT/docs/operations/V1.0_CANONICAL_ROLLOUT_RUNBOOK.md)** | Step-by-step traffic migration from legacy routes to canonical `POST /orchestrate`. | Orchestration traffic shifting, telemetry verification. |
| **[V1.0 Incident Runbook](file:///c:/Projects/AIBO_ASSISTANT/docs/operations/V1.0_INCIDENT_RUNBOOK.md)** | Incident classification (Sev 1 - Sev 3), triage trees, provider circuit breaking, and mitigation procedures. | Outages, latency alerts, HMAC token validation anomalies, database degradation. |
| **[V1.0 Production Readiness Review](file:///c:/Projects/AIBO_ASSISTANT/docs/operations/V1.0_PRODUCTION_READINESS.md)** | Release readiness sign-off criteria covering testing, security, observability, and container sanity. | Release gate approvals. |
| **[V1.0 Observability Contract](file:///c:/Projects/AIBO_ASSISTANT/docs/operations/V1.0_OBSERVABILITY.md)** | In-process metrics definitions, log sanitization standards, correlation ID propagation, and health probes. | System diagnosis and monitoring. |

---

## Creating New Runbooks

When drafting a new service or scenario-specific runbook, authors must conform to the standard structure defined in [`templates/runbook-template.md`](file:///c:/Projects/AIBO_ASSISTANT/.github/templates/runbook-template.md). Required sections:
1. Target Service & Ownership
2. Preconditions & Access Requirements
3. Health Probes & Verification Commands
4. Step-by-Step Procedure
5. Rollback Procedure
6. Escalation Matrix

