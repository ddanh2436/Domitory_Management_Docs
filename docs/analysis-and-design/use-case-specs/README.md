# Use-Case Specifications — Index (PA3 Section D)

<!-- Performed by: <member>; Reviewed by: <member>; Edited by: <member> -->

Full specification for every use case shown in `../use-case-model.md` (11 functional groups, 72 use cases). Each specification includes: name & ID, actor(s), description, preconditions, basic flow, alternative flows, postconditions, and special requirements, per the PA3 rubric.

**Legend used throughout:** ✅ implemented and verified against the current code · 🔶 partially implemented · 🆕 not yet implemented (proposed design only).

| File | Use cases | Diagram §/Functional Requirements | Status |
| --- | --- | --- | --- |
| [01-authentication-profile.md](01-authentication-profile.md) | UC-AUTH-01…04, UC-PRO-01…03, UC-STAY-01…03 | §2 / FR01–FR03 | 8 ✅ · 2 🆕 |
| [02-system-administration.md](02-system-administration.md) | UC-ADM-01…04 | §3 / FR04–FR07 | 3 ✅ · 1 🔶 |
| [03-room-student-management.md](03-room-student-management.md) | UC-ROOM-01…10, UC-STU-01 | §4 / FR09–FR13 | 10 ✅ · 1 🔶 |
| [04-contract-management.md](04-contract-management.md) | UC-CON-01…06 | §5 / FR14–FR17 | 6 ✅ (2 with actor mismatch) |
| [05-checkout-deposit-refund.md](05-checkout-deposit-refund.md) | UC-CHK-01…04 | §6 / FR18–FR21 | 4 ✅ |
| [06-finance-meter-management.md](06-finance-meter-management.md) | UC-FIN-01…11 | §7 / FR22–FR24 | 6 ✅ · 5 🔶 |
| [07-residence-management.md](07-residence-management.md) | UC-RES-01…05 | §8 / FR25 | 3 ✅ · 1 🔶 · 1 🆕 |
| [08-maintenance-management.md](08-maintenance-management.md) | UC-MNT-01…10 | §9 / FR26–FR28 | 9 ✅ · 1 🆕 |
| [09-feedback-suggestions.md](09-feedback-suggestions.md) | UC-FBK-01…03 | §10 / (not in FR01–30) | 3 🆕 |
| [10-conduct-evaluation.md](10-conduct-evaluation.md) | UC-COND-01…05 | §11 / FR29–FR30 | 3 ✅ · 1 🔶 · 1 🆕 |
| [11-notifications-message-center.md](11-notifications-message-center.md) | UC-NOT-01…03 | §12 / (postconditions of other UCs) | 2 ✅ · 1 🔶 |

**Total: 72 use cases — 54 ✅ fully implemented, 10 🔶 partially implemented, 8 🆕 not yet implemented.**

## ⚠ Items requiring a team decision before this section is submission-ready

Collected from every "⚠" / "Decision needed" flag inline in the 11 files above, grouped by theme:

### A. Real discrepancies between the diagram and the running code (fix one side or the other)
1. **UC-CON-02 / UC-CON-03** — the diagram assigns "Extend contract" and "Liquidate contract" to **Dormitory Manager**, but the actual endpoints (`POST /contracts/extend`, `POST /contracts/terminate`) are **student self-service**, driven by `req.user.sub`, with no `@Roles` guard at all. This is the single most important fix needed in this document set.
2. **UC-FIN-06** — diagram assigns "Generate revenue report" to Dormitory Manager; the backend restricts `GET /invoices/stats/revenue` to `ADMIN` only.
3. **UC-FIN-05** — diagram links `Scheduled Trigger → Send debt reminders`; no scheduled/cron reminder job exists — all reminders are manager-triggered only.
4. **UC-PRO-02** — the diagram's own note says identity fields (full name, CCCD) can only be edited by management, but the backend's student self-service whitelist currently includes both fields.

### B. Use cases that describe more than the code currently does (decide: build it, or narrow the description)
5. **UC-FIN-01** — "upload meter photos" and "view consumption history" are not implemented; readings are transient invoice-generation inputs only, not a persisted, browsable record.
6. **UC-FIN-09** — "Payment Gateway" (bank/VNPay/MoMo/ZaloPay) is a mock that always succeeds; no real provider is integrated.
7. **UC-FIN-10** — no dedicated payment-history/ledger view exists beyond the invoice list's status column.
8. **UC-ROOM-07** — auto-assignment does not consider "student preferences," only gender and capacity.
9. **UC-COND-02** — every violation applies a deduction unconditionally; there is no "zero-impact" or warning-only record type, and no point-recovery mechanism.
10. **UC-MNT-08** — staff cannot upload before/after repair photos; only the student's initial report photo exists.

### C. Entirely unimplemented, requiring a scope decision
11. **UC-PRO-03** (change password while logged in), **UC-STAY-03** (residence history), **UC-RES-04** (visitor registration), **UC-COND-03/04** (periodic evaluation + its history view), and **all of group 9** (UC-FBK-01–03, feedback/suggestions) — these have no code at all. Each spec file sketches a proposed design; the team must decide which of these, if any, are in scope for this submission versus a future sprint.
12. **UC-ADM-02 (FR05 RBAC)** and **UC-ADM-04 vs. FR08 (backup/restore, intentionally out of scope)** — carried over from the prior review round, still open.
13. **UC-AUTH-04** — the diagram calls this an "OTP" flow; the code sends a reset **link**, not a numeric code, and has no SMS channel. Also confirm whether the `sandbox-reset-password` bypass endpoint should be removed before grading.
14. **UC-NOT-02** — "message center" is, today, the exact same screen as UC-NOT-01 (notifications) — decide whether a true two-way messaging feature is required, or merge the two use cases in the diagram.

### D. Prototype screenshots (all use cases)
15. For every ✅ use case, confirm with the TA whether screenshots of the already-running application satisfy the "UI prototype" requirement. For every 🆕 use case, there is no screen to screenshot — the team must either build the feature first or produce a labeled mockup (Figma/v0/Bolt) instead.

## Next steps for the team

1. Resolve the discrepancies in section A above — these are the highest-risk items if a TA cross-checks the diagram against a demo.
2. Decide, per item in sections B and C, what is realistically buildable before submission versus what should be documented as a known gap (update `spec.md`'s Backlog accordingly if scope changes).
3. Capture screenshots for every ✅ use case's basic/alternative flows; save under `screenshots/UC-XX/` next to the relevant spec file and embed them.
4. Log any resulting code or diagram changes in `../../Changes.md`.
