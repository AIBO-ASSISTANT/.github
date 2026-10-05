# Dependency Management Standards

## Guiding Principles

- **Zero Unjustified Dependencies**: Add third-party packages only when they eliminate substantial complexity, provide hardened security, or deliver proven domain capabilities.
- **Strict Lockfile Pinning**: All dependencies must be pinned via lockfiles (`package-lock.json` for Node, `uv.lock` or verified wheels for Python).
- **Vulnerability Auditing**: Automated dependency vulnerability scans (`npm audit`, `pip-audit`) are enforced in CI.
- **Architectural Approval**: Introducing new databases, transport protocols, or core state managers requires an Architectural Decision Record (ADR).

---

## Subsystem Dependency Standards

### 1. `AIBO-BACKEND` (Node.js 22+ LTS)
- Use `npm ci` for deterministic, clean installs in CI and production Docker builds.
- Critical libraries (Express 5, Mongoose, Zod, Pino, ioredis) must be reviewed for semantic version updates and security advisories.
- Prohibit runtime native compilation dependencies (node-gyp) where pre-built binaries or pure TypeScript alternatives exist.

### 2. `AIBO-FRONTEND` (React 19, Vite 8, TypeScript 6)
- Maintain minimal client bundle footprint; avoid bloated UI component suites with heavy CSS-in-JS runtimes.
- Approved core stack: `react`, `react-dom`, `react-router-dom` (v7), `zustand` (v5), `recharts` (v3), `lucide-react`, `axios`, `socket.io-client`.
- Development utilities: `vitest`, `@testing-library/react`, `eslint`, `prettier`.

### 3. `AIBO-ENGINE-V1.0` (Python 3.11+)
- Formal package manifest codified in [`pyproject.toml`](file:///c:/Projects/AIBO_ASSISTANT/AIBO-ENGINE-V1.0/pyproject.toml).
- Local virtual environments managed via `uv` or `pip`.
- Approved core dependencies: `fastapi`, `uvicorn`, `pydantic` (v2), `httpx`, `structlog`.
- Cognitive provider SDKs: `google-genai`, `openai`, `ollama` integrated through the Multi-Provider Gateway with circuit breaker fallbacks.
- Testing and quality: `pytest`, `pytest-asyncio`, `mypy`, `build`.

---

## Dependency Lifecycle & Security Hygiene

1. **Dependabot / Vulnerability Alerts**: High and Critical CVEs must be patched within 7 business days.
2. **Lockfile Audits**: Pull requests modifying `package-lock.json` or `pyproject.toml` without accompanying functional rationale are rejected during code review.
