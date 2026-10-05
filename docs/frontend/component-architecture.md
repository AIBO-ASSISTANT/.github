# Frontend Component Architecture

`AIBO-FRONTEND` is built with React 19, Vite 8, and TypeScript, structured as a modular, feature-driven single-page application (SPA).

---

## Architecture & Technology Stack

- **Runtime & Build**: React 19.2+, Vite 8.0+, TypeScript 6+
- **Routing**: React Router v7 (`react-router-dom`)
- **State Management**: Zustand 5 store slices (`src/shared/stores/`) with React Context providers for localized scope
- **Data Visualization**: Recharts 3.10+ for analytics and productivity metric charts
- **Icons & Styling**: Lucide React, vanilla CSS design tokens (`scripts/check-design-tokens.mjs` linter)
- **Real-Time Communication**: `socket.io-client` with automatic reconnection
- **Testing**: Vitest 5 (`npm run test:component`) + Node native test runner (`npm run test:unit`) (87 tests total)

---

## Directory Organization

```text
src/
├── app/                  # Application composition & bootstrap
│   ├── config/           # Environment parsing and runtime constants
│   ├── error/            # Global Error Boundary and fallback components
│   ├── layouts/          # Root application layouts (Header, Sidebar, Shell)
│   ├── providers/        # Global providers (Auth, Theme, Toast, Socket)
│   ├── router/           # React Router v7 route definitions and guards
│   └── theme/            # Theme tokens, dark/light mode definitions
├── features/             # Feature domains (vertical slices)
│   ├── account/          # User profile and account preferences
│   ├── auth/             # Login, signup, password reset, session recovery
│   ├── chat/             # AI conversational assistant interface & confirmation dialogs
│   ├── dashboard/        # Central productivity overview & KPI widgets
│   ├── diary/            # Daily reflection and work journal logs
│   ├── integrations/     # External calendar and workspace connections
│   ├── notifications/    # User notification bell, center, and toast triggers
│   ├── projects/         # Kanban board, columns, team member assignments
│   ├── scheduler/        # Calendar grid, time blocks, schedule editor
│   ├── storage/          # File uploads, asset manager, attachments
│   ├── support/          # In-app help, tickets, documentation viewer
│   └── tasks/            # Task lists, filters, status toggles, task details
└── shared/               # Reusable domain-neutral code
    ├── assets/           # Global icons, illustrations, logos
    ├── components/       # UI primitives (Buttons, Modals, Inputs, Cards)
    ├── hooks/            # Generic hooks (useDebounce, useMediaQuery, useClickOutside)
    ├── services/         # Axios API client, token refresh interceptor
    └── stores/           # Global Zustand store slices (auth, UI, theme)
```

---

## Component Architecture Standards

1. **Feature Encapsulation**: A feature's internal components, hooks, and types must not be imported directly by sibling features. Cross-feature data flows route through `src/shared` stores or public feature exports (`src/features/<feature>/index.ts`).
2. **Decoupled API Transport**: Components never perform raw network calls. All HTTP requests are encapsulated in dedicated feature API modules (e.g., `src/features/projects/api/projectApi.ts`) utilizing the centralized `apiClient`.
3. **Explicit Loading & Error Boundaries**: Every async feature view must implement explicit loading states (skeletons or spinners) and localized error boundaries to prevent full-page crashes on network degradation.
4. **Optimistic Updates**: Task and column interactions update local client state immediately, rolling back upon server error and notifying the user via toast notifications.
