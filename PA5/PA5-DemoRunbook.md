# PA5-2026 — Part B: Final Product Demo Runbook

**Course:** CS300 - CSC13002 - Introduction to Software Engineering
**Project:** Dormify — Dormitory Management System
**Group:** Group 04
**Format:** ~15 minutes, live product, no slides. Every member presents at least one section.

*Performed by: [name] · Reviewed by: [name] · Edited by: [name]*

> **What this document is.** A runbook for the live demo: what to show, in what order, with what
> data, and who presents each part. The presenter column is left blank — the team assigns it. Rehearse
> once end-to-end against the seeded data below before the session.

---

## 1. Before the session

**Environment**

| # | Step | Command / action |
|---|---|---|
| 1 | Start MongoDB (replica set, required for the multi-document transactions in booking and checkout) | local `mongod --replSet` or the Dockerised replica set |
| 2 | Start the Ollama runtime, or the AI section will report itself offline | `ollama serve`, with `CHAT_MODEL` and `EMBED_MODEL` already pulled |
| 3 | Start the backend on port 3001 | `cd src/backend && npm run start:dev` |
| 4 | Start the frontend | `cd src/frontend && npm run dev` |
| 5 | Confirm the knowledge base is ingested, or the assistant will return not-found for everything | `POST /api/chatbot/ingest` as an admin |

**Seed data to prepare in advance** — the demo must not be spent creating records live.

| # | Record | Why it is needed |
|---|---|---|
| 1 | One `STUDENT` with no room and no booking | Section 3: the booking → contract journey starts from a clean state |
| 2 | One `STUDENT` already in a room, with an active contract and at least one invoice | Sections 4 and 6: invoices and the AI invoice card need a real record |
| 3 | One `STUDENT` with two `ACTIVE` violations and a conduct score below 100 | Section 5: one violation is appealed live, one is revoked directly |
| 4 | One `ADMIN` and one `MAINTENANCE_STAFF` account | Every management and staff screen |
| 5 | At least two rooms with free capacity, and several unassigned students | Section 3: automatic allocation needs something to allocate |

**Have open in advance:** browser tabs for `/login`, `/admin`, `/student`, `/staff`; MongoDB Compass
if the team wants to show persisted state; a terminal showing the backend log for the realtime
notifications.

**Fallback plan.** If Ollama fails to start, present Section 6 by showing the answer-feedback review
screen and the ingestion endpoint instead of a live question, and say plainly that the runtime is a
local external dependency. Do not spend demo time debugging it.

---

## 2. Brief introduction — 1 to 2 minutes

*Presenter:* ______________

| Beat | Content |
|---|---|
| Problem statement | Dormitory operations are handled manually and in fragments — accounts, room allocation, contracts, invoices, maintenance, absence reporting, violations, and communication each live in a separate place, so nothing reconciles. |
| Product position | Dormify is a role-based dormitory management and student self-service web application: one system where a student can apply for a room, sign a contract, pay an invoice, report a fault, and see their conduct record, and where staff run the same lifecycle from the other side. |
| Target users | Students; System Administrators; Dormitory Managers; Maintenance Staff. |

Keep this to spoken words over the running homepage. Do not narrate the feature list — the demo is
the feature list.

---

## 3. Workflow 1 — Room application to contract — 3 to 4 minutes

*Presenter:* ______________ · *Use cases:* UC-AUTH-02, UC-ROOM-04/06, UC-CON-01, UC-ROOM-07

| # | Action | What to point out |
|---|---|---|
| 1 | Log in as the fresh student | Role-based redirect to `/student` — the same login routes an admin to `/admin` and staff to `/staff` |
| 2 | Browse rooms and submit a room application | Capacity and room detail are visible before applying |
| 3 | Switch to the admin tab | The admin's notification bell updates in realtime over Socket.IO, without a refresh |
| 4 | Approve the application at `/admin/bookings` | One action writes four things atomically: booking approved, contract created, `Room.currentOccupancy` incremented, `User.room` set. Say the word *transaction* here and connect it to the C4 container diagram. |
| 5 | Return to the student tab | The student now sees their room, roommates, and contract, and can export the contract PDF |
| 6 | Show `/admin/auto-assign` | The same lifecycle in bulk: preview first, then allocate every unassigned student in one operation, gender-matched |

---

## 4. Workflow 2 — Invoice to payment — 2 to 3 minutes

*Presenter:* ______________ · *Use cases:* UC-FIN-02, UC-FIN-08, UC-FIN-09

| # | Action | What to point out |
|---|---|---|
| 1 | At `/admin/invoices`, generate invoices in bulk from meter readings | Room fee, electricity, and water are computed per room; the `(room, month, year)` uniqueness guard prevents double billing |
| 2 | Attempt the same generation again | The duplicate is rejected rather than silently creating a second invoice |
| 3 | Switch to the student tab | The invoice appears with its due date and status, and the student was notified |
| 4 | Pay through the mock payment flow | Be explicit that this is a mock gateway, not a real VNPay/MoMo integration — it is listed as future scope in the Vision Document |
| 5 | Show `/admin/debts` and the revenue chart | Overdue tracking and reporting both read from the invoices created in step 1 |

---

## 5. Workflow 3 — Conduct, appeal, and maintenance — 3 to 4 minutes

*Presenter:* ______________ · *Use cases:* UC-COND-01/06/07/08, UC-MNT-01/03/05

This is the workflow to lead with if time runs short, because it is the PA4 feature and it shows a
state machine rather than a form.

| # | Action | What to point out |
|---|---|---|
| 1 | As admin, record a violation against the prepared student | The conduct score drops by the penalty and the student is notified with the remaining score |
| 2 | As that student, open `/student/profile` and appeal the violation with a reason | Submitting with a blank reason is blocked first — show the validation, then submit properly. The record moves to *Đang khiếu nại* and the board is notified. |
| 3 | As admin, open `/admin/violations` and filter to pending appeals | This is the review queue; the filter is the feature, not decoration |
| 4 | Accept the appeal | The violation moves to *Đã thu hồi* and the deducted points come back — say out loud that restoration is capped at 100 and happens exactly once |
| 5 | Reject the second student's appeal with a note, or revoke a violation directly | Shows both remaining paths; the student sees the reviewer's note |
| 6 | Student reports a maintenance fault with a photo | Cloudinary upload, priority selection |
| 7 | Admin assigns it; staff tab receives the job in realtime and completes it with a resolution note | Pending → In Progress → Resolved, with the student rating the result 1–5 stars |

---

## 6. Workflow 4 — Dormify AI assistant — 2 minutes

*Presenter:* ______________ · *Use cases:* UC-AI-01, UC-AI-02, UC-AI-03

Ask exactly three questions, in this order. The third is the one that matters.

| # | Question | What it demonstrates |
|---|---|---|
| 1 | A documented rules question, e.g. *"Quy định giờ giấc ra vào ký túc xá thế nào?"* | Grounded answer streamed token by token, with source chips naming the real documents underneath |
| 2 | *"Hóa đơn tháng này của tôi bao nhiêu tiền?"* as the student with an invoice | Personal context: the assistant answers about *this* user only, and renders a structured invoice card whose figures come from the database rather than from the language model |
| 3 | Something plainly not in the documents, e.g. *"Ký túc xá có sân bay riêng không?"* | The assistant says the documents do not cover it and offers suggestions — it does not invent an answer. Say explicitly that this is the behaviour the team tested for. |

Then click thumbs-down on an answer and open the admin feedback review screen to close the loop.

---

## 7. Technical overview — 2 to 3 minutes

*Presenter:* ______________

| Beat | Content |
|---|---|
| Tech stack | Next.js App Router frontend; NestJS + Mongoose backend on port 3001; MongoDB with a replica set for transactions; Socket.IO for realtime; Cloudinary for images; Ollama running the chat and embedding models locally. |
| Architecture | Show the C4 diagrams from `PA4/Part B` and `PA4/Part C` — four containers at Level 2, then the module-per-domain component view at Level 3. Name one concrete thing the diagram explains, e.g. why the AI runtime is drawn as an external system and what happens when it is down. |
| Security model | `JwtAuthGuard` re-reads the account status from the database on every request, so locking an account takes effect on the next call rather than at next login; `RolesGuard` plus `@Roles(...)` enforces the role matrix per endpoint. |
| Spec Kit | Show one artifact set — `PA4/Part E - Spec kit/002-violation-appeal-revocation/` — and trace it forward: `spec.md` scenarios became the acceptance rules, `contracts/violations-api.md` fixed the API shape before either side was written, and the scenarios became test cases `TC-COND-06-01` onward. Mention honestly that the generated scenarios needed the adversarial cases added by hand. |
| Testing | `e2e-tests/` runs 55 automated cases against the real API and database; 89 test cases total are documented in `PA5-TestPlan-and-TestCases.md`. |

---

## 8. Timing and rehearsal checklist

| Section | Target | Running total |
|---|---|---|
| 2. Introduction | 1:30 | 1:30 |
| 3. Room application to contract | 3:30 | 5:00 |
| 4. Invoice to payment | 2:30 | 7:30 |
| 5. Conduct, appeal, maintenance | 3:30 | 11:00 |
| 6. Dormify AI | 2:00 | 13:00 |
| 7. Technical overview | 2:00 | 15:00 |

- [ ] Every member is assigned at least one section above.
- [ ] Seed data prepared and verified the morning of the demo.
- [ ] Full run-through completed once, timed.
- [ ] Known defects agreed on in advance: if asked about the Floor Manager role, answer with
      `BUG-01-TC-AUTH-02-07` and the scoping decision behind it, rather than improvising.
- [ ] Backup recording available in case the live environment fails.
- [ ] Demo video link recorded in `PA5/PA5_youtubeLink.txt`, matching the PA3 and PA4 convention.
