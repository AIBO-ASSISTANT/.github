# Secrets Management

## Rules

- Never commit secrets, tokens, passwords, private keys, cookies, or production connection strings.
- Keep local secrets in untracked environment files.
- Use platform secret storage for CI and deployment.
- Rotate secrets after suspected exposure.
- Do not print secrets in logs, test output, screenshots, or issue forms.

## Current Important Backend Secrets

- `JWT_SECRET`
- `MONGO_URI`
- `POSTGRES_URL`
- future deployment credentials
- future external AI provider keys

## Environment Files

- `.env.example` may document names and safe placeholder values.
- `.env.local` and real `.env` files should remain untracked.
- Production values must not be copied into docs.

## Rotation Triggers

- accidental commit
- suspicious logs
- maintainer departure from secret scope
- dependency or platform compromise
- regular production security cadence once production exists
