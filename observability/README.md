# Observability & Operations Governance

This directory defines the operational standards, telemetry protocols, health probe contracts, and incident runbooks for AIBO Assistant.

---

## Observability Architecture

AIBO Assistant V1.0 implements a unified observability contract governed by [`docs/operations/V1.0_OBSERVABILITY.md`](file:///c:/Projects/AIBO_ASSISTANT/docs/operations/V1.0_OBSERVABILITY.md):
- **Structured JSON Logging**: Pino (Backend) and Structlog (Engine) with standardized event vocabulary and automatic PII/credential redaction.
- **Request Correlation**: End-to-end tracing via `X-Request-Id` and `correlation_id` across browser, backend, and engine layers.
- **Health & Readiness Probes**: Multi-tier probes (`/live`, `/ready`, `/dependencies`, `/metrics`) preventing premature traffic routing.
- **In-Process Telemetry**: Bounded in-memory counters and latency percentiles (P50, P95, P99) exposed for scraping without external dependencies.
- **Production Runbooks**: Complete operational guides in [`docs/operations/`](file:///c:/Projects/AIBO_ASSISTANT/docs/operations/) for rollout, incident response, rollback, and production readiness.

---

## Documentation Index

| Document | Purpose & Scope |
| --- | --- |
| **[Logging Standards](logging-standards.md)** | Structured log envelopes, standard event vocabulary, and strict redaction rules. |
| **[Health-Check Standards](health-check-standards.md)** | Probe behavior, status codes, dependency evaluation, and container healthchecks. |
| **[Monitoring Strategy](monitoring-strategy.md)** | In-process metrics architecture, key telemetry dimensions, and alert thresholds. |
| **[Incident Response](incident-response.md)** | Severity classifications (SEV 1 - SEV 4), response timelines, and escalation paths. |
| **[Backup & Recovery](backup-recovery.md)** | MongoDB replica set point-in-time recovery, Redis snapshots, and RPO/RTO objectives. |
| **[Maturity Roadmap](maturity-roadmap.md)** | Evolution of observability capabilities from V1.0 to future cloud monitoring. |
