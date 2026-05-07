# Scalability Strategy

Scaling decisions must follow observed constraints, not speculative complexity.

## Current Philosophy

- Keep services independently understandable.
- Scale correctness and observability before scale-out infrastructure.
- Prefer clear contracts over shared implicit state.
- Introduce queues, caches, and background workers only when a concrete workflow requires them.

## Near-Term Scaling Work

- Add CI and service health checks.
- Complete frontend workflows to expose real usage paths.
- Add metrics for request latency, error rates, auth failures, engine latency, and database latency.
- Add load tests for auth, tasks, schedules, projects, and classification.

## Potential Future Scaling Patterns

| Pressure | Candidate response | Required proof |
| --- | --- | --- |
| Read-heavy API endpoints | caching | measured repeated reads and invalidation plan |
| Slow AI processing | async job or queue | latency data and user-facing workflow design |
| High schedule write contention | stronger transaction strategy | conflict metrics and concurrency tests |
| Project board reorder load | PostgreSQL indexing/locking review | query plans and load tests |
| Engine CPU pressure | horizontal engine replicas | stateless contract and health checks |

## Anti-Pattern

Do not add infrastructure because it sounds production-like. Add it when the operational cost is justified by a measured problem and ownership is clear.
