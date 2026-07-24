# Use-Case Specifications — 2. Authentication and Personal Profile

<!-- Performed by: <member>; Reviewed by: <member>; Edited by: <member> -->

> Diagram: see `../use-case-model.md` §2. IDs and scope follow that file's latest revision (11 groups, 72 use cases). See the shared prototype/screenshot note at the end of this file.
>
> **Legend:** ✅ implemented and verified against the current code · 🆕 not yet implemented — spec below is a proposed design for the team to build or reject.

---

## UC-AUTH-01 — Submit Preliminary Residence Profile ✅

**Actor(s):** Applicant / Guest

**Description:** A guest creates an account with basic personal information to apply for dormitory residence.

**Preconditions:** The guest is not logged in and does not already hold an account with the same email.

**Basic Flow:**
1. Guest opens the registration form and enters full name, email, MSSV (optional), and password.
2. Frontend calls `POST /api/auth/register`.
3. Backend rejects duplicate email; rejects duplicate MSSV if one was provided.
4. Backend hashes the password and creates a `User` (`role = STUDENT`, `accessStatus = ACTIVE`, `behaviorScore = 100`).
5. Guest is redirected to log in (UC-AUTH-02).

**Alternative Flows:**
- **AF1 — Email already registered:** rejected with a conflict error.
- **AF2 — MSSV already registered to another account:** rejected with a conflict error.
- **AF3 — OTP/confirmation email** (per the diagram's `Email/SMS Service` actor): 🆕 the diagram shows the Email/SMS Service actor sending a confirmation for this use case; the current backend does **not** send any confirmation email on registration — it returns success immediately. **Decision needed:** add a confirmation step, or update the diagram/actor link to not include it here.

**Postconditions:** A new `User` document exists with no `room` assigned.

**Special Requirements:** Passwords are stored as a bcrypt hash (`select: false`), never returned by any query.

---

## UC-AUTH-02 — Log In ✅

**Actor(s):** Applicant / Guest, Student (and, in practice, every role)

**Description:** A user authenticates with email/MSSV + password, or via Google, and is routed to their role's area.

**Preconditions:** The account exists and `accessStatus = ACTIVE`.

**Basic Flow:**
1. User submits an identifier (email **or** MSSV) and password.
2. Frontend calls `POST /api/auth/login`.
3. Backend finds the user by `email` OR `mssv` matching the given identifier, verifies the password hash, and checks `accessStatus`.
4. Backend issues a JWT (`sub`, `email`, `role`, `accessStatus`).
5. Frontend persists the token (localStorage + cookie) and redirects to `/admin`, `/student`, or `/staff` by role.

**Alternative Flows:**
- **AF1 — Wrong password:** 401 "Sai mật khẩu".
- **AF2 — Identifier not found:** 401 "Sai thông tin đăng nhập".
- **AF3 — Account locked:** 401 with the recorded block reason.
- **AF4 — Log in with Google:** user authenticates via the Google OAuth Provider instead; `POST /api/auth/google` verifies the Google ID token and auto-creates an account on first login if none exists for that email.

**Postconditions:** A valid JWT is held by the client.

**Special Requirements:**
- `JwtAuthGuard` re-reads `accessStatus` from the database on every subsequent request, so a lock applied mid-session takes effect on the very next API call.
- ⚠ The diagram/notes describe the non-Google identifier as "email or citizen identification number (CCCD)"; the actual field checked in `auth.service.ts` is **MSSV** (student ID), not CCCD. **Verify with the team** which wording is correct and align the diagram note.

---

## UC-AUTH-03 — Log Out ✅

**Actor(s):** Student (and every other role, via their own layout's logout action)

**Description:** A logged-in user ends their session.

**Preconditions:** The user is logged in.

**Basic Flow:**
1. User clicks "Đăng xuất" (in the sidebar footer, or the account/avatar menu on admin).
2. Frontend calls `clearToken()`, removing the token from `localStorage` and the `token` cookie.
3. User is redirected to `/login`.

**Alternative Flows:** None — this is a purely client-side action with no server call.

**Postconditions:** The client holds no valid token; the next request to any protected page is redirected to `/login` by `proxy.ts`.

**Special Requirements:** There is no server-side token blacklist/revocation — logout only discards the client's copy. A JWT already issued remains cryptographically valid until it expires, though `JwtAuthGuard`'s live `accessStatus` check (UC-AUTH-02) still applies for lock/unlock scenarios.

---

## UC-AUTH-04 — Reset Forgotten Password ✅

**Actor(s):** Applicant / Guest, Student; Email/SMS Service (external)

**Description:** A user who forgot their password requests a reset link/OTP by email and sets a new password.

**Preconditions:** An account exists with the given email.

**Basic Flow:**
1. User submits their email on the "Quên mật khẩu" screen.
2. Frontend calls `POST /api/auth/forgot-password`.
3. Backend generates `resetPasswordToken` + `resetPasswordExpires` and sends a reset link via `MailService` (email).
4. User opens the link, enters a new password, and submits.
5. Frontend calls `POST /api/auth/reset-password` with the token and new password.
6. Backend validates the token has not expired, hashes the new password, and clears the reset fields.

**Alternative Flows:**
- **AF1 — Token expired/invalid:** rejected; user must request a new link.
- **AF2 — Email not registered:** ⚠ **verify** whether the system returns a generic success message regardless (to avoid leaking which emails exist) or a distinct error.

**Postconditions:** The account's password is updated; the token cannot be reused.

**Special Requirements:**
- The diagram labels this an "OTP" flow via Email/SMS; the current implementation sends a **reset link with a token**, not a numeric OTP code, and there is no SMS channel — only email. ⚠ **Decision needed:** align the diagram wording with the actual link-based flow, or implement a true OTP/SMS flow if that is the intended UX.
- A `POST /api/auth/sandbox-reset-password` endpoint bypasses the email step entirely (testing only). **Decision needed:** disable/remove before grading, or keep as a documented QA tool.

---

## UC-PRO-01 — View Personal Profile ✅

**Actor(s):** Student

**Description:** A student views their personal information.

**Preconditions:** Logged in.

**Basic Flow:**
1. Student opens `/student/profile`.
2. Frontend calls `GET /api/users/profile`, returning the user document (minus password hash) with room populated.

**Alternative Flows:** None.

**Postconditions:** None — read-only.

**Special Requirements:** None.

---

## UC-PRO-02 — Update Contact Information ✅

**Actor(s):** Student

**Description:** A student edits the subset of their profile they are allowed to self-manage.

**Preconditions:** Logged in.

**Basic Flow:**
1. Student edits phone/avatar (and, per current code, also full name/CCCD — see the flag below) on `/student/profile`.
2. Frontend calls `PATCH /api/users/profile`.
3. Backend whitelists only `fullName`, `phone`, `cccd`, `avatar` from the payload and ignores everything else.

**Alternative Flows:**
- **AF1 — Invalid format (e.g., malformed phone):** rejected with an inline validation error.

**Postconditions:** Whitelisted fields are updated.

**Special Requirements:**
- ⚠ **Discrepancy with the diagram's own notes:** §2 states *"Identity-related information such as full name... may only be updated by authorized management roles"* — but the backend's self-service whitelist (`users.service.ts`) currently includes `fullName` and `cccd`, meaning a student **can** self-edit them via the API today even if the UI form doesn't surface those inputs. **Team decision needed:** update the note to match the code, or remove those two fields from the student whitelist so only the Dormitory Manager's `PATCH /users/:id` (UC-STU-01) can change them.

---

## UC-PRO-03 — Change Password 🆕

**Actor(s):** Student

**Description *(proposed)*:** A logged-in student changes their password directly, without going through the "forgot password" email flow.

**Preconditions *(proposed)*:** Student is logged in and knows their current password.

**Basic Flow *(proposed)*:**
1. Student opens a "Đổi mật khẩu" section on their profile page and enters their current password and a new password (with confirmation).
2. Frontend calls a new endpoint, e.g. `PATCH /api/auth/change-password`, with `{ currentPassword, newPassword }`.
3. Backend verifies `currentPassword` against the stored hash, then hashes and saves `newPassword`.

**Alternative Flows *(proposed)*:**
- **AF1 — Current password incorrect:** rejected, no change made.
- **AF2 — New password fails strength requirements:** rejected with a validation error.

**Postconditions *(proposed)*:** The account's password hash is updated; existing sessions are unaffected (no forced re-login), consistent with UC-AUTH-03's lack of token revocation.

**Special Requirements:**
- 🆕 **Not yet implemented.** There is currently no "change password while logged in" endpoint — only `forgot-password`/`reset-password` (UC-AUTH-04), which requires email access. **Decision needed:** confirm this is in scope to build, and whether it needs re-authentication (current password) as shown above.

---

## UC-STAY-01 — View Current Residence Information ✅

**Actor(s):** Student

**Description:** A student views their currently assigned room: building, floor, capacity, price, and status.

**Preconditions:** Logged in.

**Basic Flow:**
1. Student opens `/student/rooms` (this route shows the student's **own** assigned room, not a search page — see UC-ROOM-02 for room search).
2. Frontend calls `GET /api/rooms/me`.
3. Backend returns the room the student currently occupies, including the room's occupant list.

**Alternative Flows:**
- **AF1 — Student has no room assigned:** the page shows an empty state instead of room details (`hasNoRoom`).

**Postconditions:** None — read-only.

**Special Requirements:** "Bed" is not a modeled concept — the schema tracks room-level `capacity`/`currentOccupancy` only, no individual bed assignment. If bed-level tracking is required by the vision document, it is a schema gap. ⚠ **verify with the team.**

---

## UC-STAY-02 — View Roommates ✅

**Actor(s):** Student

**Description:** From the same current-residence view, a student sees who else lives in their room.

**Preconditions:** The student has a room assigned.

**Basic Flow:**
1. On `/student/rooms` (UC-STAY-01), the page renders a "Danh sách cư dân cùng phòng" section listing each occupant's name, MSSV, avatar, and check-in date.
2. Roommate contact info (phone/email) is shown per the current privacy policy noted in the page copy.

**Alternative Flows:**
- **AF1 — Student is the only occupant:** the list shows just themselves (or is empty, depending on how the occupant list is filtered — ⚠ **verify** whether the current user is excluded from their own roommate list).

**Postconditions:** None — read-only.

**Special Requirements:** This is not a separate page — it is a section within the same `/student/rooms` view as UC-STAY-01, not a distinct route.

---

## UC-STAY-03 — View Residence History 🆕

**Actor(s):** Student

**Description *(proposed)*:** A student views a timeline of every room they have occupied over their residency (not just the current one).

**Preconditions *(proposed)*:** N/A.

**Basic Flow *(proposed)*:**
1. Student opens a "Lịch sử cư trú" section.
2. Frontend calls a new endpoint aggregating the student's past `Contract`/`Transfer`/`Checkout` records by room and date range.
3. Page lists each past room with move-in/move-out dates.

**Alternative Flows *(proposed)*:** None distinct.

**Postconditions:** None — read-only.

**Special Requirements:**
- 🆕 **Not yet implemented as a dedicated view.** The underlying data exists in fragments — `Contract` (status history via `TERMINATED` records is not retained once a new contract is created for the same user... ⚠ **verify:** confirm whether old contracts are kept as separate documents after termination/checkout, or whether the schema only ever holds the student's most recent contract), plus `Transfer` history (`GET /transfers/me`) and `Checkout` history (`GET /checkouts/me`). Building UC-STAY-03 means assembling these three sources into one timeline, or introducing a dedicated `ResidenceHistory` collection. **Decision needed:** which approach, and whether this is in scope for the current sprint.

---

## ⚠ TEAM ACTION — Prototype Screenshots (applies to every use case in this document set)

Per the PA3 prototype requirement, each use case must be accompanied by screenshots of the screens involved in its basic and alternative flows. For use cases marked ✅, the team can capture real screenshots from the running app (`npm run dev` in both `src/backend` and `src/frontend`) and save them under `screenshots/UC-XX/`. For use cases marked 🆕 (not yet implemented), there is no running screen to capture — the team must either build the feature first, or produce a Figma/v0/Bolt mockup for just those use cases and label it clearly as a proposed prototype, not a screenshot of a working feature.

**Decision needed from the team:** confirm with the TA whether real application screenshots satisfy the "UI prototype" requirement for the ✅ use cases, and how to handle the 🆕 ones (mockup vs. build-first).
