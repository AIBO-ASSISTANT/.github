# AIBO Assistant Database Schema

This document is the canonical logical database model for AIBO Assistant.
It reflects the normalized MVP schema requested for the product and uses the
exact table and relationship naming from the design brief.

Notes:

- `id` is the primary key for every table.
- `created_at` and `updated_at` are standard audit timestamps unless noted
  otherwise.
- Foreign keys are shown as `FK -> table.id`.
- Polymorphic links are application-enforced, not database-enforced.
- Enum values are listed exactly as expected by the schema.

## `users`

| Column | Type | Constraints | Notes |
| --- | --- | --- | --- |
| `id` | PK | Primary key | User identifier |
| `first_name` | string | Required | User first name |
| `last_name` | string | Required | User last name |
| `email` | string | Required, unique | Login email |
| `password_hash` | string | Required | Hashed password value |
| `avatar_url` | string | Optional | Profile image URL |
| `phone` | string | Optional | Contact phone number |
| `field` | string | Optional | User field / discipline |
| `address` | string | Optional | Mailing or profile address |
| `bio` | string | Optional | Profile biography |
| `timezone` | string | Optional | User preferred IANA time zone |
| `language` | string | Optional | Preferred language code |
| `theme` | string | Optional | Preferred UI theme |
| `email_verified` | boolean | Optional | Whether email has been verified |
| `is_active` | boolean | Optional | Active account flag |
| `last_login_at` | datetime | Optional | Last successful login timestamp |
| `last_activity_at` | datetime | Optional | Last observed activity timestamp |
| `created_at` | datetime | Required | Record creation timestamp |
| `updated_at` | datetime | Required | Record update timestamp |

## `projects`

| Column | Type | Constraints | Notes |
| --- | --- | --- | --- |
| `id` | PK | Primary key | Project identifier |
| `name` | string | Required | Project name |
| `description` | string | Optional | Project description |
| `type` | enum | Required | `PERSONAL` or `TEAM` |
| `status` | enum | Required | `ACTIVE`, `COMPLETED`, or `ARCHIVED` |
| `created_by` | FK -> `users.id` | Required | Project owner / creator |
| `created_at` | datetime | Required | Record creation timestamp |
| `updated_at` | datetime | Required | Record update timestamp |

## `project_members`

| Column | Type | Constraints | Notes |
| --- | --- | --- | --- |
| `id` | PK | Primary key | Membership identifier |
| `project_id` | FK -> `projects.id` | Required | Parent project |
| `user_id` | FK -> `users.id` | Required | Member user |
| `role` | enum | Required | `OWNER`, `ADMIN`, `MEMBER`, or `VIEWER` |
| `joined_at` | datetime | Required | Time the user joined the project |
| `created_at` | datetime | Required | Record creation timestamp |

Unique constraint:

- `UNIQUE(project_id, user_id)`

## `project_columns`

| Column | Type | Constraints | Notes |
| --- | --- | --- | --- |
| `id` | PK | Primary key | Column identifier |
| `project_id` | FK -> `projects.id` | Required | Parent project |
| `name` | string | Required | Column name |
| `position` | integer | Required | Display order within the project |
| `created_at` | datetime | Required | Record creation timestamp |
| `updated_at` | datetime | Required | Record update timestamp |

## `schedules`

| Column | Type | Constraints | Notes |
| --- | --- | --- | --- |
| `id` | PK | Primary key | Schedule identifier |
| `owner_id` | FK -> `users.id` | Required | Schedule owner |
| `title` | string | Required | Schedule title |
| `date` | date | Required | Calendar date |
| `start_time` | datetime | Required | Schedule start timestamp |
| `end_time` | datetime | Required | Schedule end timestamp |
| `status` | enum | Required | `PLANNED`, `ACTIVE`, `COMPLETED`, or `CANCELLED` |
| `created_at` | datetime | Required | Record creation timestamp |
| `updated_at` | datetime | Required | Record update timestamp |

## `tasks`

| Column | Type | Constraints | Notes |
| --- | --- | --- | --- |
| `id` | PK | Primary key | Task identifier |
| `title` | string | Required | Task title |
| `description` | string | Optional | Task description |
| `project_id` | FK -> `projects.id` | Optional | Parent project |
| `schedule_id` | FK -> `schedules.id` | Optional | Parent schedule |
| `column_id` | FK -> `project_columns.id` | Optional | Project board column |
| `priority` | enum | Required | `LOW`, `MEDIUM`, `HIGH`, or `URGENT` |
| `status` | enum | Required | `PENDING`, `IN_PROGRESS`, `COMPLETED`, or `CANCELLED` |
| `position` | integer | Required | Sort order within the owning container |
| `deadline` | datetime | Optional | Due deadline |
| `completed_at` | datetime | Optional | Completion timestamp |
| `created_by` | FK -> `users.id` | Required | Task creator |
| `created_at` | datetime | Required | Record creation timestamp |
| `updated_at` | datetime | Required | Record update timestamp |

Business rule:

- A task must belong to at least one of `project_id` or `schedule_id`.
- A task may belong to both a project and a schedule.
- `column_id` is valid only when the task belongs to the same project.

## `task_assignments`

| Column | Type | Constraints | Notes |
| --- | --- | --- | --- |
| `id` | PK | Primary key | Assignment identifier |
| `task_id` | FK -> `tasks.id` | Required | Assigned task |
| `user_id` | FK -> `users.id` | Required | Assigned user |
| `assigned_by` | FK -> `users.id` | Required | User who created the assignment |
| `assigned_at` | datetime | Required | Assignment timestamp |
| `created_at` | datetime | Required | Record creation timestamp |

Unique constraint:

- `UNIQUE(task_id, user_id)`

## `executions`

| Column | Type | Constraints | Notes |
| --- | --- | --- | --- |
| `id` | PK | Primary key | Execution identifier |
| `task_id` | FK -> `tasks.id` | Required | Target task |
| `action_type` | string | Required | Execution action name |
| `actor_id` | FK -> `users.id` | Required | User or actor that triggered the execution |
| `status` | enum | Required | `RUNNING`, `SUCCESS`, or `FAILED` |
| `payload` | JSON | Optional | Execution input payload |
| `completed_at` | datetime | Optional | Completion timestamp |
| `result` | JSON | Optional | Execution output payload |
| `started_at` | datetime | Optional | Start timestamp |
| `created_at` | datetime | Required | Record creation timestamp |
| `updated_at` | datetime | Required | Record update timestamp |

## `assets`

| Column | Type | Constraints | Notes |
| --- | --- | --- | --- |
| `id` | PK | Primary key | Asset identifier |
| `owner_id` | FK -> `users.id` | Required | Asset owner |
| `uploaded_by` | FK -> `users.id` | Required | User who uploaded the asset |
| `entity_type` | enum | Required | `USER`, `PROJECT`, or `TASK` |
| `entity_id` | string | Required | Polymorphic entity identifier |
| `bucket` | string | Required | Storage bucket |
| `key` | string | Required | Storage object key |
| `file_name` | string | Required | Original file name |
| `file_size` | integer | Required | File size in bytes |
| `content_type` | string | Required | MIME type |
| `visibility` | enum | Required | `PUBLIC` or `PRIVATE` |
| `status` | enum | Required | `ACTIVE` or `DELETED` |
| `created_at` | datetime | Required | Record creation timestamp |
| `updated_at` | datetime | Required | Record update timestamp |

## `activity_logs`

| Column | Type | Constraints | Notes |
| --- | --- | --- | --- |
| `id` | PK | Primary key | Activity identifier |
| `entity_type` | string | Required | Polymorphic entity type |
| `entity_id` | string | Required | Entity identifier |
| `action` | string | Required | Activity action name |
| `actor_id` | FK -> `users.id` | Required | User who performed the action |
| `details` | JSON | Optional | Activity details payload |
| `created_at` | datetime | Required | Record creation timestamp |
| `updated_at` | datetime | Required | Record update timestamp |

## `notifications`

| Column | Type | Constraints | Notes |
| --- | --- | --- | --- |
| `id` | PK | Primary key | Notification identifier |
| `user_id` | FK -> `users.id` | Required | Notification recipient |
| `title` | string | Required | Notification title |
| `message` | string | Required | Notification body |
| `type` | string | Required | Notification category |
| `is_read` | boolean | Required | Read state |
| `created_at` | datetime | Required | Record creation timestamp |
| `updated_at` | datetime | Required | Record update timestamp |

## `user_sessions`

| Column | Type | Constraints | Notes |
| --- | --- | --- | --- |
| `id` | PK | Primary key | Session identifier |
| `user_id` | FK -> `users.id` | Required | Session owner |
| `refresh_token` | string | Required | Refresh token value |
| `device` | string | Optional | Device label or identifier |
| `ip_address` | string | Optional | Last known IP address |
| `expires_at` | datetime | Required | Session expiry |
| `last_used_at` | datetime | Optional | Last usage timestamp |
| `created_at` | datetime | Required | Record creation timestamp |

## Relationship Matrix

| Parent | Child | Cardinality | Notes |
| --- | --- | --- | --- |
| `users` | `projects` | 1 -> N | `created_by` |
| `users` | `schedules` | 1 -> N | `owner_id` |
| `users` | `tasks` | 1 -> N | `created_by` |
| `users` | `project_members` | 1 -> N | `user_id` |
| `projects` | `project_members` | 1 -> N | Membership rows |
| `projects` | `project_columns` | 1 -> N | Ordered columns |
| `projects` | `tasks` | 1 -> N | Optional via `project_id` |
| `project_columns` | `tasks` | 1 -> N | Optional via `column_id` |
| `schedules` | `tasks` | 1 -> N | Optional via `schedule_id` |
| `tasks` | `task_assignments` | 1 -> N | Assignment rows |
| `users` | `task_assignments` | 1 -> N | `user_id` |
| `users` | `task_assignments` | 1 -> N | `assigned_by` |
| `tasks` | `executions` | 1 -> N | Execution history |
| `users` | `executions` | 1 -> N | `actor_id` |
| `users` | `assets` | 1 -> N | `owner_id` and `uploaded_by` |
| `users` | `activity_logs` | 1 -> N | `actor_id` |
| `users` | `notifications` | 1 -> N | Recipient |
| `users` | `user_sessions` | 1 -> N | Session ownership |

## Business Rules

1. A task must belong to at least one of:
   - a project, or
   - a schedule.
2. A task may belong to both a project and a schedule.
3. A project column can exist only within a project.
4. A task can reference a project column only if it belongs to that project.
5. A user can belong to multiple projects.
6. A project can have multiple users.
7. A task can be assigned to multiple users.
8. A user can be assigned to multiple tasks.
9. Assets are polymorphic and can be attached to users, projects, or tasks.
10. Activity logs are polymorphic and can record events for users, projects,
    tasks, and schedules.
11. Each project must have exactly one `OWNER` in `project_members`
    (the creator), while additional members may have `ADMIN`, `MEMBER`, or
    `VIEWER` roles.
12. Enforce uniqueness with:
    - `UNIQUE(project_id, user_id)` on `project_members`
    - `UNIQUE(task_id, user_id)` on `task_assignments`

## Summary

This schema is normalized, supports clear ownership boundaries, and leaves
room for future additions such as comments, labels, recurring tasks,
integrations, and richer permissions without requiring a redesign.
