# Health-Check Standards

## Health Check Types

| Type | Purpose |
| --- | --- |
| Liveness | Process is running and able to respond. |
| Readiness | Service can handle real traffic and dependencies are available. |
| Dependency | Database, engine, or external service status. |

## Current State

The backend has a test/health-style route. Formal readiness and dependency checks are not complete across all services.

## Requirements

- Backend readiness should cover MongoDB, PostgreSQL, and engine dependency expectations.
- Engine should expose a lightweight health endpoint before production deployment.
- Frontend hosting should expose static availability or platform health.
- Health responses must not leak secrets or internal topology.
