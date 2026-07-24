# Use-Case Specifications — 3. System Administration

<!-- Performed by: <member>; Reviewed by: <member>; Edited by: <member> -->

> Diagram: see `../use-case-model.md` §3. Legend: ✅ implemented · 🔶 partially implemented · 🆕 not yet implemented. See `01-authentication-profile.md` for the shared screenshot note.

---

## UC-ADM-01 — Manage User Accounts ✅

**Actor(s):** System Admin (lock/unlock also usable by Dormitory Manager in the backend, though this diagram assigns the use case to Admin only)

**Description:** The admin views, locks, unlocks, or deletes user accounts.

**Preconditions:** Actor is `ADMIN`.

**Basic Flow:**
1. Admin opens the account list (`/admin/permissions` or `/admin/students`).
2. Admin selects "Khóa tài khoản" and enters a reason; frontend calls `PATCH /api/users/:id/block`.
3. Backend rejects an empty reason, otherwise sets `accessStatus = LOCKED` + `blockReason`.

**Alternative Flows:**
- **AF1 — Unlock:** `PATCH /api/users/:id/unblock` resets `accessStatus = ACTIVE`, clears `blockReason`.
- **AF2 — Delete:** `DELETE /api/users/:id` permanently removes the account. ⚠ **verify:** confirm whether deletion is blocked when the account still has an active room/contract, since no such guard was found.
- **AF3 — Missing reason on lock:** rejected (400).
- **AF4 — Admin locks/deletes themselves or another Admin:** currently unrestricted. **Decision needed:** should this be blocked?

**Postconditions:** `accessStatus` updated (enforced from the account's next API call, per `JwtAuthGuard`'s live re-check), or the `User` document is removed.

**Special Requirements:** In the actual backend, block/unblock is available to both `ADMIN` and `DORMITORY_MANAGER`; only `ADMIN` can delete or edit arbitrary user fields via `PATCH /users/:id`.

---

## UC-ADM-02 — Manage Roles and Permissions 🔶

**Actor(s):** System Admin

**Description:** The admin views every account's role/status and reassigns a user among the fixed roles.

**Preconditions:** Actor is `ADMIN`.

**Basic Flow:**
1. Admin opens `/admin/permissions`; `GET /api/users/access-control` lists every account with role + status.
2. Admin changes a user's role via `PATCH /api/users/:id`.
3. Backend validates the new role against the schema enum and saves.

**Alternative Flows:**
- **AF1 — Invalid role value:** rejected by the enum constraint.

**Postconditions:** `user.role` updated; reflected on the account's next login.

**Special Requirements:**
- 🔶 **Partially implemented.** There is **no** dynamic `Role`/`Permission` collection — the description "Create roles, assign permissions... revoke access" implies configurable roles/permission sets, but the backend only supports reassigning a user among 5 hardcoded roles (`STUDENT`, `ADMIN`, `DORMITORY_MANAGER`, `FLOOR_MANAGER`, `MAINTENANCE_STAFF`), each with access hardcoded via `@Roles()` decorators per endpoint. **Decision needed:** build true dynamic RBAC (custom roles + a permission-matrix UI), or keep documenting the fixed-role model as final and narrow this use case's description to match.

---

## UC-ADM-03 — View and Filter Audit Logs ✅

**Actor(s):** System Admin

**Description:** The admin reviews a chronological, filterable record of every data-mutating action: who, what, when, and the result.

**Preconditions:** Actor is `ADMIN`.

**Basic Flow:**
1. Admin opens `/admin/audit-logs`.
2. `GET /api/audit-logs?page=1&limit=25` returns a paginated, newest-first list (a global `AuditLogInterceptor` populates it on every `POST`/`PATCH`/`PUT`/`DELETE`).
3. Admin reviews timestamp, actor, action label, method, path, status code, IP.
4. Admin filters by method or searches by keyword (path/action/email/role via case-insensitive regex).

**Alternative Flows:**
- **AF1 — No results match the filter:** empty state shown.
- **AF2 — The underlying request failed:** the log entry still records the true error status code.

**Postconditions:** None — read-only, immutable log.

**Special Requirements:** Entries auto-expire after 180 days (TTL index); request bodies are never stored (password safety); logging is fire-and-forget and never affects the logged request's own response.

---

## UC-ADM-04 — View Administration Dashboard ✅

**Actor(s):** System Admin (Dormitory Manager also has dashboard access in the backend)

**Description:** The admin sees an overview of dormitory operations: occupancy, pending requests across modules, and revenue.

**Preconditions:** Actor is logged in with a management role.

**Basic Flow:**
1. Admin lands on `/admin` after login.
2. Frontend issues parallel requests: student count, pending bookings/transfers/checkouts/absences/maintenance, and `GET /api/invoices/stats/revenue`.
3. Dashboard renders summary tiles and a Recharts revenue chart.

**Alternative Flows:**
- **AF1 — No data yet:** tiles show zero; chart shows an empty state.
- **AF2 — One parallel request fails:** ⚠ **verify** the dashboard degrades gracefully rather than failing entirely.

**Postconditions:** None — read-only.

**Special Requirements:** `stats/revenue` is `ADMIN`-only in the backend, unlike the rest of the dashboard which `DORMITORY_MANAGER` can also view — ⚠ **verify this is intentional.**

---

*(FR08 — Back up and restore data — is intentionally out of scope for this group per the diagram's own note.)*
