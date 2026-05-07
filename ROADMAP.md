# AIBO Roadmap

The roadmap separates implemented capability from strategic direction. It does not claim production infrastructure or advanced AI systems that are not present in the codebase.

## Current Maturity

| Dimension | Maturity |
| --- | --- |
| Backend API | Strongest area; core API modules, auth/session behavior, logging, validation, rate limiting, and tests exist. |
| Frontend | Early application scaffold with auth lifecycle and limited screens. Product workflows need completion. |
| Engine | Deterministic prototype with stable contracts, tests, and transport adapter. |
| CI/CD | Governance foundations are defined here; service repositories still need workflow adoption. |
| Deployment | Strategy and readiness rules are documented; production deployment is not implemented. |
| Observability | Structured backend logging exists; metrics, tracing, dashboards, and alerting are planned. |

## Near-Term Priorities

1. Wire reusable CI workflows into `AIBO-BACKEND`, `AIBO-FRONTEND`, and `AIBO-ENGINE`.
2. Complete frontend integration for implemented backend auth/task/schedule/project flows.
3. Align backend OpenAPI coverage with implemented project and schedule APIs.
4. Add engine dependency manifest and service readiness checks.
5. Define environment-specific deployment targets before adding deployment automation.
6. Add production readiness checklist for each service.

## Operational Maturity Roadmap

| Phase | Goal | Exit criteria |
| --- | --- | --- |
| Foundation | Repeatable local and CI validation | Lint/test/build checks run in PRs for every service. |
| Pre-production | Controlled deployability | Environment inventory, secrets process, health checks, rollback plan, and release approvals exist. |
| Production readiness | Observable runtime | Logs, metrics, traces, alerts, dashboards, backup/recovery expectations, and incident runbooks are active. |
| Scaling readiness | Known bottlenecks and capacity plan | Database ownership, queue/caching decisions, load testing, and service SLOs are documented and tested. |

## AI Maturity Roadmap

| Phase | Goal | Exit criteria |
| --- | --- | --- |
| Deterministic baseline | Keep current rules predictable | Classification, extraction, routing, and clarification tests cover supported intents. |
| Evaluation harness | Measure quality before expansion | Versioned test corpus, precision/recall style metrics, and regression thresholds exist. |
| Assisted intelligence | Add richer NLP only behind contracts | Any LLM or model provider is isolated behind the engine contract, evaluated, observable, and fail-safe. |
| Production AI operations | Govern model changes | Prompt/model versioning, safety review, fallback routing, and monitoring are mandatory. |

## Scaling Roadmap

- Keep MongoDB ownership for users, sessions, tasks, and schedules until a measured bottleneck requires a change.
- Keep PostgreSQL ownership for project management relational workflows.
- Introduce queues only when asynchronous work exists and retry behavior is designed.
- Introduce caching only for measured read pressure or expensive deterministic computation.
- Add horizontal scaling only after session, cookie, CORS, health check, and database connection behavior is production-ready.

## Production Readiness Roadmap

Production is not declared until all of the following are true:

- CI is required and passing on protected branches.
- Deployment target, environment variables, secrets, and rollback process are documented.
- Health checks exist for frontend, backend, engine, MongoDB, and PostgreSQL dependencies.
- Logs are centralized and redacted.
- Security disclosure process is active.
- Backup and recovery expectations are documented and tested.
- Feature maturity matrix is reviewed for release accuracy.
