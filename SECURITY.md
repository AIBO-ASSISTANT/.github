# Security Policy

Security issues must be handled privately and with enough context for maintainers to reproduce and assess impact. Do not open a public issue for an undisclosed vulnerability.

## Supported Scope

| Area | Status |
| --- | --- |
| `AIBO-BACKEND` | Primary security scope: auth, sessions, validation, persistence, API exposure, logging, rate limiting. |
| `AIBO-FRONTEND` | Browser auth behavior, token storage discipline, user data exposure, dependency risk. |
| `AIBO-ENGINE` | Input validation, deterministic routing behavior, transport contract, future AI safety controls. |
| `.github` | Governance docs, workflow templates, disclosure process, security standards. |

## Reporting a Vulnerability

Use GitHub private vulnerability reporting if it is enabled for the affected repository. If it is not enabled, contact the repository maintainers through the team private channel before publishing details.

Include:

- affected repository and commit or release
- vulnerability category
- reproduction steps
- expected and actual behavior
- impact assessment
- whether credentials, tokens, or private data are involved
- suggested mitigation if known

## Response Expectations

Maintainers should:

1. acknowledge the report as soon as possible
2. assign an owner
3. reproduce or reject with evidence
4. classify severity
5. prepare a fix in a private branch when appropriate
6. document remediation and follow-up controls

## Security Baselines

- Secrets must never be committed.
- Refresh tokens must remain HttpOnly cookie owned for browser clients.
- Access tokens must remain short lived.
- Logs must not include passwords, tokens, cookies, authorization headers, or refresh-token hashes.
- Production must not allow wildcard CORS.
- Dependency updates must be reviewed for security and compatibility.

Detailed policies live under [security](security).
