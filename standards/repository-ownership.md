# Repository Ownership

## Ownership Rules

- `.github` owns shared governance and cross-repo documentation.
- Service repositories own implementation, tests, and service-local docs.
- Cross-repo contracts must be documented in `.github` and implemented in service repos.
- No repository may silently redefine shared terminology.

## Responsibility Matrix

| Topic | Primary owner | Required collaborators |
| --- | --- | --- |
| Auth/session | Backend | Frontend, security reviewers |
| API response envelope | Backend | Frontend, governance |
| Engine classification contract | Engine | Backend |
| Frontend route/user workflow | Frontend | Backend when API behavior changes |
| Database migrations | Backend | Operations/security reviewers when production-impacting |
| CI/CD standards | `.github` | All service owners |
| Deployment strategy | `.github` | Backend, frontend, engine owners |
| Security policy | `.github` | All service owners |

## CODEOWNERS

A formal `CODEOWNERS` file should be added only when GitHub teams or maintainers are known. Do not create fake ownership handles.
