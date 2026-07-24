# Use-Case Specifications — 7. Finance and Meter Management

<!-- Performed by: <member>; Reviewed by: <member>; Edited by: <member> -->

> Diagram: see `../use-case-model.md` §7. Legend: ✅ implemented · 🔶 partially implemented · 🆕 not yet implemented. See `01-authentication-profile.md` for the shared screenshot note.

---

## UC-FIN-01 — Manage Electricity and Water Meter Readings 🔶

**Actor(s):** Dormitory Manager

**Description:** A manager records opening/closing meter readings per room, with photo evidence, and reviews consumption history over time.

**Preconditions:** Actor holds `ADMIN` or `DORMITORY_MANAGER`.

**Basic Flow (as actually implemented — readings as invoice-generation input, not a standalone record):**
1. Manager opens `/admin/invoices` and starts bulk invoice generation for a billing period.
2. For each room, manager enters `electricityKwh` and `waterM3` (consumption for the period) alongside a unit price.
3. `POST /api/invoices/generate-bulk` computes each room's electricity/water fee (`unitPrice × consumption`) and creates one invoice per room.

**Alternative Flows:**
- **AF1 — Single-invoice path:** manager enters pre-computed `electricityFee`/`waterFee` directly via `POST /api/invoices` instead of raw kWh/m³ readings.
- **AF2 — Duplicate invoice for the same room/period:** rejected by the unique index on `(room, month, year)`.

**Postconditions:** One or more `Invoice(PENDING)` documents created; the raw readings themselves are **not** persisted as a separate record once the invoice is generated.

**Special Requirements:**
- 🔶 **Partially implemented.** The description promises "upload meter photos" and "view consumption history" — neither exists today: readings are transient input parameters for `generate-bulk`, not stored in their own collection, so there is no photo attachment and no historical consumption chart/list to review later. **Decision needed:** introduce a dedicated `MeterReading` collection (room, period, opening/closing values, photo URL) if this history/photo requirement is in scope, or narrow the use-case description to match the current "readings as billing input only" behavior.

---

## UC-FIN-02 — Create or Bulk-Generate Invoices ✅

**Actor(s):** Dormitory Manager

**Description:** A manager creates a single invoice or bulk-generates invoices for many rooms from meter readings.

**Preconditions:** Actor holds `ADMIN` or `DORMITORY_MANAGER`.

**Basic Flow:** See UC-FIN-01's basic/alternative flows — they are the same underlying endpoints (`POST /invoices`, `POST /invoices/generate-bulk`).

**Alternative Flows:** See UC-FIN-01.

**Postconditions:** `Invoice(PENDING)` document(s) created with `totalAmount` computed from room/electricity/water fees.

**Special Requirements:** The unique index `(room, month, year)` is the authoritative guard against duplicate billing for the same period, even under concurrent bulk-generation calls.

---

## UC-FIN-03 — Mark Invoice as Paid Manually ✅

**Actor(s):** Dormitory Manager

**Description:** A manager manually marks an invoice paid for payments collected outside the electronic gateway (cash, offline bank transfer).

**Preconditions:** The invoice exists and is not already `PAID`.

**Basic Flow:**
1. Manager opens `/admin/invoices`, finds the target invoice, clicks "Đánh dấu đã thanh toán".
2. `PATCH /api/invoices/:id/pay` sets `status = PAID`, `paidAt = now`, and sends payment-confirmation notifications.

**Alternative Flows:**
- **AF1 — Invoice not found:** 404.

**Postconditions:** `Invoice.status = PAID`.

**Special Requirements:** None beyond role restriction.

---

## UC-FIN-04 — View Debt Summary ✅

**Actor(s):** Dormitory Manager

**Description:** A manager sees which rooms currently have unpaid invoices, the amounts owed, and who lives there.

**Preconditions:** Actor holds `ADMIN` or `DORMITORY_MANAGER`.

**Basic Flow:**
1. Manager opens `/admin/debts`.
2. `GET /api/invoices/debts` aggregates all `PENDING`/`OVERDUE` invoices grouped by room: total debt, unpaid count, overdue count, oldest due date, current occupants.
3. Dashboard shows overview tiles plus a per-room table.

**Alternative Flows:**
- **AF1 — No rooms currently owe anything:** positive empty state.
- **AF2 — A debt room has no current occupants:** shown distinctly; the reminder action is disabled for that row.

**Postconditions:** None — read-only. Aggregated **by room**, not by individual student, per the current implementation (the description says "by student or room" — student-level breakdown is not separately offered).

**Special Requirements:** None.

---

## UC-FIN-05 — Send Debt Reminders ✅ / 🔶

**Actor(s):** Dormitory Manager; Scheduled Trigger *(per diagram — see flag below)*

**Description:** A manager sends a reminder notification to one indebted room's students, or to all indebted rooms at once.

**Preconditions:** At least one room appears in the debt summary (UC-FIN-04).

**Basic Flow:**
1. Manager clicks "Nhắc nợ" on a room's row (or "Nhắc nợ tất cả" for all rooms).
2. `POST /api/invoices/debts/:roomId/remind` (or `.../remind-all`) re-confirms unpaid invoices exist and notifies every current occupant in realtime.

**Alternative Flows:**
- **AF1 — Room no longer has unpaid invoices** (paid between page load and the reminder click): 404 ("Phòng này không còn hóa đơn nợ nào").
- **AF2 — Room has no occupants:** the remind action is disabled.
- **AF3 — A reminder fails for one student:** does not stop reminders to the room's other occupants, nor "remind all" from proceeding to the next room.

**Postconditions:** None persisted — a notification is sent.

**Special Requirements:**
- 🔶 **The diagram shows `Scheduled Trigger → UC-FIN-05`, implying automatic/scheduled debt reminders — this does not exist.** All debt reminders are manager-triggered only; the only cron-scheduled job in this group is UC-FIN-07 (marking overdue). **Decision needed:** add a scheduled reminder job (e.g., weekly for rooms still in debt), or correct the diagram to remove the `Scheduled Trigger` link from this use case.

---

## UC-FIN-06 — Generate Revenue Report ✅ / 🔶

**Actor(s):** System Admin *(the backend restricts this to `ADMIN`, not `DORMITORY_MANAGER`, despite the diagram assigning it to Dormitory Manager)*

**Description:** A report of actual collected revenue (paid invoices only), broken down by room/electricity/water fees.

**Preconditions:** Actor holds `ADMIN`.

**Basic Flow:**
1. Actor views the dashboard's revenue chart.
2. `GET /api/invoices/stats/revenue` aggregates `Invoice(PAID)` documents by month/year and returns the most recent 6 periods.

**Alternative Flows:**
- **AF1 — Fewer than 6 months of paid invoices:** chart shows however many periods exist.
- **AF2 — No paid invoices:** chart is empty.

**Postconditions:** None — read-only.

**Special Requirements:**
- 🔶 **Actor mismatch:** the diagram assigns this to Dormitory Manager, but the backend restricts `stats/revenue` to `ADMIN` only. **Decision needed:** open the endpoint to `DORMITORY_MANAGER` too, or correct the diagram.
- 🔶 Only **monthly** grouping is implemented; the description promises "monthly, quarterly, or yearly" reports — quarterly/yearly aggregation does not exist. **Decision needed** on whether to add it.

---

## UC-FIN-07 — Mark Overdue Invoices ✅

**Actor(s):** Scheduled Trigger (automatic); Dormitory Manager (manual override)

**Description:** Invoices whose due date has passed without payment are flagged overdue and the room's students notified; a manager can also trigger this on demand.

**Preconditions:** At least one `Invoice(PENDING)` has a due date in the past (or, lacking a due date, belongs to a prior billing period).

**Basic Flow:**
1. A daily cron job runs `markOverdueInvoices()`, finding every eligible `Invoice(PENDING)`.
2. For each match: `status = OVERDUE`, `overdueAt = now`; every student in that room is notified with the amount and original due date.

**Alternative Flows:**
- **AF1 (extends) — Manual trigger:** manager clicks "Cập nhật quá hạn" on `/admin/invoices`; `POST /api/invoices/trigger-overdue` runs the same logic on demand.
- **AF2 — Notification fails for one room:** logged; does not stop the rest of the batch.
- **AF3 — Nothing is currently overdue:** completes with `updated: 0, notified: 0`.

**Postconditions:** Matching invoices become `OVERDUE`; students are notified; the invoices now count toward UC-FIN-04's debt summary.

**Special Requirements:** This is the only genuinely scheduler-driven use case in this group — see UC-FIN-05's flag about the mismatched Scheduler link there.

---

## UC-FIN-08 — View Invoices ✅

**Actor(s):** Student

**Description:** A student views their room's invoice list and details.

**Preconditions:** The student belongs to a room.

**Basic Flow:**
1. Student opens `/student/invoices`.
2. `GET /api/invoices/room/:roomId` returns the room's invoices (accessible to the room's own students as well as managers).

**Alternative Flows:** None.

**Postconditions:** None — read-only.

**Special Requirements:** None.

---

## UC-FIN-09 — Pay Invoice 🔶

**Actor(s):** Student; Payment Gateway *(per diagram — see flag below)*

**Description:** A student pays an invoice through a supported payment method.

**Preconditions:** An unpaid invoice exists for the student's room.

**Basic Flow (as actually implemented — mock gateway only):**
1. Student selects an unpaid invoice and proceeds to `/student/payment/[id]`.
2. Student confirms payment; `PATCH /api/invoices/:id/pay-mock` verifies ownership, sets `status = PAID`, `paidAt = now`, sends confirmation notifications.

**Alternative Flows:**
- **AF1 — Invoice already paid:** payment action unavailable.
- **AF2 — Student attempts to pay for a room they don't belong to:** rejected by the ownership check.

**Postconditions:** `Invoice.status = PAID`.

**Special Requirements:**
- 🔶 **No real Payment Gateway integration exists.** The diagram lists "bank, VNPay, MoMo, ZaloPay" as alternative flows of this single use case — today there is exactly one flow, a mock gateway that always succeeds. **Decision needed (tracked in `spec.md` backlog):** integrate a real provider, and if only one is chosen for this submission, update the diagram/description to reflect that rather than listing all four as already-supported alternatives.

---

## UC-FIN-10 — View Payment History 🔶

**Actor(s):** Student

**Description:** A student reviews their successful, pending, and failed payments.

**Preconditions:** Logged in.

**Basic Flow (as actually implemented):**
1. Student opens `/student/invoices`, where paid invoices are visible inline (status `PAID`) alongside pending/overdue ones — there is no separate "payment history" page or transaction log.

**Alternative Flows:** None distinct today.

**Postconditions:** None — read-only.

**Special Requirements:**
- 🔶 **No dedicated payment-history view or transaction ledger exists** (e.g., gateway transaction ID, attempted-but-failed payment records). Because the payment gateway is currently a mock that always succeeds (UC-FIN-09), there is also no concept of a "failed payment" to show. **Decision needed:** is the current invoice list (implicitly showing paid/unpaid) sufficient, or is a distinct payment-history/ledger view required?

---

## UC-FIN-11 — Download Invoice PDF ✅

**Actor(s):** Student

**Description:** A student exports an invoice as a PDF file.

**Preconditions:** An invoice exists.

**Basic Flow:**
1. Student clicks "📄 Xuất hóa đơn PDF" on `/student/invoices`.
2. `exportInvoicePdf()` (in `app/utils/exportPdf.ts`) renders the invoice into a printable document and opens the browser's print-to-PDF dialog.

**Alternative Flows:**
- **AF1 — Popup blocked:** export fails; ⚠ **verify** an error is surfaced to the user.

**Postconditions:** None server-side.

**Special Requirements:** Client-side only, same mechanism family as the contract PDF export (UC-CON-04/UC-CON-06).
