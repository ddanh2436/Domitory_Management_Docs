# Use-Case Specifications — 11. Conduct and Student Evaluation

<!-- Performed by: <member>; Reviewed by: <member>; Edited by: <member> -->

> Diagram: see `../use-case-model.md` §11. Legend: ✅ implemented · 🔶 partially implemented · 🆕 not yet implemented. See `01-authentication-profile.md` for the shared screenshot note.

---

## UC-COND-01 — Record Student Violation ✅

**Actor(s):** Dormitory Manager (`ADMIN`/`DORMITORY_MANAGER` in the backend — `FLOOR_MANAGER` may view but not create, per UC-COND-05)

**Description:** A manager records that a student violated a dormitory rule.

**Preconditions:** Target account exists with `role = STUDENT`.

**Basic Flow:**
1. Manager opens a student's profile or a violations screen, enters the reason (≤ 300 chars) and points to deduct (integer, 1–100).
2. `POST /api/violations` with `{ studentId, reason, points }`.
3. Backend verifies the target is a `STUDENT`, then (always, as part of this same request — see UC-COND-02) deducts points from `behaviorScore` and records the violation with a `scoreAfter` snapshot.
4. Student is notified of the deduction and new score.

**Alternative Flows:**
- **AF1 — Target not found:** 404.
- **AF2 — Target is not a Student:** rejected.
- **AF3 — Points out of range (< 1 or > 100) or non-integer:** rejected.
- **AF4 — Reason empty or > 300 characters:** rejected.

**Postconditions:** A `Violation` record exists, linked to the student and the manager who recorded it (`markedBy`); `User.behaviorScore` is reduced (floored at 0).

**Special Requirements:** `FLOOR_MANAGER` can view violation history (UC-COND-05) but cannot create one — only `ADMIN`/`DORMITORY_MANAGER` may call this endpoint.

---

## UC-COND-02 — Apply Conduct-Score Deduction *(extends UC-COND-01)* ✅

**Actor(s):** System (always performed as part of UC-COND-01; not independently triggerable)

**Description:** Whenever a violation is recorded, the student's conduct score is automatically recalculated and floored at zero.

**Preconditions:** A violation is being recorded (UC-COND-01 in progress).

**Basic Flow:**
1. System reads the student's current `behaviorScore` (default 100 if unset).
2. `scoreAfter = max(0, currentScore − points)`.
3. `User.behaviorScore` is updated; `scoreAfter` is stored on the `Violation` record as a historical snapshot (never rewritten by later violations).

**Alternative Flows:**
- **AF1 — Deduction would go below 0:** floored at 0 instead.

**Postconditions:** `User.behaviorScore` reflects cumulative deductions to date (floored at 0).

**Special Requirements:**
- The diagram's note ("not every recorded incident must necessarily result in a deduction") describes a **conditional** extend relationship, but the current implementation applies a deduction on **every** violation unconditionally — there is no "record with zero point impact" option today (though a manager could enter `points: 1` as a minimal deduction, since 0 is not a valid value per validation). **Decision needed:** should a violation with no score impact be possible (e.g., a warning-only record), which would require allowing `points = 0` or a separate "warning" record type?
- No point-recovery mechanic exists — `behaviorScore` only ever decreases. **Decision needed:** is a recovery mechanism (points restored over time for good behavior) in scope, as UC-COND-03 might suggest if periodic evaluations can also raise a score?

---

## UC-COND-03 — Perform Periodic Student Evaluation 🆕

**Actor(s):** Dormitory Manager

**Description *(proposed)*:** On a recurring schedule (e.g., monthly or per semester), a manager records a broader conduct evaluation for each student, independent of any specific violation.

**Preconditions *(proposed)*:** N/A.

**Basic Flow *(proposed)*:**
1. Manager opens a "Đánh giá định kỳ" screen at the start of an evaluation period.
2. For each student (or a filtered subset), manager records an evaluation outcome (e.g., a qualitative rating or a score adjustment) and comments.
3. Frontend calls a new endpoint, e.g. `POST /api/evaluations`.
4. Backend creates an `Evaluation` record per student for that period.

**Alternative Flows *(proposed)*:**
- **AF1 — Bulk evaluation for an entire floor/building:** manager applies the same baseline outcome to many students at once, adjusting individual exceptions.

**Postconditions *(proposed)*:** One `Evaluation` record per evaluated student for the period.

**Special Requirements:**
- 🆕 **Not yet implemented.** Today, `behaviorScore` changes **only** as a side effect of recorded violations (UC-COND-01/02) — there is no periodic, violation-independent evaluation process. **Decision needed:** define what a "periodic evaluation" actually records (a numeric adjustment? a qualitative tier like "Tốt/Khá/Trung bình"? both?) and how it interacts with the existing `behaviorScore` (adds to it, is stored separately, or replaces it for that period) before building UC-COND-04.

---

## UC-COND-04 — View Evaluation History 🔶

**Actor(s):** Student

**Description:** A student views their history of periodic conduct evaluations.

**Preconditions:** Logged in.

**Basic Flow (as actually implemented — no periodic evaluations exist, see UC-COND-03):**
1. Student views their current `behaviorScore` on `/student/profile` (`GET /api/users/profile`) and their violation history (UC-COND-05) — there is no separate "evaluation" record to browse, since UC-COND-03 does not exist yet.

**Alternative Flows:** None today.

**Postconditions:** None — read-only.

**Special Requirements:**
- 🔶 **Depends entirely on UC-COND-03 being built first.** Until then, this use case has no distinct data to show beyond what UC-COND-05 (violation history) and the profile's `behaviorScore` already provide.

---

## UC-COND-05 — View Violation History ✅

**Actor(s):** Student

**Description:** A student reviews their own recorded violations and the conduct points deducted for each.

**Preconditions:** Logged in.

**Basic Flow:**
1. Student opens their profile.
2. `GET /api/violations/me` returns every violation, newest first, each with reason, points deducted, and the score at that time.

**Alternative Flows:**
- **AF1 — No violations on record:** empty list; `behaviorScore` remains at its default.

**Postconditions:** None — read-only.

**Special Requirements:** The `markedBy` manager's identity is **not** exposed to the student in this endpoint (unlike the manager-facing `GET /violations/student/:id`, which does populate it) — ⚠ **verify this is an intentional privacy choice** rather than an oversight.
