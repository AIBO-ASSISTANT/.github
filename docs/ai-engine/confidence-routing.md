# Confidence Handling and Routing

## Current Behavior

The engine assigns deterministic confidence scores and routes supported intents to action candidates.

Known action types:

- `task.create`
- `schedule.create`
- `project.create`
- `clarification.request`

## Confidence Rules

- Confidence is not proof of correctness.
- Low confidence or ambiguous input should route to clarification.
- Backend must not execute risky mutations from engine output without validation and, where needed, user confirmation.
- Future model-backed confidence must be calibrated against evaluation data before it drives automation.

## Routing Rules

- Routing should be schema-backed.
- Each action type must have a documented payload shape.
- Adding an action type requires tests and downstream execution design.
- Clarification is a first-class outcome, not a failure.
