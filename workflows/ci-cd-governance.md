# CI/CD Governance

## Overview

AIBO Assistant enforces strict automated verification pipelines across all sub-repositories. Every pull request must pass automated linting, static type checking, unit tests, integration suites, and security audits before merging.

---

## Required Pull Request Checks by Repository

| Repository | Tech Stack | Required Pipeline Checks | Verification Command | Test Coverage / Scope |
| --- | --- | --- | --- | --- |
| **`AIBO-BACKEND`** | Node.js 22+, Express 5, TypeScript | ESLint, TypeScript Typecheck (`tsc --noEmit`), Jest Test Suite, npm audit | `npm test` & `npm run lint` | **343 unit/integration tests** + **92 E2E cross-repo scenarios** |
| **`AIBO-FRONTEND`** | React 19, Vite 8, TypeScript | ESLint, TypeScript Typecheck, Vitest Suite, Production Bundle Build | `npm run test` & `npm run build` | **87 component and unit tests** |
| **`AIBO-ENGINE-V1.0`** | Python 3.11+, FastAPI, Pydantic v2 | Mypy Static Typecheck, Pytest Test Suite, Wheel Build | `pytest tests/ -v` & `mypy src` | **606 cognitive engine tests** (NLU, Gateway, LangGraph state, fallback) |
| **`.github`** | Markdown, YAML, Mermaid | Markdown linting, JSON/YAML schema validation, link verification | Pre-commit validation | Governance documentation, templates, workflows |

**Total Verified Workspace Test Matrix**: **1,128 tests passing (100% pass rate)**.

---

## Workflow Architecture

1. **Self-Contained Workflows**: Each service repository manages its continuous integration pipeline under `.github/workflows/`.
2. **Deterministic Environments**:
   - Python pipelines run on isolated virtual environments (managed via `uv` or `pip`).
   - Node.js pipelines run on active Node LTS (Node 22) with pinned package lockfiles (`package-lock.json`).
3. **Hermetic Test Isolation**: CI tests execute against ephemeral mock datastores or in-memory fixtures. No production credentials or external cloud network calls are permitted in CI runs.
4. **Security Scanning**:
   - `npm audit` scans Node.js dependencies for CVEs.
   - `pip-audit` / `safety` scans Python dependencies.
   - GitHub secret scanning prevents credential leakage into Git trees.

---

## Release & Deployment Gates

No artifact or container image may be promoted to staging or production without:
1. Green CI run on the exact commit SHA across all affected repositories.
2. Verified pass of the cross-repo E2E test scenarios (`AIBO-BACKEND/tests/e2e/scenarios/`).
3. Zero untracked schema mutations against MongoDB models.
4. Validated execution of `python scripts/phase11_preflight.py`.

