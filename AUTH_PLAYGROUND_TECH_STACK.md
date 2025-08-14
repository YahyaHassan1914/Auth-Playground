# Auth Playground — Tools, Libraries & Third‑Party Services

> A reference list of the tools, libraries, and services used in **Auth Playground**, organized by frontend, backend, and infrastructure — with notes on the current status of each.

---

## **Frontend Stack**

### Core
- **React (Vite)** — SPA framework and build tool; current version: latest stable.
- **Tailwind CSS v4** — Utility‑first styling with CSS variables for theming.
- **React Router** — Client‑side routing for protected/public routes.

### State Management
- **React Context API** — Global settings: theme, language, chosen auth method.
- **Reducer** — Manages authentication session lifecycle.
- **Zustand** — Manages live logs and timeline events.

### UI/UX Enhancements
- **Lucide React** — Icon library.
- **React‑Toastify** — Toast notifications (theme‑aware).
- **Error Boundaries** — Custom + global fallback UI.
- **Suspense** — Data and lazy loading states.

### Internationalization
- **react‑i18next** — Language picker and translations.

---

## **Backend Stack**

### Core
- **Node.js (Express)** — API gateway and routing.
- **MongoDB (Mongoose)** — Unified user and session storage.

### Authentication Strategies
- **JWT (jsonwebtoken)** — Stateless token authentication.
- **Session + Cookies (express‑session)** — Stateful authentication.
- **OAuth 2.0 / OIDC (Passport.js)** — Google and GitHub providers.
- **Firebase Admin SDK** — Managed authentication (optional).

### Middleware & Utilities
- **CORS** — Cross‑origin requests handling.
- **Helmet** — HTTP header security.
- **morgan/winston** — Logging.

---

## **Third‑Party Services**

- **Google Cloud** — OAuth credentials for Google login.
- **GitHub Developer** — OAuth credentials for GitHub login.
- **Firebase** — Authentication and identity management (optional).
- **Postman** — API testing.

---

## **Current Project Status**
- **Milestone 1 (In Progress)** — JWT + Sessions implemented with inspector and timeline.
- OAuth strategy (Google/GitHub) partially configured, pending final UI integration.
- Firebase strategy planned, not yet implemented.
- UI theming system operational; i18n structure in place with base translations.
- Initial error boundaries and toasts integrated; timeline visualizer in active development.

