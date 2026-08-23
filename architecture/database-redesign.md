# AIBO Assistant MongoDB Redesign

This document explains the design rationale behind the production-ready MongoDB model for the AIBO Assistant MVP.

For the exact field-by-field schema, use [database-schema.md](database-schema.md).

The goal is to remove duplicated collections, avoid circular references, and keep the schema simple enough for an MVP while still leaving room to grow.

## 1. Architecture Review

The current backend has three separate problems:

1. Task modeling is duplicated.
   - There is a general `tasks` collection.
   - There is also a `project_tasks` collection that repeats the same business concept with a different storage shape.
   - This creates synchronization risk and makes it unclear which collection is the source of truth.

2. Scheduling is too tightly coupled.
   - The current design mixes task and schedule ownership in both directions.
   - That creates circular relationships and makes future refactors harder.
   - The redesign uses one direction only: schedules may reference tasks, but tasks do not reference schedules.

3. Enterprise infrastructure arrived too early.
   - `executions`, `event_outbox`, `idempotency_records`, `project_write_fences`, and `schedule_write_fences` are valuable in a mature platform, but they add operational cost before the MVP has proven it needs them.
   - These should not be part of the MVP database.

The recommended redesign keeps the platform clean by using a small number of primary domain collections and pushing advanced reliability infrastructure to a later phase.

## 2. Collections to Remove

| Collection | Decision | Reason |
| --- | --- | --- |
| `project_tasks` | Remove | It duplicates the task domain and should be merged into `tasks`. |
| `executions` | Remove from MVP | This is execution-engine infrastructure, not core productivity data. |
| `event_outbox` | Remove from MVP | Useful later for reliable async messaging, but premature for MVP. |
| `idempotency_records` | Remove from MVP | Important only once external write reliability becomes a proven problem. |
| `project_write_fence` | Remove from MVP | Concurrency fencing is infrastructure, not product data. |
| `schedule_write_fence` | Remove from MVP | Same reason as project write fence. |
| `project_columns` | Keep as separate collection | Kanban columns remain first-class ordered records with board-specific metadata. |

## 3. Collections to Merge

| Source | Target | Result |
| --- | --- | --- |
| `project_tasks` | `tasks` | One task model for both personal and project work. Add project board fields directly to `tasks`. |
| `project_columns` | `project_columns` | Keep project board columns as their own collection. |

## 4. Collections to Keep

| Collection | Why keep it |
| --- | --- |
| `users` | Core identity and profile entity. |
| `projects` | Core workspace container. |
| `project_members` | Needed for multi-user access control and role management. |
| `tasks` | Single source of truth for all tasks. |
| `schedules` | Needed for planning, calendar blocks, and AI scheduling. |
| `task_assignments` | Needed because tasks can be assigned to multiple users and assignment metadata matters. |
| `notifications` | Core user-facing communication channel. |
| `assets` | Needed for avatars, uploads, and future attachments. |
| `activity_logs` | Valuable audit trail and history feed. |
| `user_sessions` | Required for secure auth/session management. |
| `integrations` | Keep as future optional infrastructure, not MVP-critical. |

## 5. Redesigned Database Schema

### `users`

Purpose: identity, auth, and user preferences.

Fields:
- `_id`
- `first_name`
- `last_name`
- `email`
- `password_hash`
- `avatar_url`
- `phone`
- `field`
- `address`
- `bio`
- `timezone`
- `language`
- `theme`
- `role`
- `email_verified`
- `is_active`
- `last_login_at`
- `last_activity_at`
- `created_at`
- `updated_at`

Indexes:
- `email` unique
- `role`
- `is_active`
- `email_verified`
- `first_name + last_name + email` for lightweight directory lookup

References:
- Parent of most other collections via `user_id`-style ownership fields.

### `projects`

Purpose: workspace container for team or personal project work.

Fields:
- `_id`
- `owner_id`
- `name`
- `description`
- `type`
- `status`
- `columns[]`
- `created_at`
- `updated_at`

Embedded `columns[]` subdocument shape:
- `_id`
- `name`
- `position`
- `color`
- `is_done`
- `created_at`
- `updated_at`

Indexes:
- `owner_id`
- `status`
- `type`
- `owner_id + updated_at`

Unique constraints:
- Optional application-level uniqueness on `(owner_id, name)` if duplicate project names become a UX issue.

References:
- `owner_id -> users._id`
- `columns[]` are embedded and not a separate collection.

### `project_members`

Purpose: many-to-many project access control.

Fields:
- `_id`
- `project_id`
- `user_id`
- `role`
- `joined_at`
- `created_at`
- `updated_at`

Indexes:
- `project_id`
- `user_id`
- `project_id + role`

Unique constraints:
- `project_id + user_id`

References:
- `project_id -> projects._id`
- `user_id -> users._id`

### `tasks`

Purpose: the single source of truth for all task-like work.

Fields:
- `_id`
- `owner_id`
- `created_by`
- `project_id`
- `project_task_id`
- `column_id`
- `schedule_id`
- `title`
- `description`
- `type`
- `status`
- `priority`
- `position`
- `order`
- `due_at`
- `deadline`
- `completed_at`
- `source`
- `is_deleted`
- `deleted_at`
- `purge_after`
- `metadata`
- `created_at`
- `updated_at`

Recommended enum values:
- `type`: `personal`, `project`, `ai`
- `status`: `todo`, `doing`, `done`, `cancelled`
- `priority`: `low`, `medium`, `high`, `urgent`
- `source`: `manual`, `ai`, `imported`

Indexes:
- `owner_id + is_deleted + status + updated_at`
- `owner_id + is_deleted + due_at`
- `project_id + column_id + position`
- `project_id + status + updated_at`
- `owner_id + type + created_at`

Unique constraints:
- None required at the database level.

References:
- `owner_id -> users._id`
- `project_id -> projects._id`
- `column_id` is an embedded project column ID, not a separate FK.
- `project_task_id` is the public project-board identifier used by task assignments.

### `schedules`

Purpose: time blocks and schedule events for tasks or ad-hoc planning.

Fields:
- `_id`
- `user_id`
- `task_id`
- `start_at`
- `end_at`
- `timezone`
- `local_date`
- `status`
- `source`
- `updated_by`
- `completed_at`
- `is_deleted`
- `deleted_at`
- `notes`
- `created_at`
- `updated_at`

Recommended enum values:
- `status`: `scheduled`, `completed`, `skipped`, `cancelled`
- `source`: `manual`, `ai`
- `updated_by`: `user`, `ai`

Indexes:
- `user_id + local_date + status + is_deleted`
- `task_id + start_at`
- `user_id + start_at + end_at`
- `user_id + is_deleted + start_at`

Unique constraints:
- Optional partial unique on `user_id + start_at + end_at` where `is_deleted = false` if duplicate blocks are a problem.

References:
- `user_id -> users._id`
- `task_id -> tasks._id` optionally, so schedules can exist with or without a linked task.

### `task_assignments`

Purpose: assignment metadata for multi-assignee tasks.

Fields:
- `_id`
- `task_id`
- `user_id`
- `assigned_by`
- `assigned_at`
- `created_at`
- `updated_at`

Indexes:
- `task_id`
- `user_id`
- `assigned_by`
- `user_id + assigned_at`

Unique constraints:
- `task_id + user_id`

References:
- `task_id -> tasks._id`
- `user_id -> users._id`
- `assigned_by -> users._id`

### `notifications`

Purpose: user-facing alerts and system messages.

Fields:
- `_id`
- `user_id`
- `type`
- `title`
- `message`
- `data`
- `is_read`
- `read_at`
- `created_at`
- `updated_at`

Indexes:
- `user_id + is_read + created_at`
- `user_id + created_at`
- `type + created_at`

Unique constraints:
- None required.

References:
- `user_id -> users._id`

### `assets`

Purpose: file and object metadata for user uploads and future attachments.

Fields:
- `_id`
- `owner_id`
- `uploaded_by`
- `entity_type`
- `entity_id`
- `storage_provider`
- `bucket`
- `key`
- `file_name`
- `mime_type`
- `size`
- `checksum`
- `visibility`
- `status`
- `metadata`
- `deleted_at`
- `created_at`
- `updated_at`

Recommended enum values:
- `entity_type`: `user`, `project`, `task`
- `visibility`: `private`, `public`
- `status`: `active`, `deleted`

Indexes:
- `owner_id + status + created_at`
- `entity_type + entity_id`
- `bucket + key`

Unique constraints:
- `bucket + key`

References:
- `owner_id -> users._id`
- `uploaded_by -> users._id`
- `entity_type/entity_id` are polymorphic and enforced in service logic.

### `activity_logs`

Purpose: audit trail and human-readable history.

Fields:
- `_id`
- `actor_id`
- `entity_type`
- `entity_id`
- `action`
- `metadata`
- `created_at`
- `updated_at`

Indexes:
- `actor_id + created_at`
- `entity_type + entity_id + created_at`
- `action + created_at`

Unique constraints:
- None required.

References:
- `actor_id -> users._id`
- Other references are polymorphic.

### `user_sessions`

Purpose: auth session storage and refresh-token rotation.

Fields:
- `_id`
- `session_id`
- `user_id`
- `refresh_token_hash`
- `device`
- `ip_address`
- `user_agent`
- `expires_at`
- `last_used_at`
- `revoked_at`
- `revoked_reason`
- `created_at`
- `updated_at`

Indexes:
- `session_id` unique
- `user_id + revoked_at + expires_at`
- TTL index on `expires_at`

Unique constraints:
- `session_id`

References:
- `user_id -> users._id`

### `integrations`  [future optional]

Purpose: external sync and provider configuration.

Fields:
- `_id`
- `user_id`
- `provider`
- `account_id`
- `display_name`
- `status`
- `oauth`
- `settings`
- `external_mappings`
- `sync_state`
- `conflict_policy`
- `last_webhook_at`
- `last_synced_at`
- `last_error`
- `created_at`
- `updated_at`

Indexes:
- `user_id + provider + account_id` unique
- `user_id + status + updated_at`

References:
- `user_id -> users._id`

This should be treated as future feature work unless calendar sync is required for the MVP launch.

## 6. Final Relationship Diagram

```text
users
  ├──< projects.owner_id
  ├──< project_members.user_id
  ├──< tasks.owner_id
  ├──< schedules.user_id
  ├──< task_assignments.user_id
  ├──< task_assignments.assigned_by
  ├──< notifications.user_id
  ├──< user_sessions.user_id
  ├──< assets.owner_id
  ├──< assets.uploaded_by
  └──< activity_logs.actor_id

projects
  ├── embeds columns[]
  ├──< project_members.project_id
  └──< tasks.project_id

tasks
  ├──< task_assignments.task_id
  ├──< schedules.task_id (optional)
  └──< assets.entity_type = task

schedules
  └── belongs to users, may optionally point to a task
```

## 7. Final Business Rules

1. Every user must have a unique email address.
2. A project must have exactly one owner.
3. A user may belong to many projects.
4. A project may have many members.
5. A project may contain many embedded columns.
6. A task must have exactly one owner.
7. A task may belong to a project, a schedule, both, or neither depending on its source type.
8. A task may optionally belong to one project column when it belongs to a project.
9. A task may be assigned to multiple users.
10. A user may be assigned to many tasks.
11. A schedule may optionally be linked to a task.
12. A schedule must always belong to a user.
13. A schedule must not overlap if the business rule says one active block per time slot.
14. Assets are polymorphic and must be validated in service logic.
15. Activity logs are append-only.
16. Sessions must expire automatically.
17. Soft delete should be used for user-facing content that may need recovery.
18. Enterprise reliability collections should not exist in the MVP.

## 8. Index Strategy

Recommended indexes:

- `users.email` unique for login and identity.
- `users.is_active`, `users.email_verified` for account state and filtering.
- `projects.owner_id`, `projects.status`, `projects.updated_at` for workspace lists.
- `project_members.project_id + user_id` unique for membership integrity.
- `tasks.owner_id + is_deleted + status + updated_at` for user inbox and filtered list views.
- `tasks.project_id + column_id + position` for board rendering.
- `tasks.owner_id + due_at` for agenda and due-date views.
- `schedules.user_id + local_date + status + is_deleted` for calendar queries.
- `schedules.task_id + start_at` for task-linked schedule history.
- `task_assignments.task_id + user_id` unique for assignment integrity.
- `notifications.user_id + is_read + created_at` for inbox-style notification feeds.
- `assets.bucket + key` unique for storage integrity.
- `assets.owner_id + status + created_at` for asset lists.
- `activity_logs.entity_type + entity_id + created_at` for history lookups.
- `user_sessions.session_id` unique and `expires_at` TTL for auth lifecycle.

## 9. MongoDB Best Practices

- Use embedding for bounded child data like project columns.
- Use referencing for multi-owner entities like tasks, assignments, schedules, and memberships.
- Keep one canonical collection per concept.
- Avoid circular references.
- Use transactions only when creating or updating multiple related collections together.
- Use soft delete for tasks, schedules, assets, and other recoverable user content.
- Use TTL indexes for sessions and any temporary token storage.
- Prefer compound indexes over text indexes for the MVP.
- Use cursor pagination for large task, activity, and notification feeds.
- Keep polymorphic references limited and validate them in service logic.

## 10. Future Expansion

These should stay out of the MVP but remain easy to add later:

- comments
- labels
- recurring task templates
- AI conversations
- AI memory
- integrations
- calendar sync
- analytics
- audit history retention policies
- real-time collaboration

## 11. Final Database

The final MVP database should be:

- `users`
- `projects`
- `project_columns`
- `project_members`
- `tasks`
- `schedules`
- `task_assignments`
- `notifications`
- `assets`
- `activity_logs`
- `user_sessions`

Everything else should be treated as future infrastructure or merged into the collections above.

This is the cleanest, most maintainable MongoDB model for the MVP.
