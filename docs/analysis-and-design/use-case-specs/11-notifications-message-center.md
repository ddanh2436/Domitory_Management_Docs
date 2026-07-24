# Use-Case Specifications — 12. Notifications and Message Center

<!-- Performed by: <member>; Reviewed by: <member>; Edited by: <member> -->

> Diagram: see `../use-case-model.md` §12. Legend: ✅ implemented · 🔶 partially implemented. See `01-authentication-profile.md` for the shared screenshot note.

---

## UC-NOT-01 — View Notifications ✅

**Actor(s):** Student (and every other role, via the same `NotificationBell` component)

**Description:** A user views system-generated and management notifications, both on page load and in realtime.

**Preconditions:** Logged in.

**Basic Flow:**
1. User opens `/student/notifications` (or clicks the bell icon in any layout).
2. `GET /api/notifications/me` returns the user's notifications, newest first.
3. A Socket.IO connection (authenticated via JWT handshake, joined to a `user_<id>` room) pushes new `newNotification` events live, without requiring a page refresh.
4. User marks a notification read (`PATCH /api/notifications/:id/read`) or all read at once (`PATCH /api/notifications/read-all`).

**Alternative Flows:**
- **AF1 — No notifications yet:** empty state.
- **AF2 — Socket connection drops:** the notification bell falls back to whatever was last fetched via the REST call; ⚠ **verify** whether it reconnects automatically.

**Postconditions:** Read/unread state updated per notification.

**Special Requirements:** Every other use case in this document set that "notifies" a user (booking decisions, transfer decisions, new invoices, debt reminders, repair-status updates, residence-declaration updates, violation records) is a **postcondition** of that use case, not a separate action — this is explicitly called out in the diagram's own note.

---

## UC-NOT-02 — Use Message Center 🔶

**Actor(s):** Student

**Description:** A student views received messages and communication history.

**Preconditions:** Logged in.

**Basic Flow (as actually implemented — same screen as UC-NOT-01):**
1. Student opens `/student/notifications` — the same notification list as UC-NOT-01, with no additional "message center" concept layered on top (no threads, no reply capability, no separate inbox).

**Alternative Flows:** None distinct today.

**Postconditions:** None — read-only.

**Special Requirements:**
- 🔶 **Not a distinct feature from UC-NOT-01.** The diagram lists this as a separate use case ("communication history"), but there is no dedicated message/thread model in the backend — all communication is one-directional system notifications (`Notification`/`Announcement` schemas), not a two-way message center. **Decision needed:** is a true message center (e.g., threaded conversations between a student and management, tying into the feedback flow in group 9) required, or should this use case be merged into UC-NOT-01 in the diagram since they are the same screen today?

---

## UC-NOT-03 — Send Announcement or Message ✅

**Actor(s):** Dormitory Manager

**Description:** A manager broadcasts information to a targeted set of students, rooms, floors, or buildings.

**Preconditions:** Actor holds `ADMIN` or `DORMITORY_MANAGER`.

**Basic Flow:**
1. Manager opens `/admin/announcements`, composes a message, and selects the target audience.
2. `POST /api/notifications/broadcast` creates the announcement and fans it out as individual notifications (and/or a broadcast record) to the targeted recipients, delivered in realtime via Socket.IO to any of them currently online.
3. `GET /api/notifications/broadcast/history` lets the manager review previously sent announcements.

**Alternative Flows:**
- **AF1 — Targeting a specific room/floor/building vs. all students:** ⚠ **verify** the granularity of targeting actually implemented in `NotificationsService`'s broadcast logic (e.g., whether floor/building-level targeting exists, or only "all students" vs. "a specific list of user IDs").

**Postconditions:** Notifications created for every targeted recipient; a broadcast history entry exists.

**Special Requirements:** Restricted to `ADMIN`/`DORMITORY_MANAGER` — this is the use case referenced by §10's "Dormitory Manager assumes the feedback-handling responsibility" note in the sense that it is the *outbound* half of manager↔student communication; the *inbound* half (UC-FBK-01–03) does not exist yet.
