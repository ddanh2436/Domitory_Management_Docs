# Use-Case Specifications — Index (PA3 Section D)

<!-- Performed by: <member>; Reviewed by: <member>; Edited by: <member> -->

Full specification for every use case shown in `../use-case-model.md`, grouped the same way (7 files, 40 use cases, UC-01 → UC-40). Each specification includes: name & ID, actor(s), description, preconditions, basic flow, alternative flows, postconditions, and special requirements, per the PA3 rubric.

| File | Use cases | Functional Requirements |
| --- | --- | --- |
| [01-authentication-profile.md](01-authentication-profile.md) | UC-01 … UC-03 (+02a, 02b) | FR01–FR03 |
| [02-system-administration.md](02-system-administration.md) | UC-04 … UC-08 | FR04–FR08 |
| [03-room-booking-allocation.md](03-room-booking-allocation.md) | UC-09 … UC-15 | FR09–FR13 |
| [04-contracts-checkout.md](04-contracts-checkout.md) | UC-16 … UC-23 | FR14–FR21 |
| [05-finance.md](05-finance.md) | UC-24 … UC-30 | FR22–FR24 |
| [06-maintenance.md](06-maintenance.md) | UC-31 … UC-35 | FR26–FR28 |
| [07-residency-rules.md](07-residency-rules.md) | UC-36 … UC-40 | FR25, FR29–FR30 |

## ⚠ Items requiring a team decision before this section is submission-ready

These were found while writing the specs against the actual backend code and are called out inline (search each file for "⚠") — collected here for convenience:

1. **UC-02b** — confirm the "forgot password" response message when the email is not registered (avoid leaking which emails exist), and decide whether the `sandbox-reset-password` bypass endpoint should be removed/disabled before grading.
2. **UC-03 / UC-10** — `fullName` and `cccd` are currently in the student's *self-service* editable whitelist, contradicting the spec's "only Admin edits core identity data" rule. Decide: update the rule, or remove those two fields from the student whitelist.
3. **UC-04** — decide whether an Admin should be blocked from locking/deleting themselves or another Admin (currently unrestricted); confirm whether deleting a user with an active room/contract should be blocked or cascaded.
4. **UC-05** — decide whether dynamic RBAC (custom roles + permission matrix) is in scope for this submission, or whether the fixed 5-role model is the accepted final design.
5. **UC-08** — entirely unimplemented (FR08). Decide the backup mechanism (mongodump/mongorestore vs. custom export), storage location, and whether it must be built before grading.
6. **UC-09** — decide whether to add a `genderType` input to the room create/edit form so auto-assignment (UC-13) can actually separate male/female rooms in practice.
7. **UC-13** — same gender-input gap on the student side (`User.gender`); without it, auto-assignment only ever matches students to `MIXED` rooms.
8. **UC-17 / UC-18** — decide whether to add explicit `@Roles('STUDENT')` guards to `/contracts/extend` and `/contracts/terminate` (currently open to any authenticated role, relying only on self-scoped queries), and whether self-service termination (UC-18) should remain available once the full checkout workflow (UC-20–22) exists.
9. **UC-16** — confirm whether `GET /contracts/my-contract` should be filtered to `ACTIVE` only, or intentionally returns the most recent contract regardless of status.
10. **UC-29** — confirm the revenue-stats endpoint should stay `ADMIN`-only rather than also allowing `DORMITORY_MANAGER` (inconsistent with the rest of the dashboard).
11. **UC-31** — confirm whether a maintenance report should still be accepted text-only if Cloudinary is not configured on the server, and whether new-request notifications should also reach `DORMITORY_MANAGER`/`FLOOR_MANAGER`, not just `ADMIN`.
12. **UC-39** — decide whether a conduct-point recovery mechanism (points restored over time for good behavior) is planned, since the current system only ever deducts.
13. **UC-40** — confirm whether hiding the `markedBy` manager's identity from the student's own violation view is an intentional privacy choice.
14. **Prototype screenshots (all use cases)** — confirm with the TA whether screenshots of the already-implemented, running application satisfy the "UI prototype" requirement, or whether a separate Figma/v0/Bolt artifact is mandatory regardless. See the note at the end of `01-authentication-profile.md`.

## Next steps for the team

1. Resolve the decision points above (or explicitly accept the current behavior as final).
2. Run the app and capture screenshots for each use case's basic/alternative flows; save them under `screenshots/UC-XX/` next to the relevant spec file and embed them.
3. Log any resulting code or spec changes in `../../Changes.md`.
