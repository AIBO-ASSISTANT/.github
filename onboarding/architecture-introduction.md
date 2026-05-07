# Architecture Introduction

AIBO is organized as three service repositories governed by this `.github` repository.

## Repositories

- `AIBO-FRONTEND`: browser UI and frontend auth lifecycle.
- `AIBO-BACKEND`: HTTP API, auth/session, validation, persistence, and engine orchestration.
- `AIBO-ENGINE`: deterministic classification, extraction, routing, and engine schemas.
- `.github`: standards, templates, architecture, security, CI/CD governance, onboarding, and operations docs.

## Request Shape

The browser talks to the frontend. The frontend calls backend `/api/v1`. The backend validates and authorizes requests, reads/writes databases, and calls the engine for classification when needed.

## Important Boundary

The engine is not a database writer and the frontend is not an auth authority. The backend is the security and persistence boundary.
