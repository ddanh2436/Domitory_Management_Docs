# Use-Case Specifications — 6. Checkout and Deposit Refund

<!-- Performed by: <member>; Reviewed by: <member>; Edited by: <member> -->

> Diagram: see `../use-case-model.md` §6. See `01-authentication-profile.md` for the shared screenshot note.

---

## UC-CHK-01 — Submit Checkout Request ✅

**Actor(s):** Student

**Description:** A student who wants to move out submits a checkout request with a reason and expected departure date.

**Preconditions:** The student occupies a room, has a `Contract(ACTIVE)`, and has no other pending checkout.

**Basic Flow:**
1. Student opens `/student/checkout`, clicks "Yêu cầu trả phòng", enters the expected date and a reason.
2. `POST /api/checkouts` validates the date is not in the past and no other pending checkout exists.
3. Backend creates a `Checkout(PENDING)` with `depositAmount` defaulted to one month's rent (`Contract.rentalFee`).
4. Managers are notified in realtime.

**Alternative Flows:**
- **AF1 — Expected date in the past:** rejected.
- **AF2 — Student has no room / no active contract:** rejected.
- **AF3 — A pending checkout already exists:** rejected; must cancel first (`PATCH /api/checkouts/:id/cancel`).

**Postconditions:** A `Checkout(PENDING)` exists, awaiting review (UC-CHK-02).

**Special Requirements:** The deposit amount is a **convention** (one month's rent), not a real deposit collected at contract signing — the system does not currently collect deposits separately.

---

## UC-CHK-02 — Review Checkout Request ✅

**Actor(s):** Dormitory Manager

**Description:** A manager reviews a pending checkout request, inspects the room's assets for damage (UC-FIN-relevant compensation calc, extended by UC-CHK-03), and either rejects the request or proceeds to finalize it (UC-CHK-04).

**Preconditions:** A `Checkout(PENDING)` exists.

**Basic Flow:**
1. Manager opens `/admin/checkouts` and clicks "Kiểm tra & hoàn tất" on a pending request.
2. Manager inspects the room and, for each damaged/missing item found, adds a row: item name, fee (VND), optional note (this is UC-CHK-03, always performed here when deductions apply — see the diagram's `«extend»` relationship).
3. Manager may adjust the deposit amount if it differs from the one-month-rent default.
4. Manager confirms, which proceeds to UC-CHK-04.

**Alternative Flows:**
- **AF1 — Reject instead of completing:** `PATCH /api/checkouts/:id/reject`, optionally with an `adminNote`; `Checkout.status = REJECTED`; student notified; no contract/room changes.
- **AF2 — No damage found:** manager adds zero items; compensation is 0 and the full deposit will be refunded (UC-CHK-04).

**Postconditions:** Either `Checkout(REJECTED)`, or the flow proceeds into UC-CHK-04 with the damage list and deposit prepared.

**Special Requirements:** None beyond role restriction (`ADMIN`/`DORMITORY_MANAGER`/`FLOOR_MANAGER` in the current backend).

---

## UC-CHK-03 — Calculate Compensation Fee *(extends UC-CHK-02)* ✅

**Actor(s):** Dormitory Manager

**Description:** The manager enters damaged or missing items and their fees; the system live-computes the total compensation and resulting refund.

**Preconditions:** A checkout is being reviewed (UC-CHK-02) and at least one damaged item is being recorded.

**Basic Flow:**
1. For each item, manager enters name, fee (non-negative integer VND), and an optional note.
2. The modal live-computes: total compensation (sum of item fees) and refund = `max(0, deposit − compensation)`.
3. This computed breakdown carries into UC-CHK-04's confirmation.

**Alternative Flows:**
- **AF1 — Manager removes a row before submitting:** discarded; no server call has happened yet.
- **AF2 — Item name or fee left invalid:** the confirm action in UC-CHK-04 is blocked with an inline error until corrected.

**Postconditions:** None yet — purely client-side preparation; nothing is persisted until UC-CHK-04.

**Special Requirements:** This use case only occurs when deductions are recorded, per the diagram's own scope note — a checkout with no damaged items skips straight from UC-CHK-02 to UC-CHK-04 with zero compensation.

---

## UC-CHK-04 — Refund Deposit and Complete Checkout *(includes UC-CON-03)* ✅

**Actor(s):** Dormitory Manager

**Description:** The manager finalizes the checkout: the damage list and deposit from UC-CHK-02/03 are persisted, compensation and refund are computed, the contract is terminated, and the room slot is released — all atomically.

**Preconditions:** A `Checkout(PENDING)` exists with the damage list prepared (UC-CHK-02/03).

**Basic Flow:**
1. Manager reviews the computed refund amount in a confirmation dialog and confirms.
2. `PATCH /api/checkouts/:id/complete` is called with the damage list, (optionally adjusted) deposit amount, and an optional admin note.
3. Backend, inside a MongoDB transaction:
   a. Sets `Checkout.status = COMPLETED`, stores `damages[]`, `depositAmount`, `compensationAmount`, `refundAmount = max(0, deposit − compensation)`, `processedAt`.
   b. Sets the linked `Contract.status = TERMINATED`, `endDate = now` (this is the include relationship to UC-CON-03's postcondition, though implemented as separate inline logic — see UC-CON-03's maintainability note).
   c. Decrements the room's occupancy (floored at 0), reopens it to `AVAILABLE` if it had been `FULL`.
   d. Removes `room` from the student's `User` document.
4. After commit, the student is notified with the compensation charged and amount refunded.

**Alternative Flows:**
- **AF1 — Any transaction step fails:** the whole transaction aborts; `Checkout` remains `PENDING`; no partial changes to contract/room/user.

**Postconditions:** `Checkout(COMPLETED)` with full financial breakdown; `Contract(TERMINATED)`; room has one more free slot; student no longer occupies the room.

**Special Requirements:**
- Notifications are sent **after** the transaction commits, so a notification failure never rolls back already-committed changes.
- See UC-CON-03's maintainability note: this use case duplicates contract-termination/room-release logic independently rather than calling the same service method as the student's self-service termination.
