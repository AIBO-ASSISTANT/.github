# Monitoring Strategy

## Current State

No centralized monitoring stack is implemented.

## Metrics Roadmap

Track:

- request rate
- response latency
- error rate by status family
- auth failures
- refresh failures and reuse detections
- database query latency
- engine request latency
- engine classification distribution
- rate limit triggers

## Tracing Roadmap

Add tracing after request IDs and metrics are stable. Trace boundaries should include:

- frontend request initiation where practical
- backend request handling
- database calls
- engine calls

## Alerting Philosophy

Alerts should represent user impact or security risk. Do not alert on every warning log.

Candidate alerts:

- sustained 5xx rate
- auth/session anomaly spike
- database unavailable
- engine unavailable above threshold
- deployment health check failure
- backup failure once backups exist
