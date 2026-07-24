# Use-Case Specifications — 8. Residence Management

<!-- Performed by: <member>; Reviewed by: <member>; Edited by: <member> -->

> Diagram: see `../use-case-model.md` §8. Legend: ✅ implemented · 🔶 partially implemented · 🆕 not yet implemented. See `01-authentication-profile.md` for the shared screenshot note.
>
> Code reality check: the backend currently models exactly **two** declaration types — `TAM_TRU` (an overnight guest staying in the student's room) and `TAM_VANG` (the student themself away overnight) — both through the same `Absence` schema and endpoints. The diagram's four separate student use cases (UC-RES-01–04) map onto these two types as noted below.

---

## UC-RES-01 — Submit Overnight Absence Declaration ✅

**Actor(s):** Student

**Description:** A student declares that they will be away from the dormitory overnight.

**Preconditions:** The student has a room assigned and no other declaration is currently `PENDING`.

**Basic Flow:**
1. Student opens `/student/absences`, selects "Tạm vắng" (`AbsenceType.TAM_VANG`), enters a start date, end date, and reason.
2. `POST /api/absences` validates the type, reason, and date range (end ≥ start, start not in the past).
3. Backend creates an `Absence(PENDING)` and notifies all managers in realtime.

**Alternative Flows:**
- **AF1 — End date before start date, or start date in the past:** rejected.
- **AF2 — Student has no room:** rejected.
- **AF3 — A pending declaration already exists:** rejected; must cancel first (`PATCH /api/absences/:id/cancel`).

**Postconditions:** An `Absence(PENDING, type=TAM_VANG)` exists, awaiting review (UC-RES-05).

**Special Requirements:** The date range already supports multi-day absences, not just a single night — see UC-RES-03 for how this relates to "long-term" absence.

---

## UC-RES-02 — Register Temporary Residence ✅

**Actor(s):** Student

**Description:** A student registers that a guest will stay overnight in their room.

**Preconditions:** The student has a room assigned and no other declaration is currently `PENDING`.

**Basic Flow:**
1. Student opens `/student/absences`, selects "Tạm trú" (`AbsenceType.TAM_TRU`), enters start/end date, reason, and the guest's full name and ID number.
2. `POST /api/absences` requires `guestName` and `guestIdNumber` for this type, in addition to the same date/reason validation as UC-RES-01.
3. Backend creates an `Absence(PENDING, type=TAM_TRU)` and notifies managers.

**Alternative Flows:**
- **AF1 — Missing guest name or ID number:** rejected.
- **AF2 — Same date/room/pending-declaration guards as UC-RES-01.**

**Postconditions:** An `Absence(PENDING, type=TAM_TRU)` exists, awaiting review (UC-RES-05).

**Special Requirements:** None beyond the guest-detail requirement above.

---

## UC-RES-03 — Register Long-Term Temporary Absence 🔶

**Actor(s):** Student

**Description *(per diagram)*:** A student reports a long-term absence, presumably distinct from a short overnight absence (UC-RES-01) — e.g., required by local residency regulations once an absence exceeds a certain duration.

**Preconditions:** Same as UC-RES-01.

**Basic Flow (as actually implemented — no distinct type or threshold exists):**
1. A student wanting to declare a long-term absence uses the same "Tạm vắng" form as UC-RES-01, simply choosing a longer `endDate`.
2. The backend applies identical validation regardless of the absence's duration — there is no separate `AbsenceType` value, approval workflow, or documentation requirement triggered by length.

**Alternative Flows:** Same as UC-RES-01.

**Postconditions:** Same as UC-RES-01 — an `Absence(PENDING, type=TAM_VANG)`, indistinguishable in the data model from a short absence except by its date range.

**Special Requirements:**
- 🔶 **Not implemented as a distinct use case.** `AbsenceType` only has `TAM_TRU`/`TAM_VANG`; there is no "long-term" variant, duration threshold, or extra required fields. **Decision needed:** does Vietnamese dormitory/residency regulation require different handling once an absence exceeds a specific number of days (e.g., mandatory local-authority notification)? If so, define the threshold and any extra fields/approval steps; otherwise, merge this use case into UC-RES-01 in the diagram.

---

## UC-RES-04 — Register a Visitor 🆕

**Actor(s):** Student

**Description *(proposed)*:** A student registers a day-visitor (someone visiting during the day, not staying overnight) — distinct from UC-RES-02's overnight guest.

**Preconditions *(proposed)*:** The student has a room assigned.

**Basic Flow *(proposed)*:**
1. Student opens a "Đăng ký khách thăm" form, enters visitor name, ID number, visit date, and expected time window.
2. Frontend calls a new endpoint, e.g. `POST /api/visitors`.
3. Backend creates a `Visitor` record, possibly notifying security/reception.

**Alternative Flows *(proposed)*:**
- **AF1 — Visit outside allowed hours:** rejected per dormitory rules.

**Postconditions *(proposed)*:** A `Visitor` record exists for security/front-desk reference.

**Special Requirements:**
- 🆕 **Not yet implemented — no code exists for this.** This is the same gap already listed in `spec.md`'s Backlog ("Đăng ký khách thăm", proposal §3.6). It is distinct from UC-RES-02 (overnight guest, which already exists as `TAM_TRU`) because a day-visitor does not stay overnight and likely needs a lighter-weight registration (no ID-number-heavy overnight paperwork) and possibly a different review actor (security/front desk rather than the Dormitory Manager). **Decision needed:** is this in scope for the current sprint, and who reviews it?

---

## UC-RES-05 — Review and Track Residence Declarations ✅

**Actor(s):** Dormitory Manager

**Description:** A manager reviews pending temporary-residence/absence declarations (of either type) and approves or rejects them.

**Preconditions:** An `Absence(PENDING)` exists (of any type).

**Basic Flow:**
1. Manager opens `/admin/absences`, reviewing the pending list (student name/MSSV, room, type, dates, reason).
2. Manager clicks "Duyệt"; `PATCH /api/absences/:id/approve` sets `status = APPROVED`, `processedAt = now`, notifies the student.

**Alternative Flows:**
- **AF1 — Reject:** `PATCH /api/absences/:id/reject`; `status = REJECTED`; student notified.
- **AF2 — Declaration no longer pending** (e.g., cancelled by the student moments earlier): 404.

**Postconditions:** `Absence.status` becomes `APPROVED` or `REJECTED`.

**Special Requirements:** There is no separate downstream "occupancy tracking record" beyond the `Absence` document itself — the approved/rejected list **is** the tracking record for this feature. If UC-RES-04 (visitor registration) is built, this use case would also need to review that new record type.
