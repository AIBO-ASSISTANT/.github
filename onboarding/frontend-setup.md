# Web Frontend Developer Setup (`AIBO-FRONTEND`)

This document provides setup, testing, and operational guidance for the browser user interface: `AIBO-FRONTEND`.

---

## 1. Prerequisites

- **Node.js**: v22.0.0+ (Tested on v22.17.0)
- **npm**: v10.0.0+

---

## 2. Installation & Running

```bash
cd AIBO-FRONTEND

# Install dependencies
npm install

# Copy environment template
cp .env.example .env

# Start Vite development server
npm run dev
```

The frontend client serves on `http://localhost:8080` (or `http://localhost:5173`) and proxies `/api` to the backend.

---

## 3. Core NPM Scripts

| Script | Command | Purpose |
| :--- | :--- | :--- |
| `npm run dev` | `vite` | Starts Vite local development server with HMR. |
| `npm run build` | `vite build` | Compiles and tree-shakes production bundle into `dist/`. |
| `npm test` | Unit + Vitest | Runs all 87 tests (32 unit tests + 55 component tests). |
| `npm run test:unit` | `node --test ...` | Runs fast native Node type-stripped unit tests. |
| `npm run test:component` | `vitest run` | Runs DOM-based React component suites. |
| `npm run type-check` | `tsc --noEmit` | Verifies TypeScript types with zero emission. |
| `npm run lint` | `eslint .` | Runs ESLint rules across JSX and TypeScript code. |

---

## 4. Implemented User Interface Surfaces

- **Dashboard**: Productivity metrics, weekly activity chart (Recharts), urgent task feeds.
- **Scheduler**: Full calendar views (day, week, month), slot booking, conflict warning modals.
- **Project Manager**: Dynamic Kanban boards, custom columns, drag-and-drop task assignment.
- **Diary**: Personal daily notes, mood tracking, reflective historic calendar.
- **Settings Hub**: Tabbed suite covering General, Notifications, Security & Sessions (password change & active sessions), Privacy, Appearance (light/dark mode toggle), and Connected Apps.
- **Support & Help Center**: In-app FAQ, feedback reporting, documentation links.
- **Auth Flow**: Login, signup, password reset, onboarding wizard with in-memory token security.

---

## 5. Security & Session Handling

1. **In-Memory Access Tokens**: Access tokens are kept exclusively in memory. Tab closure completely clears token credentials.
2. **Silent Background Refresh**: When access tokens expire (15 min), the API client automatically triggers `POST /api/v1/auth/refresh` using the secure `HttpOnly` cookie without interrupting the user.
3. **Multi-Tab Synchronization**: Session logout or token revocation is synchronized across all browser tabs via `BroadcastChannel` and storage events.
