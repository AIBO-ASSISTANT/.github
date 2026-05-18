# Intent Classification Architecture

`AIBO-ENGINE` currently uses deterministic rule-based classification.

## Current Contracts

AI engine analysis endpoint:

```http
POST /ai-engine/analyze
Content-Type: application/json
```

Request:

```json
{
  "text": "Schedule a meeting tomorrow at 3 PM",
  "reference_date": "2026-05-16",
  "source": "aibo-backend"
}
```

Response:

```json
{
  "schema_version": "2.0",
  "classification": {
    "intent": "scheduling",
    "confidence": 0.92,
    "processed_input": "schedule a meeting tomorrow at 3 pm"
  },
  "entities": {
    "title": "meeting",
    "date": "2026-05-17",
    "time": "15:00",
    "duration_minutes": null,
    "priority": "medium",
    "project_name": null,
    "participants": [],
    "raw": {
      "title": "meeting",
      "date": "tomorrow",
      "time": "at 3 PM",
      "duration": null,
      "priority": null,
      "project_name": null,
      "participants": null
    },
    "unresolved": []
  },
  "warnings": []
}
```

Decision engine endpoint:

```http
POST /decision-engine/decide
Content-Type: application/json
```

It accepts the reviewed `classification`, `entities`, and optional `context`
from the analysis step, then returns `task_type`, `actions`, and `warnings`.

```json
{
  "schema_version": "2.0",
  "task_type": "schedule",
  "actions": [
    {
      "type": "schedule.create",
      "payload": {
        "title": "meeting",
        "date": "2026-05-17",
        "time": "15:00",
        "duration_minutes": null,
        "participants": [],
        "project_name": null
      },
      "confidence": 0.99,
      "requires_confirmation": false
    }
  ],
  "warnings": []
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
- Backend must validate both engine responses before using them.
- New intents require tests, docs, routing behavior, and API impact review.
