# Use-Case Specifications — 10. Feedback and Suggestions

<!-- Performed by: <member>; Reviewed by: <member>; Edited by: <member> -->

> Diagram: see `../use-case-model.md` §10. **All three use cases in this group are 🆕 not yet implemented** — this entire functional group is listed in `spec.md`'s Backlog ("Hòm thư góp ý/khiếu nại", proposal §3.8). See `01-authentication-profile.md` for the shared screenshot note (for this group, only Figma/mockup-style prototypes are possible, since none of these screens exist yet).

---

## UC-FBK-01 — Submit Complaint or Feedback 🆕

**Actor(s):** Student

**Description *(proposed)*:** A student submits a complaint or piece of feedback about dormitory operations, facilities, or staff conduct.

**Preconditions *(proposed)*:** Logged in.

**Basic Flow *(proposed)*:**
1. Student opens a new "Góp ý / Khiếu nại" page, selects a category (facility, staff conduct, billing, other), and writes a message.
2. Frontend calls a new endpoint, e.g. `POST /api/feedback`.
3. Backend creates a `Feedback(PENDING)` record and notifies the Dormitory Manager.

**Alternative Flows *(proposed)*:**
- **AF1 — Anonymous submission:** ⚠ **decision needed** — should students be able to submit without their identity being shown to the manager, given this may be a complaint about staff?

**Postconditions *(proposed)*:** A `Feedback` record exists, awaiting manager response (UC-FBK-03).

**Special Requirements:**
- 🆕 **Not yet implemented.** The system currently only supports one-way communication (managers → students, via UC-NOT-03 announcements); there is no channel for a student to initiate a message to management. **Decision needed:** confirm this is in scope for the current sprint before building it.

---

## UC-FBK-02 — Submit Suggestion 🆕

**Actor(s):** Student

**Description *(proposed)*:** A student submits a suggestion for improving dormitory life (distinct from a complaint — proactive rather than reactive).

**Preconditions *(proposed)*:** Logged in.

**Basic Flow *(proposed)*:**
1. Student opens the same or an adjacent form as UC-FBK-01, selecting "Suggestion" instead of "Complaint/Feedback".
2. Same endpoint/record type as UC-FBK-01, differentiated by a `type` field.

**Alternative Flows *(proposed)*:** None distinct from UC-FBK-01.

**Postconditions *(proposed)*:** A `Feedback(type=SUGGESTION)` record exists.

**Special Requirements:**
- 🆕 **Not yet implemented.** **Decision needed:** should suggestions and complaints share one schema/endpoint (differentiated by a type field, as assumed above) or be entirely separate collections/flows? The diagram treats them as two use cases but does not specify.

---

## UC-FBK-03 — Review and Respond to Feedback 🆕

**Actor(s):** Dormitory Manager

**Description *(proposed)*:** A manager reviews submitted complaints/suggestions and responds to the student.

**Preconditions *(proposed)*:** At least one `Feedback` record exists (UC-FBK-01/02).

**Basic Flow *(proposed)*:**
1. Manager opens a "Góp ý & Khiếu nại" admin page listing all feedback, filterable by type/status.
2. Manager writes a response and marks the item resolved.
3. Frontend calls a new endpoint, e.g. `PATCH /api/feedback/:id/respond`.
4. Backend saves the response and notifies the student (reusing the existing notification infrastructure).

**Alternative Flows *(proposed)*:**
- **AF1 — Manager closes an item without a written response** (e.g., duplicate/spam): allowed, with a distinct "closed, no response" status.

**Postconditions *(proposed)*:** `Feedback.status = RESOLVED` (or `CLOSED`) with an optional response message; student notified.

**Special Requirements:**
- 🆕 **Not yet implemented — entire group.** **Team decision needed before building:** what is the minimum viable version for this submission — a simple one-way "student writes, manager reads and marks resolved" flow (as sketched above), or a full back-and-forth comment thread? Given the PA3 timeline, the team should decide whether this whole group is worth implementing now or deferring, since the rest of the notification infrastructure (Socket.IO, `NotificationsService`) can be reused directly once the `Feedback` schema/endpoints exist.
