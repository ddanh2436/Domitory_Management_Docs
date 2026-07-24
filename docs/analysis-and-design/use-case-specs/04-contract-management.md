# Use-Case Specifications — 5. Contract Management

<!-- Performed by: <member>; Reviewed by: <member>; Edited by: <member> -->

> Diagram: see `../use-case-model.md` §5. See `01-authentication-profile.md` for the shared screenshot note.
>
> ⚠ **Read this before the individual specs:** two use cases in this group (UC-CON-02, UC-CON-03) are attributed to **Dormitory Manager** in the diagram, but the corresponding actions in the actual codebase are **student self-service** endpoints (`POST /contracts/extend`, `POST /contracts/terminate`, both driven by `req.user.sub` from the `/student/contracts` page). This is flagged in detail in each spec below — **team decision needed** on whether to (a) correct the diagram's actor to Student, (b) additionally build a manager-side extend/terminate action and keep both, or (c) restrict the current student self-service endpoints to managers only, matching the diagram exactly.

---

## UC-CON-01 — Create Rental Contract ✅ *(system-automatic)*

**Actor(s):** Dormitory Manager *(credited actor — the contract is created automatically as a side effect of the manager's approval action, not entered manually via a blank form)*

**Description:** A rental contract is generated automatically once a room application (UC-ROOM-06) or automatic room allocation (UC-ROOM-07) is approved.

**Preconditions:** A `Booking` is being approved, or a student is being auto-assigned a room.

**Basic Flow:**
1. Manager approves a room application (UC-ROOM-06) or runs auto-assignment (UC-ROOM-07).
2. As part of that same transaction, the system (`ContractsService.createContractFromBooking()`) generates a unique `contractNumber` (e.g., `HD-2026-...`), sets `startDate = now`, `endDate = now + 5 months`, `rentalFee = room.price`, and standard `terms` text.
3. The contract is linked to the booking, user, and room.

**Alternative Flows:**
- **AF1 — Contract-number collision:** extremely unlikely (timestamp + random suffix), but guarded by a unique index on `contractNumber` as a last-resort safety net.

**Postconditions:** A new `Contract(ACTIVE)` exists.

**Special Requirements:** There is no manager UI to create a contract manually/independently of a booking approval or auto-assignment — this use case only ever happens as a byproduct of UC-ROOM-06/UC-ROOM-07, consistent with the diagram's own note ("A rental contract may be created after a room application or automatic room allocation has been approved").

---

## UC-CON-02 — Extend Rental Contract ✅ (actor mismatch — see note above)

**Actor(s) per diagram:** Dormitory Manager · **Actor per actual code:** Student (self-service)

**Description:** The contract's end date is extended by a chosen number of months.

**Preconditions:** An active contract exists for the student.

**Basic Flow (as actually implemented):**
1. Student opens `/student/contracts` and clicks "Gia hạn hợp đồng" (the frontend currently sends a fixed 6 months, not a student-chosen value, despite the backend accepting 1–12).
2. Frontend calls `POST /api/contracts/extend` with `{ months: 6 }`.
3. Backend finds the student's own `Contract(ACTIVE)` (via `req.user.sub`) and adds the requested months to `endDate`.

**Alternative Flows:**
- **AF1 — Invalid months value (< 1, > 12, non-integer):** rejected.
- **AF2 — `months` omitted entirely:** backend defaults to 6 months rather than rejecting.
- **AF3 — No active contract found:** 404.

**Postconditions:** `Contract.endDate` extended.

**Special Requirements:**
- ⚠ **Actor mismatch (see group-level note):** this is a student self-service action today, not a manager action as the diagram states.
- The endpoint has **no `@Roles` guard at all** — any authenticated role could technically call it, though only a role holding a contract would find it meaningful. **Decision needed:** add an explicit role restriction once the correct actor is confirmed.

---

## UC-CON-03 — Liquidate Rental Contract ✅ (actor mismatch — see note above)

**Actor(s) per diagram:** Dormitory Manager · **Actor per actual code:** Student (self-service); also performed internally by the system during checkout completion (UC-CHK-04)

**Description:** The contract is ended early and the room slot released.

**Preconditions:** An active contract exists for the student.

**Basic Flow (as actually implemented, student self-service):**
1. Student opens `/student/contracts` and clicks "Thanh lý hợp đồng".
2. `POST /api/contracts/terminate` finds the student's own `Contract(ACTIVE)`, sets `status = TERMINATED`, `endDate = now`.
3. Backend decrements the room's occupancy (floored at 0), reopens it to `AVAILABLE` if it had been `FULL`, and removes `room` from the student's `User` document.

**Alternative Flows:**
- **AF1 — No active contract found:** 404.
- **AF2 *(«include»* by UC-CHK-04)** — when a checkout is completed by a manager, the system performs equivalent contract-termination logic **inline inside `CheckoutsService.completeCheckout()`**, not by calling this same code path. ⚠ **Maintainability note:** the two termination implementations are independent; if the termination business rule changes, both must be updated together.

**Postconditions:** `Contract(TERMINATED)`; room regains one free slot; student no longer occupies the room.

**Special Requirements:**
- ⚠ **Actor mismatch (see group-level note):** letting a student self-terminate their own contract outside the formal checkout process (UC-CHK-01–04) means they can skip asset inspection and deposit-refund calculation entirely. **Decision needed:** should self-service termination remain available once the full checkout workflow exists, or should it be manager-only (matching the diagram) / removed in favor of always going through checkout?
- Same missing-`@Roles`-guard note as UC-CON-02.

---

## UC-CON-04 — Export Contract PDF ✅

**Actor(s):** Dormitory Manager

**Description:** A manager exports a student's contract as a PDF from the contract-management screen.

**Preconditions:** A contract exists.

**Basic Flow:**
1. Manager opens `/admin/contracts` and clicks "📄 PDF" on a contract row.
2. Frontend (`exportContractPdf()` in `app/utils/exportPdf.ts`) renders the contract into a printable HTML document in a new window and triggers the browser's print-to-PDF dialog.

**Alternative Flows:**
- **AF1 — Popup blocked by the browser:** the export silently fails to open; ⚠ **verify** the UI surfaces an error toast in this case.

**Postconditions:** None server-side — a PDF is saved to the manager's device via the browser's print dialog.

**Special Requirements:** Generation is entirely client-side (no dedicated backend PDF endpoint); same underlying utility function is reused by the student-side export (UC-CON-06).

---

## UC-CON-05 — View Rental Contract ✅

**Actor(s):** Student

**Description:** A student views their own contract's terms, dates, and rent.

**Preconditions:** Logged in.

**Basic Flow:**
1. Student opens `/student/contracts`.
2. `GET /api/contracts/my-contract` returns the student's contract (any status), populated with room and user info.

**Alternative Flows:**
- **AF1 — No contract exists:** empty state shown.

**Postconditions:** None — read-only.

**Special Requirements:** ⚠ **verify** whether the endpoint should be filtered to `ACTIVE` contracts only, or intentionally returns the most recent contract regardless of status (e.g., so a student can still see a just-terminated contract right after checkout).

---

## UC-CON-06 — Download Contract PDF *(extends UC-CON-05)* ✅

**Actor(s):** Student

**Description:** A student downloads their own contract as a PDF for printing/record-keeping.

**Preconditions:** The student has a contract to export (UC-CON-05).

**Basic Flow:**
1. From `/student/contracts`, student clicks the PDF export action.
2. `exportContractPdf()` renders the contract client-side and opens the browser's print-to-PDF dialog.

**Alternative Flows:**
- **AF1 — Popup blocked:** export fails; ⚠ **verify** error feedback is shown.

**Postconditions:** None server-side.

**Special Requirements:** Same client-side mechanism and utility function as UC-CON-04.
