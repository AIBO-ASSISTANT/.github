# Frontend UI Consistency & Design Standards

## Design System & Token Governance

`AIBO-FRONTEND` enforces strict visual consistency across all feature modules through unified design tokens, dark/light themes, and an automated token linter (`npm run lint:design`).

---

## Core UI Principles

1. **Token Discipline**: All colors, margins, radiuses, shadows, and typographic scales must reference design tokens defined in `src/styles/` or CSS custom properties. Ad-hoc hex codes and inline styling are blocked by `scripts/check-design-tokens.mjs`.
2. **Predictable Layout Structure**: Feature screens render inside standardized application shells (`AppLayout`, `Header`, `Sidebar`, `ToastContainer`), maintaining consistent padding and navigation affordances.
3. **Designed Transient States**: Every dynamic interface element must support four distinct visual states:
   - **Default**: Clean, interactive component rendering.
   - **Loading**: Pulse skeletons or subtle spinner indicators matching the element's layout geometry.
   - **Empty**: Contextual empty-state illustrations with actionable creation buttons.
   - **Error**: Non-catastrophic inline notices with retry triggers.
4. **Form Ergonomics**: Form validation errors render directly beneath the offending input field with clear remediation guidance, rather than opaque global alerts.

---

## Implemented Product Workflows (V1.0 RC)

| Feature Module | Active Implementation Scope | Key Components |
| --- | --- | --- |
| **Auth** | Full login, signup, session recovery, and multi-tab logout synchronization. | `LoginForm`, `SignupForm`, `SessionGuard` |
| **Dashboard** | Productivity KPIs, weekly activity charts, upcoming schedule agenda, and quick-action bars. | `ProductivityChart` (Recharts), `AgendaSummary`, `QuickTaskModal` |
| **Tasks** | Filterable list, status toggles (Todo, In Progress, Done), priority indicators, task edit modal. | `TaskList`, `TaskCard`, `TaskFilterBar` |
| **Projects** | Kanban board layout, column drag-and-drop, task creation per column, member avatars. | `KanbanBoard`, `ProjectColumn`, `ColumnHeader` |
| **Scheduler** | Interactive calendar grid, day/week view switches, scheduled task blocks, conflict warnings. | `CalendarGrid`, `TimeSlotBlock`, `ScheduleModal` |
| **AI Assistant** | Real-time chat drawer, intent feedback, streaming message bubbles, interactive HMAC confirmation dialogs. | `AIChatDrawer`, `MessageList`, `ConfirmationModal` |
