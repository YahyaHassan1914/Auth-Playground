# Auth Playground — Learn & Compare Authentication Strategies (MERN)

> An educational application that lets developers log in using different authentication methods and then **see exactly what happened**—tokens, cookies, sessions, profiles—side‑by‑side with a clear visual timeline and plain‑English explanations.

---

## Why this project exists

Authentication is often learned piecemeal—one tutorial for JWT, another for sessions, another for OAuth. That makes it hard to compare approaches or understand how to **scale a codebase** to support multiple methods without turning it into spaghetti.

**Auth Playground** solves this by putting several auth strategies under one roof with a unified data model and a consistent UI. You’ll pick a method, sign in, and then explore:

- The **raw artifacts** produced by that method (e.g., JWT, session cookie, OAuth profile)
- A **timeline** of each step across **frontend and backend**
- An **explanation** of how and why it works, including trade‑offs and when to choose it

This is not a vendor pitch—it’s a neutral, side‑by‑side learning lab.

---

## What you’ll learn

- **Conceptual clarity**: Stateful vs stateless auth, where secrets live, how identity is proven each request
- **Architecture**: How to design a backend that supports multiple auth methods without conflicts
- **Frontend practices**: Handling tokens/cookies, protected routes, error boundaries, i18n, theming
- **Operational thinking**: Extensibility, portability, and guardrails for production hardening

---

## Audience

- Developers who know basic MERN and want to **truly understand** authentication
- Teams evaluating which auth strategy fits their product and constraints
- Educators needing a concrete, inspectable sandbox for teaching auth

---

## Scope & non‑goals

**In scope**

- Clear demonstrations of multiple auth methods in one app
- Visual and textual explanations with raw data inspectors and timelines
- Patterns for scaling to more auth providers cleanly

**Out of scope (for this README)**

- Provider account setup, environment variables, and credential acquisition
- Low‑level, provider‑specific implementation guides

---

## High‑level feature set

1. **Multi‑Auth Login/Registration**

   - Choose an auth method: **JWT**, **Session + Cookies**, **OAuth (Google/GitHub)**, **Firebase**, **Passwordless**, etc... (optional extension)
   - A single form with method selector; the app calls the relevant backend route

2. **Unified Identity & Profile**

   - One **shared user record** in the database regardless of auth method
   - Ability to display and (optionally) link multiple auth methods to the same identity

3. **Data Inspector (Post‑Login)**

   - **Sidebar**: who you are (email, provider, last login), and your chosen method
   - **Main panel**: raw artifacts with safe redactions, e.g.:
     - JWT: header/payload/claims, expiry
     - Session: cookie name/value metadata, server session reference
     - OAuth: provider profile snapshot
     - Firebase: UID and claims

4. **Timeline Visualizer**

   - Step‑by‑step event log across client and server:
     - Frontend request → Backend handler → DB operations → Response → Client storage
   - Designed as a **sequence diagram** you can read if you know nothing else

5. **Explanations & Trade‑offs**

   - Friendly, concise write‑ups per method: when to use, common pitfalls, security notes
   - Comparison matrix (stateful vs stateless, storage, rotation, CSRF risk, DX)

6. **UI/UX Fundamentals**

   - **Theme system** (Light/Dark/Coffee) with CSS variables; instant switching
   - **Internationalization (i18n)** with language picker; all UX strings translatable
   - **Toast notifications** for key auth and navigation events
   - **Error & Suspense boundaries** for resilient loading and failure states

---

## Architecture at 10,000 ft

```
┌──────────────────────────────────────────────────────────────────┐
│                            React (Vite)                          │
│  • Auth chooser  • Forms  • Protected routes  • Timeline viewer  │
│  • Theme/i18n     • Toaster • Data Inspector (raw artifacts)     │
└───────────────▲───────────────────────────────────────┬──────────┘
                │ HTTP (JSON)                           │
                │                                       │
        ┌───────┴───────────────────────────────────────▼───────┐
        │                     Express API Gateway                │
        │  /auth/jwt  /auth/session  /auth/oauth  /auth/firebase │
        │  Cross‑cutting: logging, rate limiting, errors         │
        └───────────────▲──────────────────────────────────┬─────┘
                        │                                  │
               Strategy modules                     Unified models
                        │                                  │
                JWT | Session | OAuth | Firebase     User / Session
                        │                                  │
                        └──────────────┬───────────────────┘
                                       │
                                 MongoDB (Mongoose)
```

**Design notes**

- Each auth method is a **strategy module** behind a stable route surface
- The database stores a **single user identity**, with room to link different auth credentials
- The React app stays method‑agnostic; it reads the chosen method from state and renders the correct inspector and explanation

---

## Core user flows (educational focus)

### 1) Pick a method

- Start on the welcome screen
- Choose **JWT**, **Session**, **OAuth**, or **Firebase**
- The UI explains what’s about to happen and what you’ll get to inspect

### 2) Register / Login

- Fill in the minimal fields (e.g., email + password)
- Submit via the selected route; the backend performs the method‑specific steps but always returns a **normalized session summary**

### 3) Inspect & Learn

- Sidebar: identity summary and method badge
- Main: raw artifacts (with sensitive parts partially redacted) plus the **timeline** and **explanation**
- Copy buttons for tokens/IDs where safe, plus “open in viewer” for decoded payloads

### 4) Compare methods

- Switch methods and log in again (same email) to see how the artifacts differ while the user identity stays the same
- Optional: link multiple methods to the same account and view combined profile

---

## State management philosophy

- **Contexts (stable global settings)**: Theme, Language, and the pre‑login “chosen auth method”
- **Reducer (auth session lifecycle)**: isAuthenticated, user profile, and method‑specific artifact references
- **Zustand/Redux (high‑frequency, cross‑cutting data)**: live API request/response logs, timeline events, and the data inspector’s raw buffers

This split keeps UI responsive and avoids unnecessary re‑renders while making the inspector and timeline feel instantaneous.

---

## Theming & i18n approach

- **Tailwind v4 + CSS variables** for instant theme switching (no rebuild)
- `:root` holds the Light theme; `[data-theme="dark"|"coffee"]` overrides for others
- React **ThemeContext** applies/removes the `data-theme` attribute
- **react‑i18next** (or similar) powers a language picker; every visible string is translatable
- Toasts consume the same CSS variables so they remain theme‑aware automatically

---

## Resilience: errors, loading, and observability

- **Error boundaries**: global wrapper and targeted boundaries around volatile widgets (charts, profile fetchers)
- **Suspense**: colocated with components that fetch or lazy‑load to show focused fallbacks
- **Inspector‑first logging**: network requests and key lifecycle events feed the timeline and can be exported for study

---

## Comparison matrix (conceptual)

| Method             | Stateful? | Client artifact           | Server storage             | Typical risks           | Great for                   |
| ------------------ | --------- | ------------------------- | -------------------------- | ----------------------- | --------------------------- |
| JWT                | No        | Bearer token              | None (stateless)           | Token theft, long TTL   | APIs, mobile, microservices |
| Session + Cookie   | Yes       | HttpOnly cookie           | Session store              | CSRF, session fixation  | Web apps, SSR               |
| OAuth (OIDC)       | Depends   | Provider code → app token | Short‑lived session or JWT | Misconfigured callbacks | Social login, SSO           |
| Firebase (managed) | Managed   | SDK token                 | Provider‑side              | Vendor lock‑in, quotas  | Rapid prototyping           |

> The app provides interactive explanations for these cells rather than just a static table.

---

## UX highlights

- **Inspector layout**: sidebar (identity & method) + main panel (raw data, timeline, explanation)
- **Safe redaction**: secrets are masked by default with one‑click reveal in dev mode
- **Copy & decode**: “Copy token”, “Decode JWT” tooling built into the viewer
- **Accessibility**: keyboard‑navigable controls, semantic headings, contrast‑aware themes

---

## Extensibility playbook (adding a new method)

1. Add a **strategy module** with clear inputs/outputs
2. Extend the **normalized response** contract so the frontend inspector needs no special cases
3. Register a **new explanation** and **timeline template**
4. Add a tile in the chooser; no changes to existing strategies required

This keeps the system open for growth without regressions.

---

## Success criteria

- A beginner can pick any method and explain, in their own words, the **who/what/where** of the login: who is authenticated, what artifacts were created, where they’re stored
- Switching between methods feels **predictable and consistent**
- The codebase demonstrates **clean separations** of concerns and is easy to extend

---

## Roadmap (learning milestones)

- Milestone 1: JWT + Sessions with inspector and timeline
- Milestone 2: OAuth (Google/GitHub) with provider profile viewer
- Milestone 3: Optional Firebase strategy
- Milestone 4: Account linking (one identity, multiple methods)
- Milestone 5: Exportable audit logs and side‑by‑side comparisons
