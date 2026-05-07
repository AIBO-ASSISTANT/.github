# Glossary

| Term | Meaning |
| --- | --- |
| Access token | Short-lived bearer token returned to the frontend and stored in memory. |
| Refresh token | Backend-managed browser session token stored in an HttpOnly cookie. |
| Engine | `AIBO-ENGINE`, the deterministic AI domain layer. |
| Classification | Engine output identifying intent and confidence. |
| Entity extraction | Engine process that extracts fields such as date, time, title, participant, or priority. |
| Action candidate | Engine-routed structured action proposal, such as `task.create`. |
| Clarification | Engine output indicating more user information is needed. |
| API envelope | Standard success/error response structure returned by backend. |
| ADR | Architecture Decision Record. |
| Maturity matrix | Canonical implemented/partial/planned/prototype/experimental status tracker. |
| Governance repo | This `.github` repository. |
