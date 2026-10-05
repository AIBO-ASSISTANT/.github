# Cognitive Entity Extraction & Constraint Resolution

This document specifies the entity models, constraint schemas, and temporal normalization rules implemented in `AIBO-ENGINE-V1.0`.

---

## 1. Entity Architecture & Provenance Tracking

Every entity extracted during natural language understanding carries a typed classification, canonical value, and provenance attribute:

```
┌──────────────────────────────────────────────────────────────┐
│ Entity                                                       │
├──────────────────────────────────────────────────────────────┤
│ - type: EntityType (TASK, PROJECT, TIME_EXPRESSION, etc.)    │
│ - value: str (Normalized entity value)                       │
│ - raw_text: str (Original substring from user utterance)     │
│ - provenance: Provenance (EXPLICIT vs INFERRED)              │
│ - confidence: float (0.0 to 1.0)                             │
└──────────────────────────────────────────────────────────────┘
```

- **`EXPLICIT`**: Directly stated by the user (e.g., "prepare quarterly report").
- **`INFERRED`**: Deduced from context or defaults (e.g., defaulting priority to `medium` when unspecified).

---

## 2. Canonical Entity Types (`EntityType`)

| Entity Type | Description | Extraction Example |
| :--- | :--- | :--- |
| `TASK` | Name or title of an actionable task | "prepare quarterly presentation" |
| `PROJECT` | Name of a project board or umbrella initiative | "Website Redesign 2026" |
| `COLUMN` | Specific Kanban column title | "In Progress", "Code Review" |
| `SCHEDULE` | Calendar event or appointment title | "Quarterly Financial Review" |
| `PERSON` | Assignee, collaborator, or participant | "Alice Smith", "team lead" |
| `TIME_EXPRESSION` | Natural language time or relative date | "tomorrow at 3 PM", "next Friday" |
| `DURATION` | Length of time allocated for an activity | "45 minutes", "2 hours" |
| `PRIORITY` | Urgency classification | `low`, `medium`, `high`, `urgent` |
| `PREFERENCE_KEY` | Cognitive preference attribute | "focus_hours", "theme" |
| `PREFERENCE_VALUE` | Cognitive preference setting | "morning", "dark_mode" |

---

## 3. Constraint Resolution (`ConstraintType`)

When user requests contain operational boundaries, they are extracted as typed constraints:

| Constraint Type | Description | Canonical Representation |
| :--- | :--- | :--- |
| `DEADLINE` | Hard completion timestamp or date limit | ISO-8601 string: `2026-10-02T17:00:00` |
| `TIME_WINDOW` | Permitted time block for scheduling | Tuple `(start_time, end_time)` e.g. `09:00` to `12:00` |
| `DEPENDENCY` | Prerequisite task or action dependency | Task title reference: `taskTitleRef` |
| `ALLOCATION_LIMIT`| Maximum time or resource allocation | Numeric minutes: `60` |

---

## 4. Deterministic Temporal Resolution (`date_resolver.py`)

To eliminate LLM hallucinations and calendar drift across timezones:
1. **Anchor Date**: The client passes `reference_date` (e.g. `2026-10-01`).
2. **Deterministic Computation**: `src/cognitive/understanding/date_resolver.py` resolves:
   - "today" -> `2026-10-01`
   - "tomorrow" -> `2026-10-02`
   - "in 2 days" -> `2026-10-03`
   - "this Friday" -> Nearest future Friday from `reference_date`
3. **Time Formatting**: Canonical 24-hour `HH:MM` format is produced for API mutations alongside friendly 12-hour AM/PM formatting for user confirmation dialogue.
