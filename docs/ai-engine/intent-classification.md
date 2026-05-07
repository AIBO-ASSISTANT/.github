# Intent Classification Architecture

`AIBO-ENGINE` currently uses deterministic rule-based classification.

## Current Contract

Transport endpoint:

```http
POST /classify
Content-Type: application/json
```

Request:

```json
{
  "input": "Schedule a meeting tomorrow at 3 PM"
}
```

Response:

```json
{
  "intent": "scheduling",
  "confidence": 0.92,
  "processed_input": "schedule a meeting tomorrow at 3 pm"
}
```

## Supported Intent Values

- `task_creation`
- `scheduling`
- `project`
- `unknown`

## Rules

- `unknown` is valid and preferred over false certainty.
- Confidence must stay within `0` and `1`.
- Backend must validate engine responses.
- New intents require tests, docs, routing behavior, and API impact review.
