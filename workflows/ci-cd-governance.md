# CI/CD Governance

## Current State

The service repositories do not yet have fully enforced CI/CD. This repository provides reusable workflow foundations that service repositories can adopt.

## Required Pull Request Checks

| Repository | Required checks |
| --- | --- |
| `AIBO-BACKEND` | install, lint, test, dependency audit |
| `AIBO-FRONTEND` | install, lint, test, build, dependency audit |
| `AIBO-ENGINE` | dependency install, unit tests |
| `.github` | documentation structure, issue template syntax review, workflow syntax review |

## Workflow Architecture

- reusable workflows live in `.github/workflows`
- service repositories call reusable workflows from their own workflow files
- deployment workflows remain manual placeholders until real environments exist
- CI must not require production secrets

## Security Scanning

Initial scanning should include:

- `npm audit` for Node repositories
- dependency review on pull requests when GitHub plan supports it
- secret scanning through GitHub settings
- future Python dependency scanning after engine manifest exists

## Rule

No deploy job should be added before target environment, secrets, approval, health checks, and rollback are documented.
