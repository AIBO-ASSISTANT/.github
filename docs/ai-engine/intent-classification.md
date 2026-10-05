# Cognitive Intent Classification & Natural Language Understanding

This document specifies the intent taxonomy, entity resolution, and contract schemas implemented in `AIBO-ENGINE-V1.0`.

---

## 1. Natural Language Understanding Pipeline

The understanding service processes user utterances into structured, validated Pydantic objects:

```
User Message + Context + Reference Date
            ↓
LLM Gateway (Structured Generation: Gemini / GPT-4o-mini / Ollama)
            ↓
Raw JSON Payload
            ↓
Pydantic Validation (UnderstandingResult.model_validate)
            ↓
Deterministic Date & Time Normalization (date_resolver.py)
            ↓
Action Building & Planning Pipeline
```

---

## 2. Canonical Intent Taxonomy (`UnderstandingIntent`)

The intent taxonomy is strictly governed and mapped across 8 core domains:

| Category | Intent Identifier | Description / Trigger Example |
| :--- | :--- | :--- |
| **Task Management** | `create_task`<br/>`update_task`<br/>`delete_task` | "Add a task to prepare report"<br/>"Rename task report to draft"<br/>"Delete the report task" |
| **Project Management** | `create_project`<br/>`update_project`<br/>`delete_project` | "Create a new project Website Redesign"<br/>"Archive project Alpha"<br/>"Delete project Beta" |
| **Project Columns** | `create_column`<br/>`update_column`<br/>`delete_column` | "Add an In Review column to project"<br/>"Change column title"<br/>"Remove Done column" |
| **Task Lifecycle** | `move_task`<br/>`assign_task` | "Move task to In Progress"<br/>"Assign prepare report to Alice" |
| **Calendar Scheduling**| `create_schedule`<br/>`reschedule`<br/>`delete_schedule`<br/>`complete_schedule` | "Book a sync meeting tomorrow at 10 AM"<br/>"Move meeting to 2 PM"<br/>"Cancel my 3 PM appointment"<br/>"Mark sync meeting as done" |
| **Goal Planning** | `plan_project`<br/>`create_study_plan` | "Plan a 4-week study plan for exams"<br/>"Create a project roadmap with tasks" |
| **Cognitive Memory** | `remember_preference` | "Remember that my focus hours are in the morning" |
| **Retrieval & Dialogue**| `retrieve_information`<br/>`general_conversation`<br/>`needs_clarification` | "What are my high priority tasks?"<br/>"Hello, how can you help me?"<br/>"Schedule something later" (ambiguous) |

---

## 3. Canonical Orchestration Contract (`POST /orchestrate`)

The engine is called by `AIBO-BACKEND` via the canonical endpoint:

### Request Schema:
```json
{
  "user_message": "Schedule a design sync tomorrow at 10 AM",
  "conversation_id": "conv-user-123",
  "project_id": null,
  "reference_date": "2026-10-01",
  "conversation_history": [
    { "role": "user", "content": "Hi AIBO" },
    { "role": "assistant", "content": "Hello! How can I help you today?" }
  ],
  "confirmation": null
}
```

### Response Schema:
```json
{
  "status": "awaiting_confirmation",
  "response_text": "I have prepared to schedule 'design sync' on your calendar for tomorrow, Oct 02 at 10:00 AM. Would you like me to proceed?",
  "proposed_actions": [
    {
      "action_type": "schedule.create",
      "parameters": {
        "title": "design sync",
        "date": "2026-10-02",
        "time": "10:00"
      },
      "risk_level": "medium"
    }
  ],
  "pending_confirmation": {
    "confirmation_id": "hmac_sha256_token_string",
    "state_record_id": "state_uuid",
    "risk_level": "medium",
    "summary": "Schedule 'design sync' for tomorrow, Oct 02 at 10:00 AM"
  },
  "correlation_id": "req-uuid-456",
  "latency_ms": 245.2
}
```

---

## 4. Ambiguity & Clarification Invariants

- If essential information is missing (e.g. "schedule a meeting" without time or date), the intent is classified as `needs_clarification`.
- The engine generates targeted `clarification_questions` rather than making risky assumptions.
- Confidence scores range from `0.0` to `1.0`. Confidence serves solely as an understanding signal and **never** bypasses authorization gates.
