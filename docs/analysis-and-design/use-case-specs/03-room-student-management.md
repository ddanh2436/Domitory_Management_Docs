# Use-Case Specifications — 4. Room and Student Management

<!-- Performed by: <member>; Reviewed by: <member>; Edited by: <member> -->

> Diagram: see `../use-case-model.md` §4. Legend: ✅ implemented · 🔶 partially implemented. See `01-authentication-profile.md` for the shared screenshot note.

---

## UC-ROOM-01 — Manage Rooms ✅

**Actor(s):** Dormitory Manager (System Admin also has this access in the backend)

**Description:** A manager maintains the room catalog: add, update, remove, and mark rooms under maintenance.

**Preconditions:** Actor holds `ADMIN` or `DORMITORY_MANAGER`.

**Basic Flow:**
1. Manager opens `/admin/rooms`, adds a room (name, building, floor, capacity, price, facilities).
2. `POST /api/rooms` creates the room (`status = AVAILABLE` by default).
3. Manager edits an existing room via `PATCH /api/rooms/:id`, or sets `status = MAINTENANCE`.

**Alternative Flows:**
- **AF1 — Duplicate room name:** rejected by the unique index on `Room.name`.
- **AF2 — Delete a room:** `DELETE /api/rooms/:id`. ⚠ **verify:** confirm deletion is blocked when `currentOccupancy > 0` or active contracts reference the room.
- **AF3 — "Monitoring room conditions by floor"** (per §4's Floor Manager reassignment note): the room list can be filtered by building/floor client-side; ⚠ **verify** a floor filter control actually exists in the `/admin/rooms` UI, since the reassignment note assumes it does.

**Postconditions:** `rooms` collection updated; `RoomSchema`'s `pre('save')` hook keeps `status` consistent with occupancy vs. capacity.

**Special Requirements:** `genderType` (`MALE`/`FEMALE`/`MIXED`) exists on the schema for auto-assignment (UC-ROOM-07) but the create/edit form does not yet expose an input for it — every room defaults to `MIXED` until the form is extended.

---

## UC-STU-01 — Manage Student Records ✅

**Actor(s):** Dormitory Manager

**Description:** A manager views the student roster, filters/searches it, edits a student's core data, and reviews their room-rental history.

**Preconditions:** Actor holds a management role (view: `ADMIN`/`DORMITORY_MANAGER`/`FLOOR_MANAGER`; edit: `ADMIN` only via `PATCH /users/:id`).

**Basic Flow:**
1. Manager opens `/admin/students`; `GET /api/users/students` lists every student with room/contract summary.
2. Manager opens a student's detail to review booking/transfer/absence/violation history.
3. Admin edits core fields (full name, phone, CCCD, avatar) via `PATCH /api/users/:id`.

**Alternative Flows:**
- **AF1 — Non-Admin manager attempts to edit:** rejected (403) — only `ADMIN` may call `PATCH /users/:id`.
- **AF2 — Filter by floor/building:** ⚠ **verify** the student list page actually supports this filter, since the diagram's Floor Manager reassignment note (§4) assumes it does.

**Postconditions:** Targeted `User` document's whitelisted fields updated.

**Special Requirements:** "Room-rental history" is not a single dedicated view — it is assembled from separate `Booking`/`Transfer`/`Checkout` history endpoints, the same gap noted for UC-STAY-03 (residence history) on the student side.

---

## UC-ROOM-02 — Search Available Rooms ✅

**Actor(s):** Student

**Description:** A student without a room browses rooms by filters.

**Preconditions:** Logged in.

**Basic Flow:**
1. Student opens `/student/book-room`.
2. Frontend calls `GET /api/rooms?status=AVAILABLE` (plus other filters supported by `SearchRoomDto`: building, floor, price range).
3. Results list rooms with remaining capacity.

**Alternative Flows:**
- **AF1 — No rooms match the filter:** empty state shown.

**Postconditions:** None — read-only.

**Special Requirements:** None.

---

## UC-ROOM-03 — View Room Details *(included by UC-ROOM-04)* ✅

**Actor(s):** Student

**Description:** A student inspects a specific room's full details (price, capacity, facilities, current occupancy) before applying.

**Preconditions:** The room exists.

**Basic Flow:**
1. From the search results (UC-ROOM-02) or `/student/rooms/[id]`, the student opens a room's detail view.
2. `GET /api/rooms/:id` returns the room's full profile.

**Alternative Flows:**
- **AF1 — Room no longer exists / was deleted:** 404 shown.

**Postconditions:** None — read-only.

**Special Requirements:** This use case is always performed as part of UC-ROOM-04 (`«include»`), since a student must view a room before applying to it, per the diagram.

---

## UC-ROOM-04 — Submit Room Application ✅

**Actor(s):** Student

**Description:** A student submits a request to rent a chosen room.

**Preconditions:** The student does not currently occupy a room.

**Basic Flow:**
1. Student selects a room on `/student/book-room` (having viewed its details, UC-ROOM-03).
2. Frontend calls `POST /api/bookings` with the `roomId`.
3. Backend creates a `Booking(PENDING)`.

**Alternative Flows:**
- **AF1 — Student already has a room:** rejected.
- **AF2 — Room fills up between viewing and submitting:** rejected by the guarded creation logic; student must pick another room.
- **AF3 — Student cancels their own pending application:** `PATCH /api/bookings/:id/cancel`.

**Postconditions:** A `Booking(PENDING)` exists, awaiting review (UC-ROOM-06).

**Special Requirements:** None.

---

## UC-ROOM-05 — Track Room Application Status ✅

**Actor(s):** Student

**Description:** A student views the status of their own room applications.

**Preconditions:** Logged in.

**Basic Flow:**
1. Student opens `/student/bookings`.
2. `GET /api/bookings/me` returns the student's bookings with status (`PENDING`/`APPROVED`/`REJECTED`/`CANCELLED`).

**Alternative Flows:** None.

**Postconditions:** None — read-only.

**Special Requirements:** None.

---

## UC-ROOM-06 — Review Room Application ✅

**Actor(s):** Dormitory Manager

**Description:** A manager approves or rejects a pending room application; approval creates the student's first contract.

**Preconditions:** A `Booking(PENDING)` exists.

**Basic Flow:**
1. Manager opens `/admin/bookings`, clicks "Duyệt".
2. Backend, in a transaction: increments `Room.currentOccupancy`, sets `Booking.status = APPROVED`, creates a `Contract` (`ContractsService.createContractFromBooking()`), sets `User.room`.
3. Student is notified in realtime.

**Alternative Flows:**
- **AF1 — Reject:** `Booking.status = REJECTED`; student notified; no other changes.
- **AF2 — Room fills concurrently:** the guarded update fails; the transaction aborts and the manager must retry.

**Postconditions:** Approved: room occupancy incremented, contract created, `User.room` set. Rejected: booking closed only.

**Special Requirements:** Runs inside a MongoDB transaction for atomicity across the booking, room, and contract documents.

---

## UC-ROOM-07 — Run Automatic Room Allocation ✅ / 🔶

**Actor(s):** Dormitory Manager

**Description:** A manager bulk-assigns every student without a room into available rooms, matching gender where the room type requires it.

**Preconditions:** At least one student has no room; actor holds `ADMIN` or `DORMITORY_MANAGER`.

**Basic Flow:**
1. Manager opens `/admin/auto-assign`; `GET /api/assignments/preview` shows unassigned-student and free-slot counts.
2. Manager confirms "Chạy phân phòng tự động"; `POST /api/assignments/auto` runs.
3. For each unassigned student (name order), the system picks the first room whose `genderType` is `MIXED` or matches the student's `gender`, with remaining capacity; increments occupancy (guarded), creates a `Booking(APPROVED)` + `Contract`, sets `User.room`, and notifies the student.
4. A per-student results table is returned (assigned room, or skipped with a reason).

**Alternative Flows:**
- **AF1 — No compatible room:** student listed `SKIPPED` with reason.
- **AF2 — A room fills mid-run from a concurrent process:** that assignment is skipped; the run continues with the next student.
- **AF3 — Zero unassigned students or zero free slots:** the run button is disabled.

**Postconditions:** Zero or more students newly assigned rooms with bookings/contracts.

**Special Requirements:**
- 🔶 The use-case summary describes allocation "based on preferences, gender, capacity, and availability" — **student preferences are not implemented**; only gender-matching and capacity are considered. **Decision needed:** is a preference input (e.g., preferred building/floor, roommate requests) required, or should the description be narrowed to match the current gender+capacity-only algorithm?
- Requires `User.gender` and `Room.genderType`; the UI to input a student's/room's gender does not yet exist, so in practice every room currently behaves as `MIXED`.

---

## UC-ROOM-08 — Submit Room Transfer Request ✅

**Actor(s):** Student

**Description:** A student who already has a room requests to move to a different one.

**Preconditions:** The student occupies a room and has no other pending transfer.

**Basic Flow:**
1. Student opens `/student/transfers`, selects a target room and reason.
2. `POST /api/transfers` validates the target differs from the current room, has capacity, and no pending transfer exists; creates `Transfer(PENDING)`.
3. Managers are notified in realtime.

**Alternative Flows:**
- **AF1 — No room / target = current room / pending transfer exists:** rejected in each case.
- **AF2 — Cancel a pending transfer:** `PATCH /api/transfers/:id/cancel`.

**Postconditions:** `Transfer(PENDING)` exists, awaiting review (UC-ROOM-10).

**Special Requirements:** None.

---

## UC-ROOM-09 — Track Room Transfer Status ✅

**Actor(s):** Student

**Description:** A student views the status of their own transfer requests.

**Preconditions:** Logged in.

**Basic Flow:**
1. Student opens `/student/transfers`.
2. `GET /api/transfers/me` returns the student's transfer history with status.

**Alternative Flows:** None.

**Postconditions:** None — read-only.

**Special Requirements:** None.

---

## UC-ROOM-10 — Review Room Transfer Request ✅

**Actor(s):** Dormitory Manager

**Description:** A manager approves or rejects a pending transfer; approval moves the student and updates their contract to the new room's price.

**Preconditions:** A `Transfer(PENDING)` exists.

**Basic Flow:**
1. Manager opens `/admin/transfers`, clicks "Duyệt".
2. Backend, in a transaction: increments the target room's occupancy (guarded), decrements the source room's (floored at 0), sets `User.room`, and updates the active `Contract.room`/`rentalFee` to the target room.
3. Student is notified with the new room's details.

**Alternative Flows:**
- **AF1 — Reject:** `Transfer.status = REJECTED`; student notified; no other changes.
- **AF2 — Target room fills concurrently:** the guarded update fails; the manager must retry against a different room.

**Postconditions:** Both rooms' occupancy updated; `User.room` changed; active contract's room/price updated.

**Special Requirements:** Transaction-wrapped across two rooms, the user, and the contract for atomicity.
