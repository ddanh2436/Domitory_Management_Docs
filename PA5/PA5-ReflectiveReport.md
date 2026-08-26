# PA5-2026 — Part C: Reflective Report

**Course:** CS300 - CSC13002 - Introduction to Software Engineering
**Project:** Dormify — Dormitory Management System
**Group:** Group 04
**Project period:** 08 July 2026 – 26 August 2026 (Sprints 1–5)

> **Status of this document.** Sections 1 to 4 are a draft assembled from the project's own
> evidence — the three git histories, the Spec Kit artifacts, the PA4 AI Usage Report, and the
> defects recorded in `PA5-TestPlan-and-TestCases.md`. Every claim below can be checked against
> those sources. The team must still read it and rewrite it in its own words before submission,
> because the grading rewards specific, genuine reflection rather than an accurate summary.
> **Section 5 is deliberately left empty:** each member writes their own paragraph.

---

## 1. Team Experience

*Performed by: [name] · Reviewed by: [name] · Edited by: [name]*

### What went well

**Splitting the system into three repositories forced clean boundaries.** `Domitory_Management_Docs`,
`Domitory_Management_Backend`, and `Domitory_Management_Frontend` were maintained separately for the
whole project. The cost was real — a change touching both tiers needed two commits in two places, and
more than once a member pushed one half and forgot the other — but the benefit was that the API
contract became the thing the team argued about, rather than reaching across the boundary to make a
change work. By PA4 the frontend talked to the backend exclusively through `app/utils/apiClient.ts`,
and no hardcoded `fetch("http://localhost:3001/...")` calls survived.

**Feature ownership stayed stable.** Each member carried one area far enough to know it properly
rather than touching everything shallowly: the AI assistant, the conduct and appeal flow, the
maintenance and staff workspace, and the test suite each had a clear owner. The reviewer convention
carried over from the PA3 documents — every section names who performed, reviewed, and edited it —
and meant no work was merged without a second person having read it.

**The team wrote tests before it was forced to.** The `e2e-tests/` harness was written in Sprint 4,
against the real API and the real database, before PA5 required any execution evidence. That is why
the two defects in the bug report were found by the team rather than discovered during the demo.

### What was difficult

**Documentation drifted behind the code, repeatedly.** This was the single recurring failure. The
Dormify AI assistant was implemented and shipped before it appeared in any use-case document, and the
violation appeal flow was specified through Spec Kit, implemented, and merged while the Use Case
Specification still listed five conduct use cases. Each drift was eventually corrected, but only by
someone noticing — never by a process. Related: when the Use Case Specification was copied from
`PA3/documents/` into `PA4/Part A - .../`, all 92 screenshot links kept their old relative paths and
silently pointed at a folder that does not exist. Nothing in the workflow catches a broken link in a
Markdown document.

**A role was designed and then abandoned halfway.** `FLOOR_MANAGER` exists in the RBAC enum and is
still named in several `@Roles(...)` guards, but the team decided not to build screens for it. The
result is `BUG-01-TC-AUTH-02-07`: logging in as a Floor Manager produces an infinite redirect loop.
The bug is closed as low severity because the role is out of scope, but a role that exists in the
authorization model and cannot log in is a design decision that was never fully carried out in either
direction.

**A late-discovered state bug showed a gap in lifecycle thinking.** `BUG-R-01-ROOM-06` — a student
who checks out cannot apply for a room again, because the old booking stays `APPROVED` — was not
found until PA5 testing, because every earlier test exercised the lifecycle in one direction only.
The team had tested "student gets a room" and "student leaves" separately, and never "student leaves,
then comes back".

**One member left the team after Sprint 2**, and the work was redistributed across the remaining
four. The task tables in the Software Development Plan were re-owned rather than rewritten, which
kept the plan usable but left some estimates attached to the original assignment.

---

## 2. Spec Kit Experience

*Performed by: [name] · Reviewed by: [name] · Edited by: [name]*

Three features were driven through Spec Kit end-to-end: `001-maintenance-resolution-log`,
`002-violation-appeal-revocation`, and `003-feedback-inbox`. Each produced `spec.md`, `plan.md`,
`tasks.md`, `quickstart.md`, and an API contract under `contracts/`.

### Benefits over how the team worked before

**It moved the arguments earlier.** The most valuable artifact turned out to be `spec.md`, not the
generated code. Writing the acceptance scenarios for `002-violation-appeal-revocation` forced two
questions that would otherwise have surfaced during implementation or, worse, during the demo: what
happens when restoring points would push a student above 100, and what happens if a manager revokes
the same violation twice. Both became explicit rules — cap at 100, restore exactly once on the
transition into `REVOKED` — and both ended up as unit tests in `violations.service.spec.ts`. Neither
was in the original feature request.

**The contract file made the two repositories agree in advance.** With `contracts/violations-api.md`
written first, the frontend and backend halves of the appeal feature were built against the same
route names, request shapes, and error messages, and integrated without the usual round of "the API
returns something different from what I assumed".

**`tasks.md` made progress visible.** A feature became a checklist rather than a vague assignment,
which made it obvious when something was actually finished versus mostly working.

### Limitations the team ran into

**Generated scenarios are written for a demo, not for a test.** Every `quickstart.md` reads as a
sequence of clicks — "bấm Khiếu nại → để trống lý do → bị chặn". That is genuinely useful for a
walkthrough, but it has no test case ID, no separated input data, and no independently checkable
expected output, so it cannot be recorded Pass/Fail or linked to a bug. Section E of the test
document records the rewriting this required.

**It only writes scenarios for the user stories it is given, and user stories describe cooperative
users.** Not one generated scenario covered a student appealing another student's violation, or a
student trying to approve their own appeal — the two cases where the feature could actually be
abused. The security boundary of the feature was invisible to the generated material, and the team
had to add those tests by hand.

**It does not know what the rest of the repository looks like.** `003-feedback-inbox/quickstart.md`
instructs the reader to `cd domitory_management_backend`, a directory that does not exist in the
working tree — the folders are `src/backend` and `src/frontend`. Anyone following the generated
script literally fails at the first command.

**Adopting it midway leaves the earlier features unspecified.** The Dormify AI assistant was built
before the team used Spec Kit for it, so it has no `spec.md`, no acceptance scenarios, and no
contract file. When PA5 required AI features to be tested, there was nothing to refine — the 24 AI
test cases had to be derived from the implementation and the PA4 use-case specification instead. The
feature that would have benefited most from up-front specification is the one that has none.

### Net assessment

Spec Kit was worth using for the features it covered, but its output is a **starting point that
still needs an engineer's judgement**, not a deliverable. It reliably produces the happy path and the
obvious validation errors; it does not produce the adversarial cases, the cross-feature interactions,
or anything that depends on knowing the actual repository. Treating its output as finished is the
mistake — the value came from arguing with it.

---

## 3. AI Tools Usage

*Performed by: [name] · Reviewed by: [name] · Edited by: [name]*

The full itemised declaration is in `PA4/Part F - AI Usage Report and Weekly Report/AI-Usage-Report.md`.
This section reflects on the experience rather than repeating the log. Tools used across the project:
Claude Code (Claude Opus 5) in VS Code, Codex/ChatGPT, and Gemini Pro, plus Spec Kit driven by Claude
Code.

### Where AI was genuinely effective

**Auditing documentation against source code.** This was the strongest use by a wide margin, because
it is exactly the work the team kept failing to do by hand. Given read access to both repositories
and asked to check the architecture documents against the code, the tool found real discrepancies the
team had not noticed: `app/context/SocketContext.tsx` was dead code while four components each opened
their own Socket.IO connection; four declared backend dependencies were unused; `InvoicesModule`
exported a service no module imported; and the use-case model described password reset as an OTP code
while the implementation emails a hashed 15-minute reset link. Every one of those was verified against
the files before being accepted.

**Producing first drafts of structured documents.** The C4 diagrams, the tech-stack section, and the
deployment diagram were drafted from the real codebase rather than from a template, which meant the
review was "is this claim true of our code" rather than "what should this document say".

**Working through a feature specification.** Used with Spec Kit, the tool was good at turning a
loosely worded feature request into explicit acceptance scenarios — the point where it asked what
should happen at the score cap was more valuable than any code it wrote.

### Limitations the team encountered

**It is confidently wrong about things it has not read.** Three assumptions in the first draft of the
test document turned out to contradict the actual `invoices.service.ts`: bulk invoice generation takes
a `readings: [...]` array, not `rooms: [...]`; an invalid meter reading is silently skipped with HTTP
200 and `skipped: 1` rather than rejected with a 400; and an invoice can be generated for a completely
empty room because the code has no occupancy check. The team chose to flag all three inline in
`uc-fin-02.js` rather than quietly change the tests to match the code, since the third is arguably a
real defect and that is a product decision.

**A plausible-looking change can break something a human would not have broken.** While tightening the
`Violation.status` field from `string` to the `ViolationStatus` enum — a change that type-checks
cleanly and looks strictly better — the application stopped booting, because `@nestjs/mongoose` cannot
infer a Mongoose type from a TypeScript string enum through reflection and needs `type: String`
declared explicitly. The unit test suite caught it immediately. Without that suite it would have been
found by whoever next ran the backend.

**It does not know what it has not been shown.** The tools worked well when pointed at the code and
poorly when asked about the project in the abstract, which is the same limitation Spec Kit has.

**It cannot supply the things that make a submission genuine.** Meeting minutes, individual
reflections, and execution results are records of what actually happened; drafting them from a
template produces something that reads correctly and is worthless.

### How the team validated AI output

Every AI-produced document was reviewed against the files it claimed to describe before being
committed, and every AI-produced code change was run through `npm run build`, the unit test suites,
and a real application boot. The two conventions that made this manageable were the
performed/reviewed/edited line on every section — which forces a named human to have read it — and
the rule that a finding is only accepted after being traced to a specific file.

---

## 4. SDLC Feedback

*Performed by: [name] · Reviewed by: [name] · Edited by: [name]*

These are offered as concrete changes, in the order the team would prioritise them.

**1. The PA sequence lets documentation and code drift apart, and never checks.** PA3 produces the
use-case specification, PA4 produces the architecture, PA5 produces the tests — but nothing in the
sequence asks whether the PA3 documents still describe the system by the time PA4 is submitted. Our
own set drifted twice: a shipped feature with no use case, and a use case group missing three of its
use cases. *Suggested change:* make each PA include a short, graded "consistency delta" — a table of
what changed in the earlier documents and why — rather than only new artifacts. It costs little and
it is exactly the discipline the course is trying to teach.

**2. Spec Kit is introduced as a generator, but its real value is as a conversation.** The framing
encourages teams to accept the generated `spec.md` and move on. The scenarios it produces cover the
cooperative user and stop there. *Suggested change:* require each team to submit, alongside the
generated spec, a short list of scenarios they added that the generator did not produce — adversarial
cases, cross-feature interactions, boundary values. That makes "review and refine" visible instead of
assumed, which is what PA5 already asks for in the test cases but not at specification time.

**3. Test evidence is required only at PA5, which is too late to change anything.** Our
`BUG-R-01-ROOM-06` — a student cannot re-apply for a room after checkout — is a lifecycle defect that
would have been caught in Sprint 3 by any test that ran the cycle twice. Instead it surfaced in the
last assignment, when the schedule has no room for a redesign. *Suggested change:* require a small
number of executed test cases from PA3 onward, growing each PA, rather than 50+ at the end.

**4. The "AI-powered feature" requirement needs a sharper definition of correctness.** PA5 asks that
AI features be tested for functional correctness, "does the feature produce the expected output for a
given input". For a retrieval-based assistant there is no single expected output — the useful
properties are that a documented question returns a grounded answer with sources, that an
undocumented question returns a not-found response instead of an invented one, and that one user's
personal context never reaches another user. *Suggested change:* state those property-based
expectations explicitly in the assignment, so teams test grounding and isolation rather than trying to
assert on generated prose.

**5. The three-repository structure was our own choice, but the assignment's packaging assumes one.**
The submission asks for a git log "of your repository", singular. With separate document, backend, and
frontend repositories, contribution history is spread across three logs, and a reviewer looking at
only one sees an incomplete picture. *Suggested change:* say explicitly that multi-repository projects
should submit one log per repository.

---

## 5. Individual Contributions

*Performed by: [name] · Reviewed by: [name] · Edited by: [name]*

**Each member writes their own 3–5 sentence reflection covering what they personally contributed and
what they learned. Do not fill these in for each other.** The contribution notes under each name are
drawn from the git history and the PA4 AI Usage Report as a memory aid for what to write about, not
as the reflection itself.

### Đào Duy Anh — 24127012

*Areas from the project record:* PA4 architecture documentation (tech stack, C4 Level 1 and Level 3,
deployment diagram); the violation appeal and revocation feature across both repositories; PA4 folder
structure and document organisation.

*Reflection:*

### Trần Huỳnh Mạnh Đạt — 24127024

*Areas from the project record:* Use-Case Model and Use-Case Specification across PA3 and PA4;
reviewer on the majority of document sections; Vision Document and Software Development Plan revisions.

*Reflection:*

### Trần Hoàng Quốc Khánh — 24127057

*Areas from the project record:* the Dormify AI assistant (retrieval, SSE streaming, answer feedback,
knowledge-base ingestion); the feedback inbox feature; the self-service change-password flow across
student, admin, and staff areas.

*Reflection:*

### Hồ Phúc Kiên — 24127067

*Areas from the project record:* PA5 test plan and test case design; the `e2e-tests/` automated
suite; UI work across the homepage, contract export, and invoice screens; PA3 weekly report.

*Reflection:*

---

*End of Reflective Report — PA5*
