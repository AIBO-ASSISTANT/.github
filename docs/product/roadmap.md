# Product Roadmap

The strategic roadmap is maintained in [../../ROADMAP.md](../../ROADMAP.md). This document exists to keep product-specific sequencing clear.

## Product Sequence

1. Make core backend workflows usable from the frontend.
2. Stabilize API documentation and generated client expectations.
3. Add a chatbot capture surface only after task, schedule, and project flows are reliable.
4. Improve engine extraction and routing with evaluation data.
5. Add planning recommendations only after measurement, confirmation, and fallback behavior are designed.

## Product Quality Gates

- A product feature cannot move to `Implemented` without an intended user interface or documented API-only status.
- AI-assisted behavior must expose uncertainty and allow user correction.
- Any feature that stores or mutates user data must include validation, authorization, and error handling.
- Roadmap items must reference current maturity; planned features cannot be described as existing behavior.
