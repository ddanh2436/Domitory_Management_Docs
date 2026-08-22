# PA5-2026 — Part A: Test Plan and Test Cases

**Course:** CS300 - CSC13002 - Introduction to Software Engineering
**Project:** Dormify — Dormitory Management System
**Document scope:** Test Plan + functional test cases for the 5 use cases with the greatest impact on the overall project. Test execution results and the Bug Report will be added in a later revision of this document, after the test cases below have been run against the implemented features.

*Performed by: [Hồ Phúc Kiên - 24127067] · Reviewed by: [Hồ Phúc Kiên - 24127067] · Edited by: [Hồ Phúc Kiên - 24127067]*

---

## A. Test Plan

*Performed by: [Hồ Phúc Kiên - 24127067] · Reviewed by: [Hồ Phúc Kiên - 24127067] · Edited by: [Hồ Phúc Kiên - 24127067]*

### 1. Test Objectives and Scope

**Objectives**
- Verify that the core student-lifecycle features of Dormify — login, room application review, automatic room allocation, invoice generation, and checkout/deposit refund — work correctly end‑to‑end against the specifications in `docs/analysis-and-design/use-case-specs/`.
- Confirm that state transitions that touch multiple collections in the same transaction (`Booking` → `Contract` → `Room.currentOccupancy` → `User.room`, and the reverse on checkout) remain consistent (atomicity), including under guarded/concurrent conditions.
- Confirm that role-based access control (`ADMIN` / `FLOOR_MANAGER` / `MAINTENANCE_STAFF` / `STUDENT`) is enforced on every endpoint exercised by the selected use cases.
- Validate boundary and negative inputs (invalid credentials, duplicate invoices, over-capacity rooms, compensation exceeding deposit, etc.) produce the documented error behavior instead of silent failure.

**Scope**
- In scope: the 5 use cases selected in Section B (`UC-AUTH-02`, `UC-ROOM-06`, `UC-ROOM-07`, `UC-FIN-02`, `UC-CHK-04`) and the endpoints/collections they exercise (`auth`, `bookings`, `assignments`, `rooms`, `contracts`, `invoices`, `checkouts`, `users`, `notifications`).
- Out of scope for this document: unit tests of individual service methods (covered separately by the dev team where present), load/performance testing, and use cases not listed in Section B (these keep the test cases already produced by Spec Kit during PA3/PA4, to be reviewed separately).

### 2. Features to be Tested

| # | Feature | Module(s) touched | Use case(s) |
|---|---|---|---|
| 1 | Authentication (login by email/MSSV, role-based redirect, locked-account handling) | `auth`, `users` | UC-AUTH-02 |
| 2 | Manual review (approve/reject) of a student's room application, with automatic contract creation | `bookings`, `rooms`, `contracts`, `users`, `notifications` | UC-ROOM-06 (includes UC-CON-01) |
| 3 | Bulk automatic room allocation for all unassigned students | `assignments`, `bookings`, `rooms`, `contracts`, `users`, `notifications` | UC-ROOM-07 |
| 4 | Invoice creation, single and bulk, from room/electricity/water fees | `invoices`, `rooms`, `contracts` | UC-FIN-02 |
| 5 | Checkout finalization: compensation calculation, deposit refund, contract termination, room release | `checkouts`, `contracts`, `rooms`, `users`, `notifications` | UC-CHK-04 (includes UC-CON-03) |

### 3. Test Environment and Tools

- **Backend:** NestJS (Node.js), MongoDB (local instance or Dockerized replica set — required for multi-document transactions used by UC-ROOM-06/07 and UC-CHK-04), run via `npm run start:dev` in `Domitory_Management_Backend`.
- **Frontend:** Next.js App Router, run via `npm run dev` in `Domitory_Management_Frontend`, `NEXT_PUBLIC_API_URL=http://localhost:3001/api`.
- **Test accounts:** at least one seeded account per role (`STUDENT`, `FLOOR_MANAGER`, `MAINTENANCE_STAFF`, `ADMIN`) with known credentials, plus disposable student accounts created per test case so bookings/contracts/invoices/checkouts do not collide between test runs.
- **Tools:**
  - Postman / `curl` (or the provided `scripts/e2e-run.js`, `scripts/e2e-seed.js`, `scripts/e2e-cleanup.js`) for direct API-level test execution and setup/teardown.
  - Browser (Chrome) for UI-driven test steps (`/admin/bookings`, `/admin/auto-assign`, `/admin/invoices`, `/admin/checkouts`, `/login`).
  - MongoDB Compass or `mongosh` to verify persisted state (`Booking`, `Contract`, `Room.currentOccupancy`, `User.room`, `Invoice`, `Checkout`) directly in the database, as required by several test cases below.
  - Browser DevTools / a WebSocket inspector to verify realtime notifications where specified.
- **Test data reset:** each test case that mutates state (booking approval, auto-assignment, invoice generation, checkout completion) must start from a known, isolated data set (a dedicated test room and test student per case) so results do not depend on execution order.

### 4. Entry and Exit Criteria

**Entry criteria**
- The 5 selected use cases are implemented and deployable locally (backend + frontend running together).
- Seed data (at least one manager account, one admin account, and rooms with free capacity) is available.
- This test case document has been reviewed by at least one teammate other than the author of each section.

**Exit criteria**
- All 55 test cases below have been executed at least once with a recorded Pass/Fail status and actual result.
- Every failed test case is linked to at least one bug report with severity and status, per the Bug Report requirements.
- No open bug with severity "Blocker" or "Critical" remains against the 5 selected use cases at submission time.
- The test summary (features tested, test cases, pass/fail counts per feature) is complete and consistent with the recorded execution results.

---

## B. Selected Use Cases and Justification

The following 5 use cases were selected from `docs/analysis-and-design/use-case-specs/` as the ones with the greatest impact on the overall project. Together they form the backbone of the student lifecycle — from the moment a user can access the system at all, through how a student is matched to a room and a contract is created (either individually or in bulk), to how the dormitory collects revenue, and finally how a student's stay is closed out. A defect in any one of these breaks or corrupts data for every other module downstream of it (bookings, contracts, rooms, invoices, and checkouts all reference each other).

| Use case | Why it has high project-wide impact |
|---|---|
| **UC-AUTH-02** — Log In | Gatekeeper for every other feature in the system; every other use case in this document (and in the whole application) is unreachable without it. A regression here blocks all roles, not just one module. |
| **UC-ROOM-06** — Review Room Application *(includes UC-CON-01 — Create Rental Contract)* | The single transaction that turns a `Booking` into an `Contract`, increments `Room.currentOccupancy`, and sets `User.room`. Almost every other module (invoices, checkouts, transfers, contracts) assumes a student already has a room and an active contract created through this flow. |
| **UC-ROOM-07** — Run Automatic Room Allocation | Same downstream effect as UC-ROOM-06 (booking + contract + room + user updates) but executed in bulk for many students at once inside one operation — the highest-blast-radius write path in the system if it misbehaves (e.g., double-booking a room, or corrupting occupancy counts for many rooms simultaneously). |
| **UC-FIN-02** — Create or Bulk-Generate Invoices | The entry point for all revenue in the system; `UC-FIN-03` (mark paid), `UC-FIN-09` (student pays), `UC-FIN-05` (debt reminders), and `UC-FIN-06` (revenue report) all depend on invoices produced correctly here, including the `(room, month, year)` uniqueness guard that prevents double billing. |
| **UC-CHK-04** — Refund Deposit and Complete Checkout *(includes UC-CON-03 — Liquidate Rental Contract)* | Closes the student lifecycle opened by UC-ROOM-06/UC-ROOM-07: terminates the contract, releases the room slot (so the room becomes available for a new UC-ROOM-06/07 cycle), and computes a monetary refund. An error here corrupts room availability for future students and can result in an incorrect refund amount. |

---

## C. Use-Case Specifications and Test Cases

*Performed by: [Hồ Phúc Kiên - 24127067] · Reviewed by: [Hồ Phúc Kiên - 24127067] · Edited by: [Hồ Phúc Kiên - 24127067]*

---

### 1. UC-AUTH-02 — Log In

| Field | Detail |
|---|---|
| **Use case ID** | UC-AUTH-02 |
| **Use Case Name** | Log In |
| **Description** | A user authenticates with an email or MSSV plus a password (or via Google), and is routed to the area matching their role (`/admin`, `/staff`, or `/student`). |
| **Actor(s)** | Applicant/Guest, Student, Maintenance Staff, Floor Manager, Admin (every role) |
| **Preconditions** | The account exists and `accessStatus = ACTIVE`. |
| **Main Flow** | 1. User submits an identifier (email or MSSV) and password on `/login`.<br>2. Frontend calls `POST /api/auth/login`.<br>3. Backend finds the user by `email` or `mssv`, verifies the password hash, and checks `accessStatus`.<br>4. Backend issues a JWT containing `sub`, `email`, and `role`.<br>5. Frontend persists the token (`localStorage` + cookie) and redirects the user to `/admin`, `/staff`, or `/student` based on role. |
| **Alternative / Exception Flows** | **AF1 — Wrong password:** system returns HTTP `401` with message "Sai mật khẩu"; user remains on `/login`.<br>**AF2 — Identifier not found:** HTTP `401` with message "Sai thông tin đăng nhập".<br>**AF3 — Account locked:** HTTP `401` with the recorded block reason; user cannot proceed.<br>**AF4 — Log in with Google:** `POST /api/auth/google` verifies the Google ID token and auto-creates an account on first login if none exists for that email. |
| **Postconditions** | A valid JWT is held by the client; `JwtAuthGuard` re-reads `accessStatus` from the database on every subsequent request. |
| **Special Requirements** | Passwords are never returned by any query; a lock applied to an account mid-session takes effect on the very next API call, not only at next login. |

#### Test Cases

**Test case 1**

| Field | Detail |
|---|---|
| Test case ID | TC-AUTH-02-01 |
| Test case name | Login successfully with valid email and password (Student) |
| Description | Verify that a student with correct email + password credentials can log in and is redirected to `/student`. |
| Related Use case | UC-AUTH-02 — Log In |
| Input Data | Account: `e2e.student@test.local` / password `E2Etest123` (role = STUDENT, accessStatus = ACTIVE) |
| Expected Output | - HTTP `200 OK` with a JWT in the response body.<br>- `localStorage` and the `token` cookie both contain the token.<br>- Browser is redirected to `/student`. |
| Test steps | 1. Open `/login`.<br>2. Enter `e2e.student@test.local` as identifier and `E2Etest123` as password.<br>3. Click "Đăng nhập".<br>4. Observe the redirect and check `localStorage`/cookie for the token. |

**Test case 2**

| Field | Detail |
|---|---|
| Test case ID | TC-AUTH-02-02 |
| Test case name | Login successfully with valid MSSV instead of email |
| Description | Verify the identifier field accepts a valid MSSV in place of an email and still authenticates the same account. |
| Related Use case | UC-AUTH-02 — Log In |
| Input Data | Account with `mssv = E2E0001`, password `E2Etest123` |
| Expected Output | - HTTP `200 OK`, JWT returned.<br>- Redirect to the role's area (`/student`). |
| Test steps | 1. Open `/login`.<br>2. Enter `E2E0001` as identifier and `Passw0rd!` as password.<br>3. Click "Đăng nhập".<br>4. Confirm successful redirect. |

**Test case 3**

| Field | Detail |
|---|---|
| Test case ID | TC-AUTH-02-03 |
| Test case name | Login fails with wrong password |
| Description | Verify that an incorrect password for an existing identifier is rejected with the documented error message. |
| Related Use case | UC-AUTH-02 — Log In |
| Input Data | Identifier: `e2e.student@test.local`, password: `WrongPass1` |
| Expected Output | - HTTP `401 Unauthorized`.<br>- Error message "Sai mật khẩu" shown on the login form.<br>- No token stored, no redirect. |
| Test steps | 1. Open `/login`.<br>2. Enter `e2e.student@test.local` / `WrongPass1`.<br>3. Click "Đăng nhập".<br>4. Observe the error message and confirm the user stays on `/login`. |

**Test case 4**

| Field | Detail |
|---|---|
| Test case ID | TC-AUTH-02-04 |
| Test case name | Login fails with an identifier that does not exist |
| Description | Verify that logging in with an email/MSSV not present in the system is rejected with the documented error message, without revealing whether the account exists. |
| Related Use case | UC-AUTH-02 — Log In |
| Input Data | Identifier: `nonexistent@example.com`, password: `Anything1` |
| Expected Output | - HTTP `401 Unauthorized`.<br>- Error message "Sai thông tin đăng nhập".<br>- No token stored. |
| Test steps | 1. Open `/login`.<br>2. Enter `nonexistent@example.com` / `Anything1`.<br>3. Click "Đăng nhập".<br>4. Observe the error message. |

**Test case 5**

| Field | Detail |
|---|---|
| Test case ID | TC-AUTH-02-05 |
| Test case name | Login rejected for a locked account |
| Description | Verify a user whose `accessStatus` is not `ACTIVE` (locked by an admin) cannot log in even with correct credentials, and sees the recorded block reason. |
| Related Use case | UC-AUTH-02 — Log In |
| Input Data | Account `e2e.student@test.local` / `E2Etest123` with `accessStatus = LOCKED`, `blockReason = "Vi phạm nội quy"` |
| Expected Output | - HTTP `401 Unauthorized`.<br>- Error message includes the recorded block reason "Vi phạm nội quy".<br>- No token stored. |
| Test steps | 1. As Admin, lock the target account via `/admin` with reason "Vi phạm nội quy".<br>2. Open `/login` in a separate/incognito session.<br>3. Enter `e2e.student@test.local` / `E2Etest123`.<br>4. Observe the error message. |

**Test case 6**

| Field | Detail |
|---|---|
| Test case ID | TC-AUTH-02-06 |
| Test case name | Login as Admin redirects to /admin |
| Description | Verify that a user with role `ADMIN` is redirected to the admin area after successful login. |
| Related Use case | UC-AUTH-02 — Log In |
| Input Data | Account `example@gmail.com` / `123456` (role = ADMIN) |
| Expected Output | - HTTP `200 OK`.<br>- Browser redirected to `/admin`. |
| Test steps | 1. Open `/login`.<br>2. Enter admin credentials.<br>3. Click "Đăng nhập".<br>4. Confirm redirect target is `/admin`. |

**Test case 7**

| Field | Detail |
|---|---|
| Test case ID | TC-AUTH-02-07 |
| Test case name | Login as Maintenance Staff / Floor Manager redirects to /staff |
| Description | Verify that manager/staff roles are redirected to `/staff`, not `/admin` or `/student`. |
| Related Use case | UC-AUTH-02 — Log In |
| Input Data | Account `norway@example.com` / `norway1234` (role = FLOOR_MANAGER); Account `example4@gmail.com` / `123456789` (role = MAINTENANCE_STAFF) |
| Expected Output | - HTTP `200 OK`.<br>- Browser redirected to `/staff`. |
| Test steps | 1. Open `/login`.<br>2. Enter manager credentials.<br>3. Click "Đăng nhập".<br>4. Confirm redirect target is `/staff`. |

**Test case 8**

| Field | Detail |
|---|---|
| Test case ID | TC-AUTH-02-08 |
| Test case name | Login rejected with empty password field |
| Description | Verify client- and/or server-side validation rejects an empty password before or instead of an authentication attempt. |
| Related Use case | UC-AUTH-02 — Log In |
| Input Data | Identifier: `student1@example.com`, password: `` (empty) |
| Expected Output | - Form blocks submission with a "required field" validation message, **or**, if submitted, HTTP `400 Bad Request` from the API.<br>- No token stored. |
| Test steps | 1. Open `/login`.<br>2. Enter `student1@example.com`, leave password blank.<br>3. Click "Đăng nhập".<br>4. Observe validation behavior. |

**Test case 9**

| Field | Detail |
|---|---|
| Test case ID | TC-AUTH-02-09 |
| Test case name | Login rejected with malformed email-like identifier |
| Description | Verify that an obviously malformed identifier (not a valid email and not a plausible MSSV) is handled gracefully — either validated client-side or rejected as "not found" server-side — without a server error. |
| Related Use case | UC-AUTH-02 — Log In |
| Input Data | Identifier: `student1@@@example`, password: `Passw0rd!` |
| Expected Output | - No HTTP `500` error.<br>- Either a client-side validation error, or HTTP `401` "Sai thông tin đăng nhập". |
| Test steps | 1. Open `/login`.<br>2. Enter `student1@@@example` / `Passw0rd!`.<br>3. Click "Đăng nhập".<br>4. Observe the response/behavior; confirm no server crash. |

**Test case 10**

| Field | Detail |
|---|---|
| Test case ID | TC-AUTH-02-10 |
| Test case name | Account locked mid-session invalidates further access immediately |
| Description | Verify that `JwtAuthGuard` re-checks `accessStatus` live, so locking a user while they hold a still-valid JWT blocks their very next API call (not just their next login). |
| Related Use case | UC-AUTH-02 — Log In |
| Input Data | Student logged in and holding a valid JWT; admin locks the same account in a separate session mid-way through. |
| Expected Output | - The student's next API call (e.g., `GET /api/bookings/me`) returns HTTP `401`, even though the JWT itself has not expired.<br>- The student is redirected to `/login` on their next protected-page navigation. |
| Test steps | 1. Log in as a student and keep the session open on `/student`.<br>2. In a second browser/session, log in as Admin and lock the student's account.<br>3. Back in the student's session, trigger an API call (e.g., refresh `/student/bookings`).<br>4. Observe the `401` response and forced redirect to `/login`. |

**Test case 11**

| Field | Detail |
|---|---|
| Test case ID | TC-AUTH-02-11 |
| Test case name | First-time login via Google auto-creates an account |
| Description | Verify that authenticating via Google with an email not yet registered creates a new account automatically and logs the user in. |
| Related Use case | UC-AUTH-02 — Log In (AF4) |
| Input Data | A Google account whose email has never been used to register in Dormify. |
| Expected Output | - HTTP `200/201` from `POST /api/auth/google`.<br>- A new `User` document exists for that email (role = STUDENT by default).<br>- User is logged in and redirected to `/student`. |
| Test steps | 1. Open `/login` and click "Đăng nhập với Google".<br>2. Complete the Google OAuth flow with a fresh email.<br>3. Observe redirect to `/student`.<br>4. In the database (or `/admin` user list), confirm a new `User` record was created for that email. |

---

### 2. UC-ROOM-06 — Review Room Application *(includes UC-CON-01 — Create Rental Contract)*

| Field | Detail |
|---|---|
| **Use case ID** | UC-ROOM-06 |
| **Use Case Name** | Review Room Application |
| **Description** | A manager approves or rejects a pending room application. Approval atomically creates the student's first rental contract (UC-CON-01), increments room occupancy, and assigns the room to the student. |
| **Actor(s)** | Admin |
| **Preconditions** | A `Booking(PENDING)` exists. |
| **Main Flow** | 1. Manager opens `/admin/bookings` and clicks "Duyệt" on a pending application.<br>2. Backend, inside a MongoDB transaction: increments `Room.currentOccupancy`, sets `Booking.status = APPROVED`, generates a unique `contractNumber` (e.g. `HD-2026-...`), creates a `Contract(ACTIVE)` with `startDate = now`, `endDate = now + 5 months`, `rentalFee = room.price`, and sets `User.room`.<br>3. The student is notified in real time. |
| **Alternative / Exception Flows** | **AF1 — Reject:** `Booking.status = REJECTED`; student notified; no room/contract changes.<br>**AF2 — Room fills concurrently:** the guarded occupancy update fails; the whole transaction aborts and the manager must retry. |
| **Postconditions** | Approved: room occupancy incremented, `Contract(ACTIVE)` created, `User.room` set. Rejected: only the booking is closed. |
| **Special Requirements** | Runs inside a single MongoDB transaction so the booking, room, and contract documents change atomically or not at all. |

#### Test Cases

**Test case 1**

| Field | Detail |
|---|---|
| Test case ID | TC-ROOM-06-01 |
| Test case name | Manager approves a pending booking successfully |
| Description | Verify that approving a `Booking(PENDING)` sets it to `APPROVED`, increments the room's occupancy, creates a new active contract, and assigns the room to the student. |
| Related Use case | UC-ROOM-06 — Review Room Application |
| Input Data | `Booking B-01` (`status = PENDING`) for student `S1` on `Room R-101` (`currentOccupancy = 1`, `capacity = 4`) |
| Expected Output | - HTTP `200 OK`, message "Duyệt đơn thành công".<br>- `Booking B-01.status = APPROVED` in DB.<br>- `Room R-101.currentOccupancy = 2`.<br>- A new `Contract(ACTIVE)` exists, linked to `S1`, `R-101`, and `B-01`.<br>- `User S1.room = R-101`. |
| Test steps | 1. Log in as Admin.<br>2. Open `/admin/bookings`, locate `B-01`.<br>3. Click "Duyệt" and confirm.<br>4. Query the DB (or `/admin/bookings` UI) to check `Booking`, `Room`, `Contract`, and `User` state. |

**Test case 2**

| Field | Detail |
|---|---|
| Test case ID | TC-ROOM-06-02 |
| Test case name | Manager rejects a pending booking |
| Description | Verify that rejecting a `Booking(PENDING)` only changes the booking's status and leaves room occupancy, contracts, and the student's room unchanged. |
| Related Use case | UC-ROOM-06 — Review Room Application (AF1) |
| Input Data | `Booking B-02` (`status = PENDING`) for student `S2` on `Room R-102` (`currentOccupancy = 1`) |
| Expected Output | - HTTP `200 OK`.<br>- `Booking B-02.status = REJECTED`.<br>- `Room R-102.currentOccupancy` remains `1` (unchanged).<br>- No `Contract` created for `S2`.<br>- `User S2.room` remains unset. |
| Test steps | 1. Log in as Admin.<br>2. Open `/admin/bookings`, locate `B-02`.<br>3. Click "Từ chối" and confirm.<br>4. Verify DB state for `Booking`, `Room`, `Contract`, `User`. |

**Test case 3**

| Field | Detail |
|---|---|
| Test case ID | TC-ROOM-06-03 |
| Test case name 3 | Contract fields are generated correctly on approval |
| Description | Verify the auto-created contract has the documented values: unique `contractNumber` format, `startDate = now`, `endDate = startDate + 5 months`, `rentalFee = room.price`. |
| Related Use case | UC-ROOM-06 — Review Room Application → UC-CON-01 — Create Rental Contract |
| Input Data | `Booking B-03` for student `S3` on `Room R-103` with `price = 1,500,000 VND` |
| Expected Output | - `Contract.contractNumber` matches pattern `HD-2026-...` and is unique.<br>- `Contract.startDate` ≈ approval timestamp.<br>- `Contract.endDate` = `startDate + 5 months` (calendar-correct, e.g. accounting for month length).<br>- `Contract.rentalFee = 1,500,000`.<br>- `Contract.status = ACTIVE`. |
| Test steps | 1. Approve `B-03` as Admin.<br>2. Open the resulting contract in `/admin/contracts` or the DB.<br>3. Verify each field listed in Expected Output. |

**Test case 4**

| Field | Detail |
|---|---|
| Test case ID | TC-ROOM-06-04 |
| Test case name | Student receives a real-time notification on approval |
| Description | Verify the student is notified via the socket connection immediately after their booking is approved. |
| Related Use case | UC-ROOM-06 — Review Room Application |
| Input Data | Student `S4` logged in with an active socket connection; a pending booking `B-04` for `S4`. |
| Expected Output | - Within a few seconds of approval, `S4`'s client receives a notification event (visible in `NotificationBell` / socket inspector) about the approved booking. |
| Test steps | 1. Log in as `S4` in one browser tab, keep it open on `/student`.<br>2. In another session, log in as manager and approve `B-04`.<br>3. Observe the notification bell/toast appearing for `S4` without a page refresh. |

**Test case 5**

| Field | Detail |
|---|---|
| Test case ID | TC-ROOM-06-05 |
| Test case name | Student receives a real-time notification on rejection |
| Description | Verify the student is notified via the socket connection immediately after their booking is rejected. |
| Related Use case | UC-ROOM-06 — Review Room Application (AF1) |
| Input Data | Student `S5` logged in with an active socket connection; a pending booking `B-05` for `S5`. |
| Expected Output | - `S5`'s client receives a rejection notification without a page refresh. |
| Test steps | 1. Log in as `S5`, keep the tab open.<br>2. As manager, reject `B-05`.<br>3. Observe the notification appearing for `S5`. |

**Test case 6**

| Field | Detail |
|---|---|
| Test case ID | TC-ROOM-06-06 |
| Test case name | Non-manager role cannot approve/reject a booking |
| Description | Verify that a user without `ADMIN`/`FLOOR_MANAGER` role cannot call the approve/reject endpoint directly. |
| Related Use case | UC-ROOM-06 — Review Room Application |
| Input Data | Student `S6`'s JWT used to call `PATCH /api/bookings/B-06/approve` |
| Expected Output | - HTTP `400 / 409`.<br>- `Booking B-06` remains `PENDING`; no room/contract/user changes. |
| Test steps | 1. Log in as a student and capture the JWT.<br>2. Using Postman/curl, call `PATCH /api/bookings/B-06/approve` with the student's token.<br>3. Confirm `401` and that no state changed. |

**Test case 7**

| Field | Detail |
|---|---|
| Test case ID | TC-ROOM-06-07 |
| Test case name | Approving a booking that is not PENDING is rejected |
| Description | Verify that re-approving an already `APPROVED` or `REJECTED` booking is rejected rather than silently creating a duplicate contract. |
| Related Use case | UC-ROOM-06 — Review Room Application |
| Input Data | `Booking B-07` already in state `APPROVED` |
| Expected Output | - HTTP `400`/`409` error, no second contract created, `Room.currentOccupancy` not incremented a second time. |
| Test steps | 1. Approve `B-07` once (baseline).<br>2. Attempt to approve `B-07` again via `/admin/bookings` or API.<br>3. Verify the error response and that occupancy/contract count did not change further. |

**Test case 8**

| Field | Detail |
|---|---|
| Test case ID | TC-ROOM-06-08 |
| Test case name | Approval fails gracefully when the room is already at full capacity |
| Description | Verify that approving a booking for a room whose `currentOccupancy` has already reached `capacity` (e.g. filled by another approval in between) is rejected and does not create a contract. |
| Related Use case | UC-ROOM-06 — Review Room Application (AF2) |
| Input Data | `Room R-108` with `capacity = 2`, `currentOccupancy = 2`; `Booking B-08(PENDING)` referencing `R-108` |
| Expected Output | - HTTP `400`/`409` error indicating the room is full.<br>- `Booking B-08` remains `PENDING` (or is explicitly marked failed, per implementation).<br>- No `Contract` created; `Room.currentOccupancy` unchanged. |
| Test steps | 1. Set up `R-108` at full capacity (approve other bookings first, or seed directly).<br>2. Attempt to approve `B-08`.<br>3. Verify the error and that no contract/occupancy change occurred. |

**Test case 9**

| Field | Detail |
|---|---|
| Test case ID | TC-ROOM-06-09 |
| Test case name | Concurrent approval of two bookings for the last free slot |
| Description | Verify that when two pending bookings target the same room's last remaining slot and are approved at nearly the same time, only one succeeds and the other's transaction aborts cleanly (no over-booking). |
| Related Use case | UC-ROOM-06 — Review Room Application (AF2) |
| Input Data | `Room R-109` with `capacity = 3`, `currentOccupancy = 2` (1 slot left); `Booking B-09a` and `B-09b` (both `PENDING`) for different students on `R-109` |
| Expected Output | - Exactly one of `B-09a`/`B-09b` ends as `APPROVED` with a contract created; the other fails with an error and remains `PENDING` (or is retried).<br>- `Room R-109.currentOccupancy` ends at exactly `3`, never `4`. |
| Test steps | 1. Fire the approve requests for `B-09a` and `B-09b` concurrently (e.g. two Postman requests sent back-to-back, or a small script).<br>2. Inspect both responses.<br>3. Verify final `Room.currentOccupancy` and that only one contract was created. |

**Test case 10**

| Field | Detail |
|---|---|
| Test case ID | TC-ROOM-06-10 |
| Test case name | Reject with an admin note is recorded and does not create side effects |
| Description | Verify that rejecting with an optional note stores the note and still results in zero room/contract/user changes. |
| Related Use case | UC-ROOM-06 — Review Room Application (AF1) |
| Input Data | `Booking B-10(PENDING)`; rejection note: "Phòng ưu tiên cho sinh viên năm nhất" |
| Expected Output | - `Booking B-10.status = REJECTED`, `adminNote` (if supported) stores the given text.<br>- No room/contract/user changes. |
| Test steps | 1. As manager, reject `B-10` and enter the note text.<br>2. Confirm.<br>3. Verify the stored note and unchanged room/contract/user state. |

**Test case 11**

| Field | Detail |
|---|---|
| Test case ID | TC-ROOM-06-11 |
| Test case name | Bookings list UI reflects the new status immediately after review |
| Description | Verify that after approving/rejecting, the `/admin/bookings` table updates the row's status badge without requiring a manual page reload. |
| Related Use case | UC-ROOM-06 — Review Room Application |
| Input Data | `Booking B-11(PENDING)` visible in `/admin/bookings` |
| Expected Output | - After confirming approval/rejection, the row's status badge changes to `APPROVED`/`REJECTED` in place. |
| Test steps | 1. Open `/admin/bookings` and locate `B-11`.<br>2. Click "Duyệt" (or "Từ chối") and confirm.<br>3. Observe the row's badge update without a manual refresh. |

---

### 3. UC-ROOM-07 — Run Automatic Room Allocation

| Field | Detail |
|---|---|
| **Use case ID** | UC-ROOM-07 |
| **Use Case Name** | Run Automatic Room Allocation |
| **Description** | A manager bulk-assigns every student who currently has no room into available rooms, matching gender where the room type requires it. |
| **Actor(s)** | Admin |
| **Preconditions** | At least one student has no room; actor holds `ADMIN`. |
| **Main Flow** | 1. Manager opens `/admin/auto-assign`; `GET /api/assignments/preview` shows counts of unassigned students and free slots.<br>2. Manager confirms "Chạy phân phòng tự động"; `POST /api/assignments/auto` runs.<br>3. For each unassigned student (processed in name order), the system picks the first room whose `genderType` is `MIXED` or matches the student's `gender` and that has remaining capacity; it increments occupancy (guarded), creates a `Booking(APPROVED)` + `Contract`, sets `User.room`, and notifies the student.<br>4. A per-student results table is returned (assigned room, or skipped with a reason). |
| **Alternative / Exception Flows** | **AF1 — No compatible room:** student listed `SKIPPED` with a reason.<br>**AF2 — A room fills mid-run from a concurrent process:** that assignment is skipped; the run continues with the next student.<br>**AF3 — Zero unassigned students or zero free slots:** the run button is disabled. |
| **Postconditions** | Zero or more students newly assigned rooms with bookings/contracts. |
| **Special Requirements** | Student preferences are **not** implemented — only gender-matching and capacity are considered. In the current system, room `genderType` is effectively always `MIXED` (no UI yet exists to set it), so gender-matching does not currently constrain results in practice. |

#### Test Cases

**Test case 1**

| Field | Detail |
|---|---|
| Test case ID | TC-ROOM-07-01 |
| Test case name | Preview shows correct unassigned-student and free-slot counts |
| Description | Verify `GET /api/assignments/preview` reports the accurate number of students without a room and the accurate number of free slots across all rooms before running the allocation. |
| Related Use case | UC-ROOM-07 — Run Automatic Room Allocation |
| Input Data | Seeded data: 5 students with `room = null`; total free capacity across all rooms = 8 |
| Expected Output | - `GET /api/assignments/preview` returns `unassignedCount = 5`, `freeSlotCount = 8` (values match seeded data exactly). |
| Test steps | 1. Seed 5 unassigned students and confirm total free capacity is 8 (via DB or `/admin/rooms`).<br>2. Open `/admin/auto-assign`.<br>3. Verify the displayed preview numbers match. |

**Test case 2**

| Field | Detail |
|---|---|
| Test case ID | TC-ROOM-07-02 |
| Test case name | Auto-assignment succeeds for all students when enough free slots exist |
| Description | Verify that running the allocation with more free slots than unassigned students results in every student receiving a room, booking, and contract. |
| Related Use case | UC-ROOM-07 — Run Automatic Room Allocation |
| Input Data | 3 unassigned students; 5 free slots spread across 2 rooms (both `genderType = MIXED`) |
| Expected Output | - All 3 students appear in the result table as assigned to a room.<br>- Each has a new `Booking(APPROVED)` and `Contract(ACTIVE)`.<br>- `User.room` set for all 3.<br>- Total room occupancy increased by exactly 3 across the affected rooms. |
| Test steps | 1. As manager, open `/admin/auto-assign`.<br>2. Click "Chạy phân phòng tự động" and confirm.<br>3. Review the results table.<br>4. Verify DB state for `Booking`, `Contract`, `Room.currentOccupancy`, `User.room` for all 3 students. |

**Test case 3**

| Field | Detail |
|---|---|
| Test case ID | TC-ROOM-07-03 |
| Test case name | Student is skipped when no compatible room exists |
| Description | Verify that a student is listed `SKIPPED` with a reason when no room has both remaining capacity and a compatible `genderType`. |
| Related Use case | UC-ROOM-07 — Run Automatic Room Allocation (AF1) |
| Input Data | 1 unassigned female student; all rooms are `genderType = MALE` and full or non-`MIXED` and unavailable for her |
| Expected Output | - Result table lists the student as `SKIPPED` with a reason such as "Không có phòng phù hợp".<br>- No `Booking`/`Contract` created for that student; `User.room` remains unset. |
| Test steps | 1. Seed the scenario described in Input Data.<br>2. Run the auto-assignment.<br>3. Verify the student appears as `SKIPPED` with a reason, and no side effects occurred for her. |

**Test case 4**

| Field | Detail |
|---|---|
| Test case ID | TC-ROOM-07-04 |
| Test case name | Run button disabled when there are zero unassigned students |
| Description | Verify the UI disables the run action when the preview reports no unassigned students, per AF3. |
| Related Use case | UC-ROOM-07 — Run Automatic Room Allocation (AF3) |
| Input Data | Seeded data where every student already has a room (`unassignedCount = 0`) |
| Expected Output | - "Chạy phân phòng tự động" button is disabled/greyed out.<br>- If `POST /api/assignments/auto` is still called directly via API, it returns an empty result / no-op rather than an error. |
| Test steps | 1. Seed data so no student is unassigned.<br>2. Open `/admin/auto-assign` and check the button state.<br>3. (Optional) Call the API directly and confirm it is a safe no-op. |

**Test case 5**

| Field | Detail |
|---|---|
| Test case ID | TC-ROOM-07-05 |
| Test case name | Run button disabled when there are zero free slots |
| Description | Verify the UI disables the run action when all rooms are at full capacity, even if unassigned students exist. |
| Related Use case | UC-ROOM-07 — Run Automatic Room Allocation (AF3) |
| Input Data | 2 unassigned students; all rooms at `currentOccupancy = capacity` |
| Expected Output | - "Chạy phân phòng tự động" button is disabled.<br>- If called directly via API, no student is assigned and each is reported `SKIPPED`. |
| Test steps | 1. Seed the scenario in Input Data.<br>2. Open `/admin/auto-assign` and check the button state.<br>3. (Optional) Call the API directly and confirm both students are skipped, not erroring out. |

**Test case 6**

| Field | Detail |
|---|---|
| Test case ID | TC-ROOM-07-06 |
| Test case name | Occupancy guard prevents overbooking when a room fills mid-run |
| Description | Verify that if a room's last slot is taken by a concurrent manual approval (UC-ROOM-06) while the auto-assignment run is in progress, the affected student in the auto-assignment run is skipped instead of over-filling the room. |
| Related Use case | UC-ROOM-07 — Run Automatic Room Allocation (AF2) |
| Input Data | `Room R-201` with exactly 1 free slot; auto-assignment run about to assign `R-201` to student `S-A`; concurrently, manager approves a separate `Booking` for student `S-B` into `R-201`. |
| Expected Output | - `Room R-201.currentOccupancy` ends at `capacity`, never exceeds it.<br>- Exactly one of `S-A`/`S-B` ends up assigned to `R-201`; the other is skipped/fails and must be handled in a later run. |
| Test steps | 1. Set up `R-201` with 1 free slot.<br>2. Trigger the auto-assignment run and, at nearly the same moment, approve a manual booking targeting `R-201` for a different student.<br>3. Verify final occupancy and that only one of the two students ended up in `R-201`. |

**Test case 7**

| Field | Detail |
|---|---|
| Test case ID | TC-ROOM-07-07 |
| Test case name | Result table lists the assigned room per student correctly |
| Description | Verify each row of the returned results table correctly names the room a student was assigned to (not a generic "assigned" flag only). |
| Related Use case | UC-ROOM-07 — Run Automatic Room Allocation |
| Input Data | 2 unassigned students, 2 target rooms with free capacity |
| Expected Output | - Each successful row shows the specific room number/name the student was assigned to, matching the `Contract.room`/`Booking.room` created for that student. |
| Test steps | 1. Run the auto-assignment for the 2 students.<br>2. Compare the room shown in the UI results table for each student against the room recorded in their new `Contract`/`Booking` in the DB. |

**Test case 8**

| Field | Detail |
|---|---|
| Test case ID | TC-ROOM-07-08 |
| Test case name | Non-manager role cannot trigger automatic allocation |
| Description | Verify a user without `ADMIN` role cannot call `POST /api/assignments/auto` directly. |
| Related Use case | UC-ROOM-07 — Run Automatic Room Allocation |
| Input Data | Student's JWT used to call `POST /api/assignments/auto` |
| Expected Output | - HTTP `403 Forbidden`.<br>- No students assigned; no state changes. |
| Test steps | 1. Log in as a student and capture the JWT.<br>2. Call `POST /api/assignments/auto` via Postman with the student's token.<br>3. Confirm `403` and no side effects. |

**Test case 9**

| Field | Detail |
|---|---|
| Test case ID | TC-ROOM-07-09 |
| Test case name | Students are processed in name order |
| Description | Verify that when free slots are more limited than unassigned students, the students who get priority match the documented name-order processing rule. |
| Related Use case | UC-ROOM-07 — Run Automatic Room Allocation |
| Input Data | 3 unassigned students named "Bình", "An", "Chi" (only 2 free slots available in total) |
| Expected Output | - Students are considered in alphabetical name order ("An", "Bình", "Chi"); "An" and "Bình" are assigned, "Chi" is `SKIPPED` due to no remaining capacity (assuming rooms fill in that order). |
| Test steps | 1. Seed the 3 students and constrain free slots to 2.<br>2. Run the auto-assignment.<br>3. Verify which 2 students were assigned and which was skipped, matching name-order priority. |

**Test case 10**

| Field | Detail |
|---|---|
| Test case ID | TC-ROOM-07-10 |
| Test case name | Newly assigned students receive a real-time notification |
| Description | Verify each student who is successfully auto-assigned a room receives a real-time notification, same as manual approval. |
| Related Use case | UC-ROOM-07 — Run Automatic Room Allocation |
| Input Data | 1 unassigned student `S-N` logged in with an active socket connection; sufficient free capacity |
| Expected Output | - `S-N` receives a notification about their new room assignment shortly after the run completes, without a page refresh. |
| Test steps | 1. Log in as `S-N` and keep the session open on `/student`.<br>2. As manager, run the auto-assignment (with `S-N` unassigned beforehand).<br>3. Observe the notification appearing for `S-N`. |

**Test case 11**

| Field | Detail |
|---|---|
| Test case ID | TC-ROOM-07-11 |
| Test case name | Mixed-gender rooms accept students of any gender (current MIXED-only behavior) |
| Description | Verify that, consistent with the documented current limitation (no UI yet to set room gender, so rooms behave as `MIXED`), both male and female unassigned students can be placed in the same room by one run. |
| Related Use case | UC-ROOM-07 — Run Automatic Room Allocation — Special Requirements |
| Input Data | 1 male and 1 female unassigned student; 1 room (`genderType = MIXED`) with 2 free slots |
| Expected Output | - Both students are assigned to the same room in the same run; no gender-mismatch error is raised. |
| Test steps | 1. Seed the scenario in Input Data.<br>2. Run the auto-assignment.<br>3. Verify both students were assigned to the same room. |

---

### 4. UC-FIN-02 — Create or Bulk-Generate Invoices

| Field | Detail |
|---|---|
| **Use case ID** | UC-FIN-02 |
| **Use Case Name** | Create or Bulk-Generate Invoices |
| **Description** | A manager creates a single invoice, or bulk-generates invoices for many rooms at once, computing `totalAmount` from room rent plus electricity/water fees. |
| **Actor(s)** | Admin |
| **Preconditions** | Actor holds `ADMIN`. |
| **Main Flow** | 1. Manager opens `/admin/invoices` and starts bulk invoice generation for a billing period (month/year).<br>2. For each room, the manager enters `electricityKwh` and `waterM3` (or pre-computed fees) alongside a unit price.<br>3. `POST /api/invoices/generate-bulk` (or `POST /api/invoices` for a single room) computes each room's electricity/water fee and creates one `Invoice(PENDING)` per room, with `totalAmount = rentalFee + electricityFee + waterFee`. |
| **Alternative / Exception Flows** | **AF1 — Single-invoice path:** manager enters pre-computed `electricityFee`/`waterFee` directly via `POST /api/invoices` instead of raw kWh/m³ readings.<br>**AF2 — Duplicate invoice for the same room/period:** rejected by the unique index on `(room, month, year)`. |
| **Postconditions** | One or more `Invoice(PENDING)` documents created with `totalAmount` computed from room/electricity/water fees. |
| **Special Requirements** | The unique index `(room, month, year)` is the authoritative guard against duplicate billing for the same period, even under concurrent bulk-generation calls. Raw meter readings are not persisted as their own record — they are transient input to invoice generation. |

#### Test Cases

**Test case 1**

| Field | Detail |
|---|---|
| Test case ID | TC-FIN-02-01 |
| Test case name | Bulk-generate invoices for a billing period across multiple rooms |
| Description | Verify that bulk generation with valid kWh/m³ readings and unit prices creates one correctly computed invoice per selected room. |
| Related Use case | UC-FIN-02 — Create or Bulk-Generate Invoices |
| Input Data | Month/Year: 09/2026; Rooms: `R-101` (rent 1,500,000, 40 kWh @ 3,500đ, 5 m³ @ 20,000đ), `R-102` (rent 1,500,000, 25 kWh @ 3,500đ, 3 m³ @ 20,000đ) |
| Expected Output | - HTTP `201 Created`.<br>- `Invoice` for `R-101`: `electricityFee = 140,000`, `waterFee = 100,000`, `totalAmount = 1,740,000`, `status = PENDING`.<br>- `Invoice` for `R-102`: `electricityFee = 87,500`, `waterFee = 60,000`, `totalAmount = 1,647,500`, `status = PENDING`. |
| Test steps | 1. As manager, open `/admin/invoices` → "Tạo hóa đơn hàng loạt".<br>2. Select period 09/2026, enter the readings/prices above for `R-101` and `R-102`.<br>3. Submit.<br>4. Verify both invoices in the list/DB match the expected computed amounts. |

**Test case 2**

| Field | Detail |
|---|---|
| Test case ID | TC-FIN-02-02 |
| Test case name | Create a single invoice with pre-computed fees |
| Description | Verify `POST /api/invoices` (single-invoice path, AF1) accepts pre-computed `electricityFee`/`waterFee` directly rather than raw kWh/m³. |
| Related Use case | UC-FIN-02 — Create or Bulk-Generate Invoices (AF1) |
| Input Data | Room `R-103`, month/year 09/2026, `rentalFee = 1,500,000`, `electricityFee = 150,000`, `waterFee = 80,000` |
| Expected Output | - HTTP `201 Created`.<br>- `Invoice.totalAmount = 1,730,000`, `status = PENDING`. |
| Test steps | 1. Call `POST /api/invoices` (or use the single-invoice form) with the data above.<br>2. Verify the created invoice's `totalAmount` and `status`. |

**Test case 3**

| Field | Detail |
|---|---|
| Test case ID | TC-FIN-02-03 |
| Test case name | Duplicate invoice for the same room/month/year is rejected |
| Description | Verify the `(room, month, year)` unique index prevents creating a second invoice for a room/period that already has one. |
| Related Use case | UC-FIN-02 — Create or Bulk-Generate Invoices (AF2) |
| Input Data | `Room R-101` already has an `Invoice` for 09/2026 (from TC-FIN-02-01); attempt to create another one for the same room/period |
| Expected Output | - HTTP `409 Conflict` (or `400`, per implementation), error message indicating a duplicate invoice.<br>- No second invoice document created. |
| Test steps | 1. Ensure `R-101` already has an invoice for 09/2026 (run TC-FIN-02-01 first).<br>2. Attempt to create another invoice for `R-101` / 09/2026.<br>3. Verify the rejection and that only one invoice exists for that room/period. |

**Test case 4**

| Field | Detail |
|---|---|
| Test case ID | TC-FIN-02-04 |
| Test case name | Bulk-generate rejects negative consumption values |
| Description | Verify that a negative `electricityKwh` or `waterM3` value is rejected by validation instead of producing a negative fee. |
| Related Use case | UC-FIN-02 — Create or Bulk-Generate Invoices |
| Input Data | Room `R-104`, `electricityKwh = -10`, `waterM3 = 5`, valid unit prices |
| Expected Output | - HTTP `400 Bad Request` with a validation error on the negative field.<br>- No invoice created for `R-104`. |
| Test steps | 1. Attempt bulk generation for `R-104` with a negative `electricityKwh`.<br>2. Verify the `400` response and that no invoice was created. |

**Test case 5**

| Field | Detail |
|---|---|
| Test case ID | TC-FIN-02-05 |
| Test case name | Bulk-generate skips or errors for a room with no active occupant/contract |
| Description | Verify that generating an invoice for a room with no current student/active contract is either excluded automatically or returns a clear validation error, rather than creating a bill with no owner. |
| Related Use case | UC-FIN-02 — Create or Bulk-Generate Invoices |
| Input Data | Room `R-105` with `currentOccupancy = 0` (empty room), included in the bulk-generation room list |
| Expected Output | - `R-105` is either skipped in the results (with a reason) or the request for it returns a validation error; in either case, no `Invoice` is created for an unoccupied room. |
| Test steps | 1. Include an empty room (`R-105`) in the bulk-generation request.<br>2. Submit.<br>3. Verify no invoice was created for `R-105` and that the response communicates this clearly. |

**Test case 6**

| Field | Detail |
|---|---|
| Test case ID | TC-FIN-02-06 |
| Test case name | Non-manager role cannot generate invoices |
| Description | Verify a student cannot call the invoice-creation endpoints directly. |
| Related Use case | UC-FIN-02 — Create or Bulk-Generate Invoices |
| Input Data | Student's JWT used to call `POST /api/invoices/generate-bulk` |
| Expected Output | - HTTP `403 Forbidden`.<br>- No invoices created. |
| Test steps | 1. Log in as a student and capture the JWT.<br>2. Call `POST /api/invoices/generate-bulk` via Postman with the student's token.<br>3. Confirm `403` and that no invoices were created. |

**Test case 7**

| Field | Detail |
|---|---|
| Test case ID | TC-FIN-02-07 |
| Test case name | New invoices default to status PENDING |
| Description | Verify every newly created invoice (single or bulk) starts as `PENDING`, never `PAID` or `OVERDUE`, regardless of creation path. |
| Related Use case | UC-FIN-02 — Create or Bulk-Generate Invoices |
| Input Data | A freshly created invoice for `Room R-106`, 09/2026 |
| Expected Output | - `Invoice.status = PENDING` immediately after creation. |
| Test steps | 1. Create an invoice for `R-106` via either the single or bulk path.<br>2. Inspect the created invoice's `status` field. |

**Test case 8**

| Field | Detail |
|---|---|
| Test case ID | TC-FIN-02-08 |
| Test case name | totalAmount correctly sums rent, electricity, and water fees |
| Description | Verify the `totalAmount` calculation is exactly `rentalFee + electricityFee + waterFee` with no rounding or off-by-one errors, using non-round numbers. |
| Related Use case | UC-FIN-02 — Create or Bulk-Generate Invoices |
| Input Data | Room `R-107`, `rentalFee = 1,350,000`, `electricityKwh = 37`, unit price `3,750đ/kWh` (→ fee `138,750`), `waterM3 = 4.5`, unit price `18,000đ/m³` (→ fee `81,000`) |
| Expected Output | - `Invoice.totalAmount = 1,350,000 + 138,750 + 81,000 = 1,569,750`, exactly. |
| Test steps | 1. Generate the invoice for `R-107` with the input data above.<br>2. Verify each fee component and the final `totalAmount` in the DB/UI match the arithmetic exactly. |

**Test case 9**

| Field | Detail |
|---|---|
| Test case ID | TC-FIN-02-09 |
| Test case name | Bulk request across many rooms creates exactly one invoice per room |
| Description | Verify submitting a bulk-generation request for N rooms in a single call creates exactly N invoices, no more, no fewer, with no cross-room data mixing. |
| Related Use case | UC-FIN-02 — Create or Bulk-Generate Invoices |
| Input Data | 5 rooms (`R-201`…`R-205`), each with distinct rent/kWh/m³ values, submitted in one bulk request for 10/2026 |
| Expected Output | - Exactly 5 new `Invoice` documents created, one per room, for 10/2026.<br>- Each invoice's `totalAmount` matches its own room's inputs (no swapped values between rooms). |
| Test steps | 1. Submit the bulk request for `R-201`…`R-205` with distinct values for 10/2026.<br>2. Count the resulting invoices and verify each one's amount corresponds to the correct room. |

**Test case 10**

| Field | Detail |
|---|---|
| Test case ID | TC-FIN-02-10 |
| Test case name | Missing unit price parameter is rejected |
| Description | Verify that omitting the electricity or water unit price in a bulk-generation request produces a validation error rather than a `NaN`/`0` fee. |
| Related Use case | UC-FIN-02 — Create or Bulk-Generate Invoices |
| Input Data | Room `R-108`, `electricityKwh = 30`, `waterM3 = 4`, electricity unit price omitted |
| Expected Output | - HTTP `400 Bad Request` citing the missing unit price.<br>- No invoice created for `R-108`. |
| Test steps | 1. Submit the bulk-generation request for `R-108` without an electricity unit price.<br>2. Verify the `400` response and that no invoice was created. |

**Test case 11**

| Field | Detail |
|---|---|
| Test case ID | TC-FIN-02-11 |
| Test case name | Newly generated invoice is visible to the student |
| Description | Verify that after invoice generation, the affected student can immediately see the new invoice on `/student/invoices`. |
| Related Use case | UC-FIN-02 — Create or Bulk-Generate Invoices → (feeds UC-FIN-08 — View Invoices) |
| Input Data | Student `S-Inv` occupying `Room R-109`; a new invoice generated for `R-109` / 09/2026 |
| Expected Output | - `GET /api/invoices` (student-scoped) / `/student/invoices` page shows the new invoice with the correct `totalAmount` and `status = PENDING`. |
| Test steps | 1. Generate an invoice for `R-109`.<br>2. Log in as `S-Inv` and open `/student/invoices`.<br>3. Verify the new invoice appears with the correct amount and status. |

---

### 5. UC-CHK-04 — Refund Deposit and Complete Checkout *(includes UC-CON-03 — Liquidate Rental Contract)*

| Field | Detail |
|---|---|
| **Use case ID** | UC-CHK-04 |
| **Use Case Name** | Refund Deposit and Complete Checkout |
| **Description** | The manager finalizes a checkout: the damage list and deposit from the review step are persisted, compensation and refund are computed, the contract is terminated, and the room slot is released — all atomically. |
| **Actor(s)** | Admin |
| **Preconditions** | A `Checkout(PENDING)` exists with the damage list (if any) already prepared during review. |
| **Main Flow** | 1. Manager reviews the computed refund amount in a confirmation dialog and confirms.<br>2. Backend, in a transaction: persists the damage-item list and final deposit amount on the `Checkout`, computes `compensation = sum(item fees)` and `refund = max(0, deposit − compensation)`, sets `Checkout.status = COMPLETED`, terminates the linked `Contract` (`status = TERMINATED`/`LIQUIDATED`, per UC-CON-03), decrements `Room.currentOccupancy`, and clears `User.room`.<br>3. Student is notified in real time with the final refund amount. |
| **Alternative / Exception Flows** | **AF1 — Reject instead of completing:** `PATCH /api/checkouts/:id/reject`, optionally with an `adminNote`; `Checkout.status = REJECTED`; student notified; no contract/room changes.<br>**AF2 — No damage found:** manager adds zero items; compensation is `0` and the full deposit is refunded. |
| **Postconditions** | Either `Checkout(REJECTED)` with no side effects, or `Checkout(COMPLETED)` with the contract terminated, the room slot released, and a computed refund recorded. |
| **Special Requirements** | Runs inside a single transaction so `Checkout`, `Contract`, `Room`, and `User` all update atomically or not at all. The deposit amount is a convention (one month's rent by default, adjustable by the manager) rather than a separately collected deposit. |

#### Test Cases

**Test case 1**

| Field | Detail |
|---|---|
| Test case ID | TC-CHK-04-01 |
| Test case name | Complete checkout with zero damage items refunds the full deposit |
| Description | Verify that finalizing a checkout with no recorded damage items refunds the entire deposit and correctly terminates the contract and releases the room. |
| Related Use case | UC-CHK-04 — Refund Deposit and Complete Checkout (AF2) |
| Input Data | `Checkout C-01(PENDING)` for student `S-C1` on `Room R-301`, `deposit = 1,500,000`, `damageItems = []` |
| Expected Output | - HTTP `200 OK`.<br>- `Checkout C-01.status = COMPLETED`, `compensation = 0`, `refund = 1,500,000`.<br>- Linked `Contract.status = TERMINATED`.<br>- `Room R-301.currentOccupancy` decremented by 1.<br>- `User S-C1.room` cleared (null). |
| Test steps | 1. As manager, open `/admin/checkouts`, select `C-01`, add zero damage items.<br>2. Confirm completion.<br>3. Verify `Checkout`, `Contract`, `Room`, and `User` state in the DB/UI. |

**Test case 2**

| Field | Detail |
|---|---|
| Test case ID | TC-CHK-04-02 |
| Test case name | Complete checkout with damage items deducts compensation from the refund |
| Description | Verify that recorded damage items correctly reduce the refund by their total fee. |
| Related Use case | UC-CHK-04 — Refund Deposit and Complete Checkout |
| Input Data | `Checkout C-02(PENDING)`, `deposit = 1,500,000`, damage items: "Vỡ cửa kính" (300,000đ), "Mất chìa khóa" (100,000đ) |
| Expected Output | - `compensation = 400,000`.<br>- `refund = 1,500,000 − 400,000 = 1,100,000`.<br>- `Checkout.status = COMPLETED`. |
| Test steps | 1. As manager, review `C-02`, add the two damage items above.<br>2. Confirm the live-computed refund matches `1,100,000` before submitting.<br>3. Complete the checkout and verify the persisted values match. |

**Test case 3**

| Field | Detail |
|---|---|
| Test case ID | TC-CHK-04-03 |
| Test case name | Compensation exceeding deposit floors the refund at zero |
| Description | Verify that when total damage compensation exceeds the deposit, the refund is `0`, never a negative number. |
| Related Use case | UC-CHK-04 — Refund Deposit and Complete Checkout |
| Input Data | `Checkout C-03(PENDING)`, `deposit = 1,000,000`, damage items totaling `1,400,000` |
| Expected Output | - `compensation = 1,400,000`.<br>- `refund = max(0, 1,000,000 − 1,400,000) = 0`.<br>- `Checkout.status = COMPLETED`. |
| Test steps | 1. As manager, review `C-03`, add damage items summing to `1,400,000` against a `1,000,000` deposit.<br>2. Confirm the UI shows `refund = 0` (not negative) before submitting.<br>3. Complete and verify the persisted `refund = 0`. |

**Test case 4**

| Field | Detail |
|---|---|
| Test case ID | TC-CHK-04-04 |
| Test case name | Completing checkout terminates the linked contract |
| Description | Verify that finalizing a checkout changes the associated `Contract.status` to `TERMINATED`/`LIQUIDATED` (UC-CON-03), and does not affect any other student's contract. |
| Related Use case | UC-CHK-04 — Refund Deposit and Complete Checkout → UC-CON-03 — Liquidate Rental Contract |
| Input Data | `Checkout C-04(PENDING)` linked to `Contract CT-04(ACTIVE)` for student `S-C4`; another unrelated active contract `CT-99` exists for a different student. |
| Expected Output | - `Contract CT-04.status = TERMINATED` after completion.<br>- `Contract CT-99.status` remains `ACTIVE` (unaffected). |
| Test steps | 1. Complete checkout `C-04`.<br>2. Verify `CT-04.status = TERMINATED` and `CT-99.status` is unchanged. |

**Test case 5**

| Field | Detail |
|---|---|
| Test case ID | TC-CHK-04-05 |
| Test case name | Room occupancy is decremented and the student's room reference is cleared |
| Description | Verify the room slot is released and the student's `room` field is cleared, freeing the room for a future UC-ROOM-06/UC-ROOM-07 assignment. |
| Related Use case | UC-CHK-04 — Refund Deposit and Complete Checkout |
| Input Data | `Room R-305` with `currentOccupancy = 3` before completing `Checkout C-05` for student `S-C5` who occupies `R-305` |
| Expected Output | - `Room R-305.currentOccupancy = 2` after completion.<br>- `User S-C5.room = null`. |
| Test steps | 1. Note `R-305.currentOccupancy` before completing `C-05`.<br>2. Complete `C-05`.<br>3. Verify occupancy decreased by exactly 1 and `S-C5.room` is now null. |

**Test case 6**

| Field | Detail |
|---|---|
| Test case ID | TC-CHK-04-06 |
| Test case name | Student receives a real-time notification with the final refund amount |
| Description | Verify the student is notified in real time upon checkout completion, including the computed refund amount. |
| Related Use case | UC-CHK-04 — Refund Deposit and Complete Checkout |
| Input Data | Student `S-C6` logged in with an active socket connection; `Checkout C-06(PENDING)` for `S-C6` |
| Expected Output | - `S-C6` receives a notification referencing the checkout completion and refund amount, without a page refresh. |
| Test steps | 1. Log in as `S-C6`, keep the tab open.<br>2. As manager, complete `C-06`.<br>3. Observe the notification appearing for `S-C6` with the correct refund figure. |

**Test case 7**

| Field | Detail |
|---|---|
| Test case ID | TC-CHK-04-07 |
| Test case name | Completing a non-PENDING checkout is rejected |
| Description | Verify that a checkout already `COMPLETED` or `REJECTED` cannot be completed a second time. |
| Related Use case | UC-CHK-04 — Refund Deposit and Complete Checkout |
| Input Data | `Checkout C-07` already in state `COMPLETED` |
| Expected Output | - HTTP `400`/`409` error; no second decrement of room occupancy, no double refund recorded. |
| Test steps | 1. Complete `C-07` once (baseline).<br>2. Attempt to complete it again.<br>3. Verify the error and that room/contract state did not change further. |

**Test case 8**

| Field | Detail |
|---|---|
| Test case ID | TC-CHK-04-08 |
| Test case name | Non-manager role cannot finalize a checkout |
| Description | Verify a student cannot call the checkout-completion endpoint for their own or another checkout. |
| Related Use case | UC-CHK-04 — Refund Deposit and Complete Checkout |
| Input Data | Student's JWT used to call the checkout-completion endpoint for `Checkout C-08` |
| Expected Output | - HTTP `403 Forbidden`.<br>- `Checkout C-08` remains `PENDING`; no room/contract changes. |
| Test steps | 1. Log in as a student and capture the JWT.<br>2. Call the checkout-completion endpoint via Postman with the student's token.<br>3. Confirm `403` and that no state changed. |

**Test case 9**

| Field | Detail |
|---|---|
| Test case ID | TC-CHK-04-09 |
| Test case name | Adjusted deposit amount is used in the refund calculation |
| Description | Verify that when the manager adjusts the deposit away from the one-month-rent default (per UC-CHK-02), the adjusted value — not the default — is used to compute the refund. |
| Related Use case | UC-CHK-04 — Refund Deposit and Complete Checkout |
| Input Data | `Checkout C-09`, default deposit `1,200,000` (one month's rent), manager adjusts it to `1,000,000`; no damage items |
| Expected Output | - `Checkout.deposit = 1,000,000` (adjusted value persisted).<br>- `refund = 1,000,000` (not the original `1,200,000`). |
| Test steps | 1. As manager, review `C-09` and change the deposit field from `1,200,000` to `1,000,000`.<br>2. Complete the checkout.<br>3. Verify the persisted deposit and refund both reflect `1,000,000`. |

**Test case 10**

| Field | Detail |
|---|---|
| Test case ID | TC-CHK-04-10 |
| Test case name | Rejecting a checkout instead of completing it makes no contract/room changes |
| Description | Verify AF1: rejecting a pending checkout only changes its status, leaving the contract active and the room occupied. |
| Related Use case | UC-CHK-04 — Refund Deposit and Complete Checkout (AF1) |
| Input Data | `Checkout C-10(PENDING)` for student `S-C10` on `Room R-310`; rejection note "Không đủ điều kiện trả phòng" |
| Expected Output | - `Checkout C-10.status = REJECTED`, `adminNote` stored.<br>- Linked `Contract.status` remains `ACTIVE`.<br>- `Room R-310.currentOccupancy` unchanged.<br>- `User S-C10.room` unchanged. |
| Test steps | 1. As manager, open `C-10` and choose "Từ chối" with the note above.<br>2. Confirm.<br>3. Verify `Checkout`, `Contract`, `Room`, `User` state is unchanged apart from the checkout's own status/note. |

**Test case 11**

| Field | Detail |
|---|---|
| Test case ID | TC-CHK-04-11 |
| Test case name | Transaction atomicity — a mid-transaction failure leaves no partial state |
| Description | Verify that if the completion transaction fails partway (e.g. a simulated DB error while terminating the contract), none of `Checkout`, `Contract`, `Room`, or `User` end up partially updated — the whole operation rolls back. |
| Related Use case | UC-CHK-04 — Refund Deposit and Complete Checkout — Special Requirements |
| Input Data | `Checkout C-11(PENDING)`; a fault is injected (e.g. temporarily dropping the DB connection, or a test hook that forces an error) during the contract-termination step of the transaction. |
| Expected Output | - The completion call returns an error (HTTP `500`/`503`, per implementation).<br>- `Checkout C-11.status` remains `PENDING` (not partially `COMPLETED`).<br>- `Contract.status` remains `ACTIVE`.<br>- `Room.currentOccupancy` and `User.room` remain unchanged. |
| Test steps | 1. Set up the fault injection for the contract-termination step (dev/test environment only).<br>2. Attempt to complete `C-11`.<br>3. Verify the error response and that `Checkout`, `Contract`, `Room`, and `User` all remain in their pre-transaction state. |

---

## D. Automated Test Code

*Performed by: [name] · Reviewed by: [name] · Edited by: [name]*

To make the 55 test cases above repeatable instead of run by hand every time, the team wrote a small automated test suite in plain Node.js, located in `e2e-tests/` at the root of `Domitory_Management_Backend`. It exercises the real backend API and MongoDB directly — no additional package needs to be installed, since it reuses the `mongodb` driver that already ships as a dependency of Mongoose, and Node's built-in `fetch` (Node ≥ 18).

### 1. What the code covers

| File | Use case | Test cases automated | Test cases left as MANUAL |
|---|---|---|---|
| `uc-auth-02.js` | UC-AUTH-02 — Log In | 10 | 1 (Google OAuth — needs a real Google ID token) |
| `uc-room-06.js` | UC-ROOM-06 — Review Room Application | 10 | 1 (UI badge updates instantly, no page reload) |
| `uc-room-07.js` | UC-ROOM-07 — Run Automatic Room Allocation | 9 | 2 (run button disabled — depends on the *global* state of the whole system, not something the script should force by mutating other students'/rooms' real data) |
| `uc-fin-02.js` | UC-FIN-02 — Create or Bulk-Generate Invoices | 11 | 0 |
| `uc-chk-04.js` | UC-CHK-04 — Refund Deposit and Complete Checkout | 10 | 1 (mid-transaction fault injection — cannot be safely simulated from an external script without a dedicated MongoDB fail point) |
| `helpers.js` | shared utilities used by all 5 files above | — | — |
| `run-all.js` | runs all 5 suites back-to-back and prints one consolidated report | — | — |

A case marked `MANUAL` still prints a clear instruction for how to verify it by hand (which screen to open, what to click, what to look for) — it is not skipped silently.

### 2. How each test is structured

Every test case in every file follows the same three steps, mirroring the "Input Data / Test steps / Expected Output" columns of the test case tables above:

1. **Arrange** — create the exact data the test case needs (a fresh room via `POST /rooms`, a fresh student via `POST /auth/register`, a booking, a checkout, etc.), using **new, randomly-named records every run** (via a `rnd()` helper) so re-running the suite never collides with data from a previous run or with data you are editing by hand on `/admin` at the same time.
2. **Act** — call the real API endpoint(s) under test with `fetch`, exactly as the frontend would (same routes, same JSON body shape, same JWT in the `Authorization` header).
3. **Assert** — check the HTTP response *and*, where the test case specifies it, the actual state written to MongoDB (via the `mongodb` driver) — e.g. that `Room.currentOccupancy` really increased by 1, that a `Contract` document was really created with the right `contractNumber` pattern, that a `Notification` document exists. An `assert()` helper throws a descriptive error the moment something doesn't match, which the runner catches and reports as `FAIL` with that exact message.

Two test cases per file that involve pure UI behavior (an instantly-updating status badge, a real-time toast notification's on-screen appearance) additionally verify what *can* be checked at the data layer (e.g. that a `Notification` document was created in the right shape) even though they are still listed as needing a final manual look at the browser, since a plain Node script has no browser/socket client to observe the UI itself.

### 3. How to run it

```bash
cd Domitory_Management_Backend
# make sure the backend (npm run start:dev) and MongoDB are already running

node e2e-tests/run-all.js        # runs all 5 suites, 55 test cases total
# or run one use case at a time:
node e2e-tests/uc-auth-02.js
node e2e-tests/uc-room-06.js
node e2e-tests/uc-room-07.js
node e2e-tests/uc-fin-02.js
node e2e-tests/uc-chk-04.js
```

Each run prints `PASS` / `FAIL` / `MANUAL` per test case as it goes, and `run-all.js` finishes by printing a Markdown table (Test case ID / date / status / actual result) that can be copied directly into Section E below, plus a plain list of every `FAIL` with its error message to make writing the Bug Report faster.

If your local `.env` uses a different database name/URI or a different backend port, override the defaults with environment variables instead of editing the code:
```bash
MONGO_URI="mongodb://127.0.0.1:27017/your_db_name" BASE_URL="http://localhost:3001/api" node e2e-tests/run-all.js
```
The 3 seeded accounts (`e2e.admin@test.local`, `e2e.staff@test.local`, both password `E2Etest123`) are assumed to already exist (created by `scripts/e2e-seed.js`); if your seed data uses different credentials, update the `SEEDED_ADMIN` / `SEEDED_STAFF` constants at the top of `uc-auth-02.js`, `uc-room-06.js`, `uc-room-07.js`, `uc-fin-02.js`, and `uc-chk-04.js` to match.

### 4. Known limitations, honestly stated

- **UC-ROOM-07 processes every unassigned student in the whole database, not just the ones a test creates.** Tests that need to check a *specific* student's outcome filter the API response down to just their own `studentId`s rather than trusting global counts like `assignedCount`. A few tests (gender-matching, ordering, concurrency) additionally assume there is no *other* compatible room quietly absorbing the test's students first — true on a clean/dedicated test database, but potentially flaky on a shared dev database with a lot of pre-existing rooms/students. This tradeoff, and which specific tests it affects, is documented in a comment at the top of `uc-room-07.js`.
- **Three code comments flag places where the actual backend behavior differs from what the first draft of this document assumed** (before the team read the real `invoices.service.ts`): bulk invoice generation uses a `readings: [...]` array, not a `rooms: [...]` array; an invalid electricity/water reading is silently skipped (HTTP 200 with `skipped: 1`) rather than rejected with HTTP 400; and an invoice can currently be generated for a completely empty room, since the code has no occupancy check. These are called out inline in `uc-fin-02.js` rather than silently "fixed" to make the test pass, since whether the last one is a bug or intended behavior is a product decision for the team, not something a test script should decide unilaterally.
- **TC-CHK-04-11 (transaction atomicity under a mid-operation failure) is not run automatically.** Reliably forcing MongoDB to fail *in the middle* of a transaction from outside the process requires a dedicated MongoDB fail point, which risks affecting other work happening on the same database at the same time. It is left as a documented manual/code-review check instead of a real fault-injection test.

## E. Test Summary (to be completed after execution)

| Feature / Use case | # Test cases | # Passed | # Failed | Notes |
|---|---|---|---|---|
| UC-AUTH-02 — Log In | 11 | 10 | 1 | Tests completed, test TC-AUTH-02-07 failed at FLOOR_MANAGER role |
| UC-ROOM-06 — Review Room Application | 11 | 3 | 8 | Tests completed |
| UC-ROOM-07 — Run Automatic Room Allocation | 11 | 2 | 9 | Tests completed |
| UC-FIN-02 — Create or Bulk-Generate Invoices | 11 | 5 | 6 | Tests completed |
| UC-CHK-04 — Refund Deposit and Complete Checkout | 11 | 1 | 10 | Tests completed |
| **Total** | **55** | — | — | |

## F. Bug Report

**Bug 1**
| Field | Detail |
|---|---|
| Bug ID | BUG-01-TC-AUTH-02-07 |
| Linked Test Case | TC-AUTH-02-07 |
| Description | Infinite redirect loop when logging in with a Floor Manager account. The UI gets stuck at the "checking access permissions" state, while the frontend continuously calls the GET /admin API uncontrollably. |
| Steps to Reproduce | Open the browser and navigate to /login --> Enter the Floor Manager credentials (norway@example.com / norway1234) --> Click "Login" --> Observe the UI and monitor the logs on the terminal running npm run dev for the frontend. |
| Expected Result | The system returns HTTP 200 OK, and the browser correctly redirects the user to the /staff page. |
| Actual Result | The browser fails to redirect to /staff. The UI is stuck on the "checking access permissions" loading screen. The frontend terminal continuously outputs an infinite loop of GET /admin 200 requests. |
| Severity | Low - Although this error completely blocks the user flow for the FLOOR_MANAGER role, the team has no intention of implementing or supporting this role within the current project scope. As a result, the core functionalities of the entire system and other roles (STUDENT, ADMIN, MAINTENANCE_STAFF) are completely unaffected. |
| Status | Closed |

**Bug 2**
| Field | Detail |
|---|---|
| Bug ID | BUG-R-01-ROOM-06 |
| Linked Test Case | TC-ROOM-06-01, TC-ROOM-06-02, TC-ROOM-06-07, TC-ROOM-06-11,... |
| Description | When a student liquidates their contract or completes the checkout process and attempts to re-apply for a room, the system blocks the action with the error message: "There is a pending application or you are currently a resident." Meanwhile, the student's previous application status on the Admin dashboard still incorrectly displays as "Approved". |
| Steps to Reproduce | Log in as a Student who has recently checked out or liquidated their rental contract --> Navigate to the room booking page and attempt to submit a new room application --> Observe the error message blocking the submission --> Log in as an Admin and open the Bookings management page --> Locate the student's previous application and observe its status. |
| Expected Result | The previous booking status should be properly archived or reset upon contract liquidation, allowing the student to successfully submit a new room application. |
| Actual Result | The system blocks the new application with a state conflict error, as the old booking remains stuck in the "APPROVED" state. |
| Severity | High — After checking out or liquidating a contract, the student is permanently blocked from registering again. This entirely deprives the student of the opportunity to book a new room for a second time. |
| Status | Closed |