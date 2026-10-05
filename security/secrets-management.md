# Secrets Management Policy

## Security Principles

- **Zero Plaintext Secrets in SCM**: Never commit secrets, tokens, API keys, passwords, private keys, session cookies, or production connection strings to Git.
- **Untracked Local Config**: Keep local environment secrets strictly in untracked `.env` or `.env.local` files, utilizing `.env.example` solely for schema templates with safe placeholder values.
- **Runtime Secret Injection**: In production and staging container environments, inject secrets via environment variables from secure secret stores (e.g., Vault, AWS Secrets Manager, GitHub Actions encrypted secrets).
- **Automated Scanning**: Maintain GitHub secret scanning and pre-commit hooks to block commits containing high-entropy keys or token patterns.
- **Immediate Invalidation**: Any suspected credential compromise requires immediate revocation, secret rotation, and deployment restart.

---

## Canonical Service Secrets Matrix

| Secret Variable | Consuming Service | Security Role | Storage & Rotation Rules |
| --- | --- | --- | --- |
| `JWT_SECRET` | `AIBO-BACKEND` | Cryptographic signing for short-lived user authentication JWTs. | High entropy (minimum 256 bits). Rotate with backward-compatible key verification window. |
| `ENGINE_SECRET` | `AIBO-BACKEND` & `AIBO-ENGINE-V1.0` | Pre-Shared Key (PSK) passed via `X-Engine-Secret` header for inter-service cognitive mutual auth. | Private internal secret; never exposed to browser or client bundles. |
| `CONFIRMATION_HMAC_SECRET` | `AIBO-BACKEND` | Signs HMAC SHA-256 tokens for high-risk action confirmation gates. | Critical security barrier; compromise allows forged action execution. |
| `MONGODB_URI` | `AIBO-BACKEND` | Authenticated connection string for MongoDB replica set (`rs0`). | Requires TLS connection, database credentials, and replica set name. |
| `REDIS_URL` | `AIBO-BACKEND` | Authenticated connection string for Redis 7 cache and rate-limiter. | Internal network access with optional password authentication. |
| `GEMINI_API_KEY` | `AIBO-ENGINE-V1.0` | Google Gemini LLM API authentication. | Managed in provider developer console; rate-limited per key. |
| `OPENAI_API_KEY` | `AIBO-ENGINE-V1.0` | OpenAI GPT-4o API authentication. | Managed in OpenAI organization portal. |

> [!NOTE]
> **PostgreSQL Retired**: `POSTGRES_URL` has been completely decommissioned. Database authentication is consolidated into MongoDB.

---

## Secret Rotation Triggers

Secret rotation must be immediately initiated upon:
1. Accidental commit to any public or private repository branch.
2. Exposure of raw tokens in application logs, error traces, or diagnostic dumps.
3. Departure or role transition of any operator with administrative access to secrets.
4. Identification of vulnerability in a third-party dependency with runtime environment access.
5. Routine annual security audit hygiene.
