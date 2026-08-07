# C4 Model — Level 2: Container Diagram (Dormify)

> Reviewed against the codebase on 2026-08-07. Sources: `system_plan.md`, `use-case-model.md`, `app.module.ts`, `proxy.ts`, `chatbot.service.ts`.

This zooms one level into the **Dormify** software system from the Level 1 System Context view. It shows every container — a separately runnable/deployable unit — the technology it is actually built with, and the protocol each connection uses in the running application.

## Diagram

```mermaid
C4Container
  title Container diagram for Dormify (Dormitory Management System)

  Person(student, "Student", "Resident: books rooms, pays invoices, files requests, chats with the AI assistant")
  Person(manager, "Dormitory / Floor Manager", "Runs approvals, contracts, finance, residence & conduct (Floor Manager folded in for this diagram)")
  Person(admin, "System Admin", "Owns accounts, roles/permissions, audit log")
  Person(staff, "Maintenance Staff", "Works assigned repair jobs")

  System_Boundary(dormify, "Dormify — Dormitory Management System") {
    Container(webapp, "Web Application", "Next.js 16, React 19, Tailwind CSS 4", "Role-scoped UI for all four roles; proxy.ts edge middleware gates routes by JWT role (UX only)")
    Container(api, "API & Realtime Server", "NestJS 11, Node.js, Socket.IO", "REST /api/* (14 domain modules) + WebSocket notification gateway, one process on port 3001. JwtAuthGuard + RolesGuard + global AuditLogInterceptor + cron jobs")
    ContainerDb(db, "Application Database", "MongoDB Atlas (Mongoose)", "13 domain collections + knowledge collection (RAG embeddings/text index)")
    Container(llm, "Local LLM Runtime", "Ollama — qwen2.5:3b, nomic-embed-text", "Self-hosted model server behind the chatbot module: embeddings + streamed chat completion")
  }

  System_Ext(cloudinary, "Cloudinary", "Image CDN — stores maintenance photos")
  System_Ext(email, "Email Service", "SMTP (Gmail) — password-reset OTP mail")
  System_Ext(google, "Google OAuth 2.0", "Verifies Google ID tokens")
  System_Ext(payment, "Payment Gateway", "Documented in the use-case model; NOT integrated — invoices use POST /invoices/pay-mock today")

  Rel(student, webapp, "Uses", "HTTPS")
  Rel(manager, webapp, "Uses", "HTTPS")
  Rel(admin, webapp, "Uses", "HTTPS")
  Rel(staff, webapp, "Uses", "HTTPS")

  Rel(webapp, api, "Calls", "REST/JSON, Bearer JWT")
  BiRel(webapp, api, "Receives notifications / connects", "WebSocket, Socket.IO (JWT handshake)")

  Rel(api, db, "Reads/writes", "Mongoose, MongoDB wire protocol")
  Rel(api, llm, "Requests embeddings & chat completions", "HTTP")
  Rel(api, cloudinary, "Uploads photos", "HTTPS, multipart")
  Rel(api, email, "Sends OTP mail", "SMTP")
  Rel(api, google, "Verifies ID token", "HTTPS")
  Rel(api, payment, "Planned — not yet called", "n/a")

  UpdateRelStyle(api, payment, $offsetY="-10")
```

> Note: `System_Ext(payment, ...)` and its `Rel` are included for completeness against the use-case model's documented actor, but represent a **planned, unimplemented** integration — see the modeling notes below. If your Mermaid renderer doesn't support the `C4Container` diagram type (GitHub's does; older renderers may not), the same information is in the tables below.

## People

| Actor | Who they are | Reaches the system via |
| --- | --- | --- |
| **Student** | Dormitory resident: browses/books rooms, pays invoices, files repair/transfer/checkout requests, talks to the AI assistant. | Web Application, browser |
| **Dormitory / Floor Manager** | Runs day-to-day operations: approvals, contracts, finance, residence & conduct oversight. Floor Manager is folded into this actor for diagramming — see note below. | Web Application, browser |
| **System Admin** | Owns accounts, roles/permissions, and the audit log. | Web Application, browser |
| **Maintenance Staff** | Works assigned repair jobs and updates their status. | Web Application, browser |

**Modeling note — Floor Manager.** This diagram folds the Floor Manager into the Dormitory Manager actor for readability, matching `use-case-model.md` §1. The backend keeps `FLOOR_MANAGER` as a distinct RBAC role in `users.schema.ts`, guarding several endpoints directly — this is a presentation simplification only, not a code change.

## Containers

| Container | Technology | Responsibility | Talks to |
| --- | --- | --- | --- |
| **Web Application** | Next.js 16 (App Router), React 19, TypeScript, Tailwind CSS 4 | Serves the role-scoped UI (`/admin`, `/student`, `/staff`, `/(auth)`); edge middleware (`proxy.ts`) reads the JWT cookie to gate routes by role, as a UX convenience only — not a security boundary. | API & Realtime Server |
| **API & Realtime Server** | NestJS 11, Node.js, Socket.IO | One process, port 3001. 14 domain modules behind `/api/*`, guarded by `JwtAuthGuard` + `RolesGuard` and a global `AuditLogInterceptor`; a Socket.IO gateway pushes realtime notifications; `@nestjs/schedule` runs the daily cron jobs (contract-expiry reminders, overdue-invoice marking). | Application Database, Local LLM Runtime, Cloudinary, Email Service, Google OAuth |
| **Application Database** | MongoDB Atlas via Mongoose | System of record for users, rooms, bookings, contracts, invoices, maintenance, transfers, checkouts, absences, violations, notifications, and the audit log — plus the chatbot's `knowledge` collection (embedding vectors + text index for RAG). | — |
| **Local LLM Runtime** | Ollama | Self-hosted model server backing the AI assistant module: `nomic-embed-text` embeds knowledge chunks and questions for the RAG similarity search; `qwen2.5:3b` generates the streamed chat answer. | — |

### Two things that look like containers but aren't

- **Realtime Gateway.** Notifications go over Socket.IO, but the gateway is a `@WebSocketGateway` class registered inside the same Nest app as the REST controllers (same `app.module.ts`, same `main.ts` bootstrap, same port 3001). It's a *component* of the API & Realtime Server container, not a second deployable — the diagram draws one box with two protocols, not two boxes.
- **Route proxy.** `proxy.ts` (Next 16's middleware) decodes the JWT to redirect by role but never verifies the signature — the secret only lives on the API server. It's a component inside the Web Application container; the real authorization boundary is the guard chain on every backend call.

## External systems

| System | Purpose | Called by | Protocol |
| --- | --- | --- | --- |
| **Cloudinary** | Hosts maintenance-request photos. | API & Realtime Server | HTTPS, multipart upload |
| **Email Service** | Delivers password-reset OTP mail (Gmail account, via Nodemailer). | API & Realtime Server | SMTP |
| **Google OAuth 2.0** | Verifies the Google ID token from the frontend's `@react-oauth/google` sign-in button (`google-auth-library` `OAuth2Client` server-side). | API & Realtime Server | HTTPS |
| **Payment Gateway** | Documented in the use-case model as an actor (bank / VNPay / MoMo / ZaloPay). **Not integrated** — `POST /invoices/pay-mock` simulates a paid invoice, and the wallet logos in the UI (`Momo.png`, `ZaloPay.png`, `ViettelPay.jpg`) are static images. | — (not integrated) | — |

## Modeling decisions worth stating

1. **The database is a container, not an external system.** Even though MongoDB Atlas is someone else's infrastructure, Dormify owns every collection's schema and every query against it — the same convention used for a managed Postgres/RDS instance.
2. **Ollama is drawn inside the boundary.** Unlike Cloudinary or Google, nobody else operates it — it's a process this team runs specifically to serve the chatbot feature, so it's modeled as an internal container even though it runs as a separate host/process from the API server.
3. **One API process, two protocols.** There is no separate gateway or BFF: REST, the WebSocket notification push, JWT auth, and the cron scheduler all live in the single NestJS deployable.
4. **Auth is enforced twice, trusted once.** The frontend's `proxy.ts` gates routes for UX; the only real trust boundary is the backend guard chain (`JwtAuthGuard` + `RolesGuard`) on every request — consistent with `system_plan.md`'s note on route protection.
5. **The diagram shows what's built, not the vision doc.** Payment Gateway appears as a dashed, external system with no live `Rel` because `system_plan.md` and the code agree it's simulated — drawing an aspirational integration as a solid line would mislead the next engineer who reads this diagram.
