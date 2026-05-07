# Documentation System

This directory contains product and domain documentation. Cross-cutting architecture, standards, operations, API governance, security, deployment, ADRs, and onboarding live in top-level directories so they are easy to find from the repository root.

## Rules

- Document current behavior separately from planned behavior.
- Link to the canonical document instead of copying the same explanation.
- Update the feature maturity matrix when capability status changes.
- Prefer short, enforceable sections over broad commentary.
- Explain why a rule exists when the reason affects engineering judgment.

## Product Docs

- [Product vision](product/vision.md)
- [Scope](product/scope.md)
- [MVP boundaries](product/mvp-boundaries.md)
- [Feature maturity matrix](product/feature-maturity-matrix.md)
- [Roadmap](product/roadmap.md)

## Engineering Domain Docs

- [Backend standards](backend/backend-standards.md)
- [Frontend component architecture](frontend/component-architecture.md)
- [Frontend state management](frontend/state-management.md)
- [Frontend UI consistency](frontend/ui-consistency.md)
- [Frontend accessibility](frontend/accessibility.md)
- [AI intent classification](ai-engine/intent-classification.md)
- [AI entity extraction](ai-engine/entity-extraction.md)
- [AI confidence and routing](ai-engine/confidence-routing.md)
- [AI limitations](ai-engine/limitations.md)
- [AI evaluation strategy](ai-engine/evaluation-strategy.md)
