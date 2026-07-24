# Use-Case Specifications — 9. Maintenance Management

<!-- Performed by: <member>; Reviewed by: <member>; Edited by: <member> -->

> Diagram: see `../use-case-model.md` §9. Legend: ✅ implemented · 🆕 not yet implemented. See `01-authentication-profile.md` for the shared screenshot note.

---

## UC-MNT-01 — Submit Repair Request ✅

**Actor(s):** Student

**Description:** A student reports a facility issue, optionally with a photo and priority.

**Preconditions:** The student has a room assigned.

**Basic Flow:**
1. Student opens `/student/maintenance`, enters title, description, priority (`LOW`/`MEDIUM`/`HIGH`/`URGENT`), optionally attaches a photo.
2. `POST /api/maintenance` (multipart) uploads the photo to Cloudinary (if configured) and creates a `Maintenance(PENDING)` request.
3. Every `ADMIN` account is notified in realtime.

**Alternative Flows:**
- **AF1 — Student has no room:** rejected.
- **AF2 — Non-image file attached:** rejected before upload is attempted.
- **AF3 — Cloudinary not configured:** if a photo was provided, the request fails with a server error; ⚠ **verify** whether a text-only submission (no photo) should still succeed in that case.

**Postconditions:** A `Maintenance(PENDING)` request exists.

**Special Requirements:** New-request notifications currently reach `ADMIN` only, not `DORMITORY_MANAGER`/`FLOOR_MANAGER` — ⚠ **verify this is intentional**, since the assignment step (UC-MNT-03) is available to both `ADMIN` and `DORMITORY_MANAGER`.

---

## UC-MNT-02 — Track Repair Request ✅

**Actor(s):** Student

**Description:** A student views the status and outcome of their own repair requests.

**Preconditions:** Logged in.

**Basic Flow:**
1. Student opens `/student/maintenance`.
2. `GET /api/maintenance/me` returns the student's requests, sorted by status weight then newest first.

**Alternative Flows:** None.

**Postconditions:** None — read-only. If a request is `RESOLVED` and unrated, the page also offers the rating action (UC-COND-adjacent — see UC-MNT-07's rating alternative flow).

**Special Requirements:** None.

---

## UC-MNT-03 — Review and Assign Maintenance Request ✅

**Actor(s):** Dormitory Manager

**Description:** A manager reviews incoming requests and assigns one to a maintenance staff member.

**Preconditions:** A `Maintenance(PENDING)` request exists; at least one active `MAINTENANCE_STAFF` account exists.

**Basic Flow:**
1. Manager opens `/admin/maintenance`, selects a staff member from `GET /api/users/maintenance-staff`.
2. `PATCH /api/maintenance/:id/assign` with `{ staffId }` validates the target is `MAINTENANCE_STAFF`, sets `Maintenance.assignedTo`, and notifies the staff member.

**Alternative Flows:**
- **AF1 — Target is not a maintenance-staff account:** rejected.
- **AF2 — Reassigning an already-assigned request to a different staff member:** allowed (overwrites `assignedTo`); ⚠ **verify** the previously assigned staff member is properly notified of the reassignment.

**Postconditions:** `Maintenance.assignedTo` set/changed.

**Special Requirements:** None beyond role restriction.

---

## UC-MNT-04 — Track Maintenance Progress ✅

**Actor(s):** Dormitory Manager

**Description:** A manager monitors all maintenance requests across pending, in-progress, and completed states.

**Preconditions:** Actor holds `ADMIN` or `DORMITORY_MANAGER`.

**Basic Flow:**
1. Manager opens `/admin/maintenance`.
2. `GET /api/maintenance` returns every request (sorted by status weight, then newest), each showing assignee, priority, and current status.

**Alternative Flows:** None.

**Postconditions:** None — read-only. `GET /api/maintenance/stats/status` additionally provides aggregate counts per status for dashboard charts.

**Special Requirements:** None.

---

## UC-MNT-05 — View Assigned Maintenance Jobs ✅

**Actor(s):** Maintenance Staff

**Description:** A staff member sees every job assigned to them, filterable by status and searchable by keyword, with realtime updates.

**Preconditions:** Logged in as `MAINTENANCE_STAFF`.

**Basic Flow:**
1. Staff opens `/staff`.
2. `GET /api/maintenance/assigned/me` returns their assignments, split into "Cần xử lý" (pending/in-progress) and "Đã xong" (resolved/rejected — see also UC-MNT-09).
3. Staff narrows the list via status-filter tabs or a keyword search (title/description/room).
4. A `MAINTENANCE`-type realtime notification (e.g., a new assignment) triggers an automatic list refresh.

**Alternative Flows:**
- **AF1 — No jobs assigned yet:** empty state.
- **AF2 — Filter/search yields no matches:** distinct empty state.

**Postconditions:** None — read-only.

**Special Requirements:** None.

---

## UC-MNT-06 — View Maintenance Job Details *(included by UC-MNT-05)* ✅

**Actor(s):** Maintenance Staff

**Description:** The staff member reviews a specific job's issue description, room location, priority, and attached photo.

**Preconditions:** The job is assigned to the staff member (UC-MNT-05).

**Basic Flow:**
1. Within the same `/staff` list (not a separate detail page), each job card already displays the full detail inline: room, title, description, priority badge, reporting student's name/phone, submission time, and a link to the attached photo (if any).

**Alternative Flows:**
- **AF1 — No photo attached:** the photo link is omitted.

**Postconditions:** None — read-only.

**Special Requirements:** ⚠ Unlike the diagram's `«include»` relationship might suggest a separate detail screen, the current implementation shows full details **inline in the list card**, not on a distinct route/modal. This satisfies the use case's intent but is worth noting for prototype screenshots (there is no separate "detail" screen to capture).

---

## UC-MNT-07 — Update Repair Status and Result ✅

**Actor(s):** Maintenance Staff (own assignments only); Dormitory Manager (any request)

**Description:** The assigned staff (or a manager) moves a request through Pending → In Progress → Completed, and the student then rates the result.

**Preconditions:** The request is assigned to the acting staff member, or the actor is a manager.

**Basic Flow:**
1. Staff clicks "Tiếp nhận sửa chữa" (→ In Progress) or "Hoàn thành" (→ Resolved), confirming in a dialog.
2. `PATCH /api/maintenance/:id/status` validates the new status and, if the actor is staff, that `assignedTo` matches them.
3. On first transition to `RESOLVED`, `resolvedAt` is recorded and the student is notified to rate the repair.

**Alternative Flows:**
- **AF1 — Staff updates a request assigned to someone else:** rejected (403).
- **AF2 — Invalid status value:** rejected.
- **AF3 — Marking `RESOLVED` a request already `RESOLVED`:** the update succeeds but `resolvedAt` is not overwritten and the rating notification is not re-sent.
- **AF4 — Manager/staff marks a request `REJECTED`** (e.g., duplicate/invalid report): allowed; no rating notification sent.
- **AF5 (student-side, downstream) — Student rates the completed repair 1–5 stars:** `PATCH /api/maintenance/:id/rate`; only the request's own student, only once, only when `RESOLVED`.

**Postconditions:** `Maintenance.status` updated; `resolvedAt` set on first resolution; `rating`/`ratedAt` set once the student rates it.

**Special Requirements:** Ratings are immutable once submitted — no "edit my rating" flow exists.

---

## UC-MNT-08 — Upload Before and After Repair Photos 🆕

**Actor(s):** Maintenance Staff

**Description *(proposed)*:** When updating a repair's status, staff attach photo evidence of the room's condition before and after the fix.

**Preconditions *(proposed)*:** A job is assigned to the staff member.

**Basic Flow *(proposed)*:**
1. When transitioning a job to "In Progress", staff optionally uploads a "before" photo.
2. When marking "Resolved", staff optionally (or mandatorily, per dormitory policy) uploads an "after" photo.
3. Both photos are stored (e.g., via the same Cloudinary integration already used for the student's initial report photo) and shown alongside the request in both the staff and student views.

**Alternative Flows *(proposed)*:**
- **AF1 — No photo provided when required by policy:** the status transition is blocked until one is attached.

**Postconditions *(proposed)*:** `Maintenance` document gains `beforePhotoUrl`/`afterPhotoUrl` fields.

**Special Requirements:**
- 🆕 **Not yet implemented.** Today, `Maintenance.imageUrl` is set only **once**, by the student at creation time (UC-MNT-01) — there is no field or upload path for staff to attach their own before/after evidence during status updates. **Decision needed:** is this required for the current sprint, and should "after" photos be mandatory before a job can be marked Resolved?

---

## UC-MNT-09 — View Maintenance History ✅

**Actor(s):** Maintenance Staff

**Description:** A staff member reviews previously completed (or rejected) maintenance work.

**Preconditions:** Logged in as `MAINTENANCE_STAFF`.

**Basic Flow:**
1. On `/staff` (the same page as UC-MNT-05), the "Đã xong" section lists every job with `status ∈ {RESOLVED, REJECTED}` assigned to the staff member, including the student's star rating if given.

**Alternative Flows:**
- **AF1 — No completed jobs yet:** the "Đã xong" section is omitted/empty.

**Postconditions:** None — read-only.

**Special Requirements:** Not a separate route — it is a section of the same dashboard as UC-MNT-05/UC-MNT-10, not a distinct "history" page.

---

## UC-MNT-10 — View Maintenance Dashboard ✅

**Actor(s):** Maintenance Staff

**Description:** A staff member sees their workload and job-status statistics at a glance.

**Preconditions:** Logged in as `MAINTENANCE_STAFF`.

**Basic Flow:**
1. `/staff` shows summary tiles: total assigned, pending count, in-progress count, resolved count, and average rating (computed client-side from rated, resolved jobs).

**Alternative Flows:**
- **AF1 — No ratings yet:** the average-rating tile shows a placeholder (`—`) instead of a number.

**Postconditions:** None — read-only.

**Special Requirements:** These tiles are the top section of the same `/staff` page as UC-MNT-05 and UC-MNT-09 — all three use cases share one screen, not three separate ones.
