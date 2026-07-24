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

## 3. `analysis-and-design/use-case-model.md` (Use-Case Model)

* **v1 (initial):** 7 Mermaid use-case diagrams (Authentication, System Administration, Booking & Allocation, Contracts & Checkout, Finance, Maintenance, Residency Rules), 40 use cases (UC-01–UC-40), 5 actors, FR01–FR30 traceability table.
* **v2 (team revision by Trần Huỳnh Mạnh Đạt, reviewed/edited by Đào Duy Anh):** expanded to 11 functional groups covering the full vision document (72 use cases: UC-AUTH/PRO/STAY, UC-ADM, UC-ROOM/STU, UC-CON, UC-CHK, UC-FIN, UC-RES, UC-MNT, UC-FBK, UC-COND, UC-NOT). Added external-system actors (Google OAuth Provider, Email/SMS Service, Payment Gateway, Scheduled Trigger) and consolidated the Floor Manager role into the Dormitory Manager actor for diagram clarity. Added a Student-feature and Maintenance-Staff-feature traceability table (§14–15) and a Floor Manager function-reassignment table (§16).
* **Consistency fixes applied after review:** added a modeling note (§1) clarifying that folding Floor Manager into Dormitory Manager is a **diagram-level simplification only** — the backend still enforces `FLOOR_MANAGER` as a real, distinct RBAC role in several endpoint guards, and no code change removed it. Reworded every place that asserted the role had been "removed" (§4, §7, §8, §10, §16 heading) to instead say "folded into this diagram's Dormitory Manager actor," to avoid contradicting `system_plan.md`. Corrected the FR19 traceability row, which read "Removed from scope" in a way that contradicted the already-implemented asset-inspection feature (FR19 ✅ in `spec.md`) — reworded to "Covered — merged into checkout review/compensation, not a standalone use case."

## 4. `analysis-and-design/use-case-specs/` (Use-Case Specifications, PA3 Section D)

* **v1 (initial):** one file per group (7 files), 40 use cases (UC-01–UC-40), matching the model's v1 numbering.
* **v2 (full rewrite to match the model's v2 revision):** all 7 v1 files deleted and replaced with 11 files (one per functional group, 72 use cases, UC-AUTH-01…UC-NOT-03) plus a rewritten index. Each use case is tagged ✅ (implemented, verified against code) / 🔶 (partially implemented) / 🆕 (not yet implemented — proposed design only): 54 ✅, 10 🔶, 8 🆕 of 72.
* Re-verified implementation details directly against the backend/frontend for every group, uncovering several **new discrepancies** between the v2 diagram and the actual code not caught in the v1 pass — most notably: `UC-CON-02`/`UC-CON-03` (contract extend/terminate) are attributed to Dormitory Manager in the diagram but are actually **student self-service** endpoints with no role guard; `UC-FIN-06` (revenue report) is diagrammed for Dormitory Manager but backend-restricted to Admin only; `UC-FIN-05`'s "Scheduled Trigger" link has no corresponding cron job. All flagged inline (⚠) and summarized in a themed list (A: diagram/code mismatches, B: described-more-than-built gaps, C: fully unbuilt use cases, D: prototype screenshots) in `use-case-specs/README.md`.
* The UI-prototype requirement (screenshots per use case) remains a team action item — see the note in `use-case-specs/01-authentication-profile.md`.

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
