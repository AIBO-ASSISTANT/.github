# Testing Guide

## Backend

Run:

```bash
npm run lint
npm run test
```

Coverage areas include auth, users, tasks, schedules, project management, intent integration, logging, and rate limit behavior.

## Frontend

Run:

```bash
npm run lint
npm test
npm run build
```

Current tests focus on auth lifecycle, API client auth flow, redirects, and validation helpers.

## Engine

Run:

```bash
python -m unittest discover -s tests
```

Current tests cover classification, pipeline behavior, transport validation, and clarification flows.

## Pull Request Rule

If a check cannot run locally, state the reason and risk in the pull request.
