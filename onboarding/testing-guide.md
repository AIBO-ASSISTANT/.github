# AIBO Assistant — Comprehensive Testing Guide

This guide details the complete multi-tiered testing strategy, execution commands, and acceptance criteria across all AIBO subsystems (totaling **1,128 automated tests**).

---

## 1. Testing Pyramid Overview

```
                        ▲
                       / \
                      /   \
                     / E2E \       Cross-Repository E2E (92 Scenarios)
                    /───────\      Real Engine + Backend + Isolated Mongo
                   / Integr. \     Service Integration Tests (Jest & Pytest)
                  /───────────\
                 /  Unit Tests \   Fast Isolated Tests (Backend, Engine, Frontend)
                /───────────────\
               / Static Analysis \ TypeScript (tsc), MyPy (Python), ESLint
              └───────────────────┘
```

---

## 2. Test Execution Matrix

| Subsystem | Test Harness | Test Count | Command | Key Coverage Areas |
| :--- | :--- | :---: | :--- | :--- |
| **Cognitive Engine** | Pytest 9 + Asyncio | **606** | `cd AIBO-ENGINE-V1.0; uv run pytest` | NLU, entity parsing, temporal date anchoring, planning, authorization policies, LLM gateway retries/fallbacks, cognitive memory. |
| **Backend Unit & API** | Jest 30 (ts-jest) | **343** | `cd AIBO-BACKEND; npm test` | Auth, users, tasks, schedules, projects, columns, notifications, rate limits, request ID propagation, error envelopes. |
| **Frontend Unit** | Node Native Test Runner | **32** | `cd AIBO-FRONTEND; npm run test:unit` | Auth lifecycle, token storage, API client interceptors, redirect preservation, environment config. |
| **Frontend Component** | Vitest 5 + Testing Lib | **55** | `cd AIBO-FRONTEND; npm run test:component`| SettingsPage hub, ProjectManager Kanban, Sidebar, Theme switcher, Shortcuts modal, Offline banner. |
| **Cross-Repo E2E** | Custom TSX Runner | **92** | `cd AIBO-BACKEND; npm run test:e2e` | Multi-turn dialogues, confirmation flows, replay rejection, cancellation, 429/503 fault injection, durable state restarts. |

**Total Automated Test Count**: **1,128 tests (100% Passing)**

---

## 3. Running Cross-Repository E2E Tests

The E2E test runner (`AIBO-BACKEND/tests/e2e/runner.ts`) coordinates a real test environment:
1. Spawns an isolated `AIBO-ENGINE-V1.0` process on port 5001 configured with the `deterministic_mock` provider.
2. Spawns an `AIBO-BACKEND` test server on port 5000.
3. Connects to an isolated MongoDB test database (`aibo_backend_test_e2e`).
4. Executes all 92 scenarios sequentially, testing end-to-end user journeys.
5. Gracefully terminates child processes and releases network ports.

### Execution Command:
```powershell
cd c:\Projects\AIBO_ASSISTANT\AIBO-BACKEND
$env:PYTHON_EXEC = "c:\Projects\AIBO_ASSISTANT\AIBO-ENGINE-V1.0\.venv\Scripts\python.exe"
npm run test:e2e
```

---

## 4. Static Analysis & Type Checking

Run all static analysis tools before submitting pull requests:

```powershell
# Cognitive Engine type checking
cd AIBO-ENGINE-V1.0
uv run mypy src

# Backend TypeScript build
cd ../AIBO-BACKEND
npm run build
npm run lint

# Frontend TypeScript check and lint
cd ../AIBO-FRONTEND
npm run type-check
npm run lint
```

---

## 5. Acceptance Policy

- **Zero Test Failures**: 100% of tests must pass before merging to `main`.
- **Zero Database Mutation on Error**: Any test testing fault injection or failure modes must verify that the database count remains unchanged.
- **Fail-Closed Security**: Unauthenticated requests or tampered confirmation tokens must be rejected with HTTP 401 or 403.
