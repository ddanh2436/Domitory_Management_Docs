# Changes.md — PA3 Submission

<!-- Performed by: <member>; Reviewed by: <member>; Edited by: <member> -->

This file lists all changes made to the project documents compared to the previous PA submission, as required by the PA3 guidelines. One section per revised document.

## 1. `requirements/spec.md` (Functional Requirements Specification)

* **Synchronized all FR statuses with the actual source code** (as of 2026-07-18). Previously only 8 FRs were marked done; the implementation is now far ahead of the checklist:
  * Marked ✅: FR01–FR04, FR06, FR10, FR12–FR21, FR23, FR25, FR29, FR30 (newly verified against code).
  * FR05 downgraded to 🔶 (partial): 5 fixed roles + per-account access control exist; dynamic role creation does not.
  * FR08 (backup/restore) remains the only unimplemented FR.
* **Added implementation notes** to each FR (route, page, behavior) so the spec doubles as a verification checklist.
* **Added actor #4** (Maintenance Staff) to the overview — the `/staff` area is now a first-class part of the product.
* **Added a Backlog section** (section 4) listing remaining work: FR08, dynamic RBAC, gender input UI, real payment gateway, visitor registration, feedback hub, AI features.
* Documented newly added schema fields: `User.gender`, `Room.genderType` (used by auto-assignment).

## 2. `analysis-and-design/system_plan.md` (System Plan)

* **Complete rewrite** — the old version described an early draft (6 collections, ~15 endpoints) that no longer matched the code. The new version documents:
  * A high-level architecture diagram (Mermaid) covering the Next.js frontend, NestJS backend, Socket.IO gateway, global audit interceptor, MongoDB, Cloudinary, and SMTP.
  * All 12+ Mongoose collections actually in use, including the new `checkouts` and `auditlogs`.
  * The full backend module/API surface (14 modules) with role restrictions.
  * The frontend route map for all four areas (auth, student, admin, staff).
  * Key design decisions: concurrency-safe occupancy updates, transactions, deposit convention, fire-and-forget logging/notifications.
* Language switched to **English** per PA3 submission guidelines.

## 3. `analysis-and-design/use-case-model.md` (Use-Case Model) — **new file**

* Created the PA3 Section C deliverable: 7 Mermaid use-case diagrams (Authentication, System Administration, Booking & Allocation, Contracts & Checkout, Finance, Maintenance, Residency Rules) with actors, use cases, and `«include»`/`«extend»` relationships.
* Added an actor catalog (5 actors incl. the cron scheduler) and a full FR ↔ UC traceability table covering FR01–FR30.

## 4. `analysis-and-design/use-case-specs/` (Use-Case Specifications) — **new folder, PA3 Section D**

* Created the full Section D deliverable: one specification per use case in `use-case-model.md` (40 use cases, UC-01–UC-40), split into 7 files matching the diagram groups, each with name/ID, actor(s), description, preconditions, basic flow, alternative flows, postconditions, and special requirements.
* Alternative flows were derived from actually reading the corresponding NestJS controllers/services (not assumed), so they reflect real validation rules, role guards, and edge cases (e.g., race-condition handling on room occupancy, the one-time-only maintenance rating, the floor-at-zero conduct score).
* Flagged 14 open decision points directly in the specs (marked ⚠) where the code's actual behavior is ambiguous, inconsistent with `spec.md`'s wording, or simply unimplemented (e.g., FR08, dynamic RBAC, the missing gender-input UI, the `sandbox-reset-password` endpoint) — collected in `use-case-specs/README.md` for the team to resolve.
* The UI-prototype requirement (screenshots per use case) is intentionally left as a team action item, since it requires either running the live app or building a separate design-tool prototype — see the note in `use-case-specs/01-authentication-profile.md`.

## 5. Implementation changes since the previous submission (context for the documents above)

New features implemented end-to-end (backend module + frontend pages + realtime notifications):

| Feature | FRs | Key artifacts |
| --- | --- | --- |
| Room checkout workflow (request → asset inspection → compensation → deposit refund → contract termination) | FR18–FR21 | `backend/src/checkouts/*`, `/student/checkout`, `/admin/checkouts` |
| Automatic room assignment (bulk, gender-aware) | FR12 | `backend/src/assignments/*`, `/admin/auto-assign` |
| System audit log (global interceptor + admin viewer) | FR06 | `backend/src/audit-logs/*`, `/admin/audit-logs` |
| Debt tracking & reminders by room | FR23 | `invoices` debt endpoints, `/admin/debts` |
| Staff-area upgrade (filters, search, average rating, realtime refresh) | FR27–FR28 | `/staff` |
| UX fixes: scrollable admin sidebar (logout button always visible), avatar dropdown with logout, checkout events in the student activity log | — | `admin/layout.tsx`, `student/page.tsx` |
