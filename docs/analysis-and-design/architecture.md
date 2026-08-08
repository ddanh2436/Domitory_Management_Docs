# Software Architecture — Dormify (Dormitory Management System)

## Document map

The architecture is documented across four files. This file holds the tech stack; each C4 level has its own file so the diagrams can be reviewed and updated independently.

| PA4 section | Document | Contents |
| --- | --- | --- |
| B | **This file, §1** | Tech stack — every technology used, and why |
| B | [`c4-level1.md`](./c4-level1.md) | **C4 Level 1** — Dormify as one box: its users and the external systems it depends on |
| C | [`c4-level2.md`](./c4-level2.md) | **C4 Level 2** — the four containers, their technologies and the protocols between them |
| C | [`c4-level3.md`](./c4-level3.md) | **C4 Level 3** — components inside the frontend and backend containers, plus a zoom on the AI assistant |
| D | [`deployment-diagram.md`](./deployment-diagram.md) | Deployment — containers mapped onto infrastructure nodes *(to be written)* |

Related: [`system_plan.md`](./system_plan.md) documents the database collections and the full API/route surface.

---

## 1. Tech Stack

<!-- Performed by: <member>; Reviewed by: <member>; Edited by: <member> -->

Dormify is a **three-tier web application**: a Next.js single-page-style frontend, a NestJS REST + WebSocket API server, and a managed MongoDB Atlas cluster. Both tiers are written in TypeScript and run on Node.js, so the team shares one language, one package manager, and one set of linting rules across the whole stack.

### 1.1. Stack at a Glance

| Layer | Technology | Version | Role in the system |
| --- | --- | --- | --- |
| Frontend | Next.js (App Router) + React | 16.2.9 / 19.2.7 | All user interfaces for Student, Admin and Maintenance Staff |
| Frontend styling | Tailwind CSS (via `@tailwindcss/postcss`) | 4.3.0 | Utility-first styling, no separate CSS framework |
| Backend | NestJS on Node.js (Express platform) | 11.1.24 / Node 22 LTS | REST API `/api/*`, business logic, authorization, scheduled jobs |
| Database | MongoDB Atlas + Mongoose ODM | 9.6.3 (ODM) | Persistent storage through 15 registered Mongoose schemas: 13 business-data schemas plus chatbot knowledge and answer-feedback schemas |
| Realtime | Socket.IO (server + client) | 4.8.3 | Push notifications to a specific user or broadcast |
| AI streaming | Server-Sent Events (native HTTP) | — | Token-by-token streaming of chatbot answers |
| Language / runtime | TypeScript on Node.js | 5.9.3 / v22.21.0 | Shared language for frontend and backend |
| Auth | JSON Web Token (`@nestjs/jwt`) + bcrypt | 11.0.2 / 6.0.0 | Stateless bearer-token authentication, password hashing |
| Federated login | Google Identity Services + `google-auth-library` | 0.13.5 / 10.7.0 | "Sign in with Google" ID-token verification |
| File storage | Cloudinary | 2.10.0 | Hosting of maintenance-request photos |
| Email | SMTP via Nodemailer | 9.0.3 | Password-reset emails |
| LLM runtime | Ollama (self-hosted) | `qwen2.5:3b`, `nomic-embed-text` | Chat generation and embeddings for the RAG chatbot |
| Testing | Jest + ts-jest, Supertest | 30.4.2 | Unit specs and end-to-end API tests |
| Tooling | ESLint 9, Prettier 3, Spec Kit, Git, Jira | — | Code quality, spec-driven workflow, version control, task tracking |

### 1.2. Frontend

**Framework — Next.js 16.2.9 (App Router) with React 19.2.7 and TypeScript.**
The UI lives in the frontend repository's `app/` directory, organised into route groups per actor: `(auth)` for login and password recovery, `student/*`, `admin/*` (Admin and Manager) and `staff/*` (Maintenance Staff). Each area has its own layout that wraps its pages in a shared guard component. Pages are mostly React Client Components because nearly every screen is data-driven and interactive.

Key frontend pieces:

* **`app/utils/apiClient.ts`** — the single HTTP client for the backend. It reads `NEXT_PUBLIC_API_URL`, appends `/api`, and attaches the JWT bearer token from `localStorage` to every request. It uses the browser's native `fetch`; no Axios or data-fetching library is used.
* **`proxy.ts`** (Next.js 16's replacement for `middleware.ts`) — server-side route protection. It reads the JWT from the `token` cookie, redirects anonymous visitors to `/login`, and keeps each role inside its own area. It only *decodes* the token (the signing secret lives on the backend), so it is a UX layer; real authorization is always enforced by the NestJS guards.
* **`app/utils/auth.ts`** — `persistToken()` / `clearToken()` keep `localStorage` (used by `apiClient` and the Socket.IO clients) and the `token` cookie (used by `proxy.ts`) in sync.
* **Realtime clients** — `NotificationBell`, `StudentLayout`, `student/page.tsx` and `staff/page.tsx` each open their own `socket.io-client` connection to port 3001 (stripping a trailing `/api` from `NEXT_PUBLIC_API_URL`) and listen for `newNotification`. `app/context/SocketContext.tsx` offers a shared-connection provider but is not yet wired up.

**Styling — Tailwind CSS v4** configured through the PostCSS plugin (`postcss.config.mjs`); there is no `tailwind.config.js`, as v4 is configured from CSS in `app/globals.css`.

**Supporting libraries:**

| Library | Version | Used for |
| --- | --- | --- |
| `socket.io-client` | 4.8.3 | Receiving `newNotification` events over WebSocket |
| `recharts` | 3.8.1 | Charts on the admin dashboard (occupancy, revenue) |
| `lucide-react`, `react-icons` | 1.21.0 / 5.6.0 | Icon sets |
| `@react-oauth/google` | 0.13.5 | Google sign-in button on the login page |

PDF export (contracts, invoices) is implemented in `app/utils/exportPdf.ts` **without any PDF library** — it renders styled HTML into a pop-up window and uses the browser's print dialog, which guarantees correct Vietnamese font rendering.

### 1.3. Backend

**Framework — NestJS 11 on the Express platform**, bootstrapped in the backend repository's `src/main.ts` and listening on **port 3001**. Bootstrap configures CORS, a global `ValidationPipe` (`whitelist` + `transform`), and a 10 MB body limit so Base64 maintenance photos fit in a request.

The code is organised as **one NestJS module per domain**, each with its own controller, service, DTOs and Mongoose schemas:

`auth`, `users`, `rooms`, `bookings`, `assignments`, `contracts`, `checkouts`, `transfers`, `absences`, `invoices`, `maintenance`, `violations`, `feedback`, `notifications`, `audit-logs`, `chatbot` — 16 modules in total.

Cross-cutting backend technologies:

* **Authentication — `@nestjs/jwt` 11.0.2.** `JwtAuthGuard` verifies the bearer token manually, reloads the user from MongoDB to check `accessStatus` (locked accounts are rejected even with a valid token), and attaches `role`/`accessStatus` to the request. Authorization is a separate `RolesGuard` driven by the `@Roles(...)` decorator. Passwords are hashed with **bcrypt** (salt rounds = 10).
* **Validation — `class-validator` 0.15.1 + `class-transformer` 0.5.1**, applied globally through the `ValidationPipe`, so every DTO is validated and type-coerced before it reaches a service.
* **Realtime — `@nestjs/websockets` + `@nestjs/platform-socket.io` 11.1.26.** `NotificationsGateway` authenticates each socket handshake with the JWT, joins the client to a private `user_<id>` room, and exposes `sendToUser` / `sendToAll` to the other modules.
* **Streaming — Server-Sent Events.** `ChatbotController` sets `Content-Type: text/event-stream` and streams typed events (`status`, `text`, `sources`, `invoice`, `notfound`) so chatbot answers appear token by token instead of after a long wait.
* **Scheduled jobs — `@nestjs/schedule` 6.1.3.** A daily 08:00 cron in `contracts.service.ts` handles contract expiry warnings; a per-minute cron in `invoices.service.ts` updates overdue invoice states.
* **Auditing — `AuditLogInterceptor`** (`audit-logs/`) records write operations into the `auditlogs` collection.
* **Email — Nodemailer 9.0.3** in `auth/mail.service.ts`, sending password-reset links through an SMTP account configured by `SMTP_HOST` / `SMTP_PORT` / `SMTP_USER` / `SMTP_PASS`.

### 1.4. Database

**MongoDB Atlas** (cloud-hosted, connected with a `mongodb+srv://` URI) accessed through **Mongoose 9.6.3** wired in by `@nestjs/mongoose`. Schemas are declared with decorators and colocated with their module under `schemas/`.

Why MongoDB fits this project: dormitory documents (contracts, invoices with variable fee lines, maintenance requests with photo arrays, chat knowledge chunks with embedding vectors) have heterogeneous, nested shapes that map naturally onto documents, and the Atlas free tier already provides the replica set, backups and vector search we need.

Database features actually relied upon:

* **Multi-document transactions** (`session.startTransaction()`) in `bookings`, `checkouts` and `transfers`, so room occupancy and the related records can never drift apart under concurrent requests. This requires the replica-set deployment that Atlas provides.
* **Atlas Vector Search** (`$vectorSearch` aggregation stage) over the `knowledge` collection, used for semantic retrieval in the chatbot.
* **Text index / `$text` search** over the same collection, used as a keyword-based retrieval branch that is merged with the vector results.

### 1.5. External Services and Integrations

| Service | Purpose | How it is called |
| --- | --- | --- |
| **MongoDB Atlas** | Primary datastore, vector search | Mongoose driver over TLS (`mongodb+srv`) |
| **Cloudinary** | Storage and CDN delivery of maintenance-request photos | `cloudinary` SDK 2.10.0 from `maintenance.service.ts`; uploads go to the folder named in `CLOUDINARY_MAINTENANCE_FOLDER` |
| **SMTP mail server** | Password-reset emails | Nodemailer over SMTP (TLS) |
| **Google Identity Services** | "Sign in with Google" | Frontend obtains an ID token via `@react-oauth/google`; the backend verifies it with `OAuth2Client.verifyIdToken()` before issuing its own JWT |
| **Ollama** | Local LLM runtime for the chatbot | HTTP calls to `OLLAMA_URL` (default `http://localhost:11434`) for both chat completion and embeddings |

### 1.6. AI / Chatbot Stack

The student-facing chatbot is a **retrieval-augmented generation (RAG)** pipeline built entirely on self-hosted models — no paid AI API is used, which keeps student data inside the team's own infrastructure:

1. **Embedding** — the question is embedded with `nomic-embed-text` through Ollama.
2. **Retrieval** — a hybrid search over the `knowledge` collection: Atlas `$vectorSearch` (similarity threshold 0.82) merged with a Mongo `$text` keyword branch (score threshold 1.6, 2 reserved slots out of 8 context chunks). Both thresholds were calibrated experimentally with `scripts/calibrate-keyword.ts`.
3. **Personal context** — for questions about "my invoice / my contract", the service additionally loads the signed-in student's own records and returns structured invoice cards so the UI can render a real table instead of asking a 3B model to draw one.
4. **Generation** — `qwen2.5:3b` produces the answer, streamed back to the browser over SSE.

The knowledge base is a set of Markdown documents under the backend repository's `src/chatbot/docs/` directory (dormitory rules, parking rules, electricity/water regulations, plus one file per functional area), ingested into MongoDB by `scripts/run-ingest.ts`.

`@google/generative-ai` remains declared in `package.json`, but it is not installed in the current dependency tree and no backend source imports it. It is therefore not part of the implemented runtime architecture; the chatbot uses Ollama exclusively.

### 1.7. Development, Testing and Process Tooling

| Area | Tooling |
| --- | --- |
| Language / build | TypeScript 5.9.3; `nest build` (backend), `next build` (frontend) |
| Package manager | npm 10.9.4 on Node.js v22.21.0 |
| Linting / formatting | ESLint 9 with `typescript-eslint` and `eslint-config-next`; Prettier 3 |
| Unit testing | Jest 30.4.2 + ts-jest; `*.spec.ts` files colocated with the backend source |
| End-to-end testing | Jest with `test/jest-e2e.json` + Supertest, driven by `scripts/e2e-seed.js`, `e2e-run.js` and `e2e-cleanup.js` |
| Spec-driven workflow | **Spec Kit** — artifacts under `docs/requirements/` (`constitution.md`, `spec.md`, and one folder per feature in `specs/` containing `spec.md`, `plan.md`, `tasks.md`, `data-model.md`, `research.md`, `quickstart.md`, `contracts/`) |
| Version control | Git / GitHub, three repositories: docs umbrella, `Domitory_Management_Backend`, `Domitory_Management_Frontend` |
| Task tracking | Jira (sprint board, task assignment) |

### 1.8. Runtime Configuration

Secrets are never committed; both applications read them from environment files.

**Backend — `Domitory_Management_Backend/.env`**

| Variable | Purpose |
| --- | --- |
| `MONGO_URI` | MongoDB Atlas connection string |
| `JWT_SECRET` | Signing key for access tokens |
| `FRONTEND_URL` | Base URL used to build password-reset links |
| `SMTP_HOST`, `SMTP_PORT`, `SMTP_USER`, `SMTP_PASS` | Outgoing mail account |
| `CLOUDINARY_CLOUD_NAME`, `CLOUDINARY_API_KEY`, `CLOUDINARY_API_SECRET`, `CLOUDINARY_MAINTENANCE_FOLDER` | Cloudinary credentials and target folder |
| `OLLAMA_URL`, `CHAT_MODEL`, `EMBED_MODEL` | Ollama endpoint and model names |
| `CHATBOT_SCORE_THRESHOLD`, `CHATBOT_SEARCH_LIMIT`, `CHATBOT_KEYWORD_MIN_SCORE`, `CHATBOT_KEYWORD_SLOTS`, `CHATBOT_HISTORY_TURNS` | Tunable RAG retrieval parameters |

**Frontend — `Domitory_Management_Frontend/.env.local`**

| Variable | Purpose |
| --- | --- |
| `NEXT_PUBLIC_API_URL` | Backend base URL (default `http://localhost:3001/api`); the Socket.IO clients strip the trailing `/api` before connecting |

### 1.9. Communication Protocols Between Tiers

| From | To | Protocol | Notes |
| --- | --- | --- | --- |
| Browser | Next.js server | HTTP/HTTPS | Page rendering and `proxy.ts` route protection |
| Browser | NestJS API | HTTP/HTTPS, JSON, `Authorization: Bearer <JWT>` | All CRUD operations under `/api/*` |
| Browser | NestJS gateway | WebSocket (Socket.IO), JWT in the handshake | `newNotification` push events |
| Browser | NestJS chatbot | HTTP + Server-Sent Events | Streamed chat answers |
| NestJS | MongoDB Atlas | MongoDB wire protocol over TLS (`mongodb+srv`) | Mongoose ODM, replica-set transactions |
| NestJS | Cloudinary | HTTPS REST | Photo upload for maintenance requests |
| NestJS | SMTP server | SMTP over TLS | Password-reset emails |
| NestJS | Ollama | HTTP (local network) | Chat completion and embedding requests |
| NestJS | Google | HTTPS | Google ID-token verification |
