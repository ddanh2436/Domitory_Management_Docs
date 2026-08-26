# PA5-2026 — Part D: Final Submission Checklist

**Course:** CS300 - CSC13002 - Introduction to Software Engineering
**Project:** Dormify — Dormitory Management System
**Group:** Group 04
**Package name:** `PA5-Group04.zip`

*Performed by: [name] · Reviewed by: [name] · Edited by: [name]*

> PA5 asks for a single package containing every document written across the whole project *updated
> to its final version*, plus the complete source code. This checklist tracks what exists, what is
> still open, and what has to be regenerated before the package is built.

---

## 1. Documents — status

| PA | Documents | Status |
|---|---|---|
| PA1 | — | **Missing from the repository.** There is no `PA1/` folder. Either the assignment had no document deliverable for this team, or the artifacts were never committed. Confirm with the course requirements and add the folder if something is owed. |
| PA2 | `spec.md`, `constitution.md`, Vision Document v1.0, Software Development Plan v1.0, AI Usage, Weekly Report, git logs | Present. Synchronised with PA4 on 26 Aug 2026: `FR31`/`FR32` added to `spec.md`; supersession notes added to both v1.0 documents. |
| PA3 | Use Case Model, Use Case Specification, Vision Document v1.2, Software Development Plan v1.2, AI Usage Report, Weekly Report, assets, git logs | Present. Synchronised with PA4 on 26 Aug 2026: AI functional group and the conduct appeal use cases back-ported; Vision and SDP raised to v1.2. |
| PA4 | Parts A–F | Present. Use Case Specification raised to v1.2 (79 use cases); 92 screenshot links repaired; change log extended. **Weekly Report meeting minutes are still empty** — see section 3. |
| PA5 | Test Plan and Test Cases, Demo Runbook, Reflective Report, AI Usage Notes, this checklist | Present. **Test execution results, the test summary, and the bug report are incomplete** — see section 3. |

## 2. Package contents

| # | Item | Required by | Status |
|---|---|---|---|
| 1 | All PA documents, Markdown (`.md`) | Part D | PA2–PA5 present; PA1 unresolved |
| 2 | All PA documents, PDF converted from Markdown | Submission Guidelines | **Outstanding** — see section 4 |
| 3 | Complete source code, excluding `node_modules` and build artifacts | Part D | Backend and frontend repositories; exclude `node_modules/`, `.next/`, `dist/`, and `.env` |
| 4 | All Spec Kit artifacts (specs, plans, tasks, generated tests) | Part D | `PA4/Part E - Spec kit/` and `docs/requirements/specs/` |
| 5 | Test documents: plan, cases, execution results, bug reports | Part A | `PA5-TestPlan-and-TestCases.md`; 44 of 89 cases executed and recorded, 3 bugs filed |
| 6 | AI Usage Report | Part D | `PA5/AI-Usage-Report.md` covers the PA5 window (09–26 Aug 2026) per member; `PA4/Part F - .../AI-Usage-Report.md` covers PA4. Both go in the package. |
| 7 | Git log for each repository | Submission Guidelines | PA2 and PA3 hold logs for earlier sprints; **export a fresh log for all three repositories** covering through the final commit |
| 8 | Demo video link | Convention from PA3/PA4 | `PA5/PA5_youtubeLink.txt` — not yet created |

## 3. Open items that need a person, not a conversion tool

These cannot be completed by editing or reformatting — they are records of things that have to
actually happen.

| # | Item | Owner | Note |
|---|---|---|---|
| 1 | Finish executing the test cases and fill in Section F | Test lead | 44 of 89 were executed on 26 Aug 2026 (40 Pass, 1 Fail, 3 Manual). Still open: the 34 AI and conduct cases, executed by hand, and the 11 `TC-ROOM-07-*` cases — see item 2. |
| 2 | Run `uc-room-07` against an isolated database | Test lead | `POST /api/assignments/auto` allocates across **all** available rooms system-wide, so running it on the shared Atlas cluster would place test students into real dormitory rooms. Point `MONGO_URI` at a local instance, seed it, then run the suite and paste the 11 rows in. |
| 3 | Refresh Section G after the remaining cases run | Test lead | The summary already carries the 26 Aug figures (44 executed, 40 Pass, 1 Fail, 3 Manual). Update it once `TC-ROOM-07-*` and the 34 AI and conduct cases have been executed. |
| 4 | Reconcile the two test runs and close out Section H | Test lead + defect owners | The 26 Aug run recorded 1 failure, filed as `BUG-03`. An earlier recorded run claimed 34 failures against 2 filed bugs. Decide which run the submission reports; if the earlier one is kept, every one of its failures still needs a linked bug report, as PA5 requires. |
| 5 | Fill the Sprint 4 meeting minutes in the PA4 Weekly Report | Whoever kept the minutes | Attendance and each member's three answers. The repository activity record above them is already complete. |
| 6 | Write the five individual reflections in the Reflective Report | Each member | Sections 1–4 are drafted; section 5 is by definition not delegable. |
| 7 | Rewrite Reflective Report sections 1–4 in the team's own voice | All | The draft is factual but grading rewards genuine reflection over an accurate summary. |
| 8 | Assign a presenter to each demo section | All | `PA5-DemoRunbook.md`, presenter fields. |
| 9 | Confirm two git author identities | All | `Loch` and `HueoBee` appear in the commit history and are not mapped to a student number. |
| 10 | Confirm the inferred entry in the PA5 AI Usage Report | Trần Huỳnh Mạnh Đạt | Row 5 of its summary table records that no repository commits were found for this member in the PA5 window and that the role was inferred from the PA4 pattern. Replace the inference with what actually happened. |

## 3b. Test data left in the shared database

The 26 Aug run created records in the shared Atlas cluster and **did not delete anything**. Left in
place so the recorded results stay reproducible: accounts on `@test.local`, rooms named `RM06*`,
`RM07*`, `FIN02*`, `CHK04*`, and the bookings, contracts, and checkouts attached to them.

**Do not run `scripts/e2e-cleanup.js` to clear it.** That script matches `email: /^e2e\./` and
`building: "E2E"`, which does not match what these suites create — so it would leave the residue
behind while deleting the two seeded accounts `e2e.admin@test.local` and `e2e.staff@test.local` that
every suite logs in with, breaking the next run. Either extend the script to match the real patterns
first, or clean up by hand.

## 4. Regenerate before packaging

The Markdown sources changed after these PDFs were exported, so every one of them is now stale.

| # | File to regenerate |
|---|---|
| 1 | `PA3/documents/Use Case Model.pdf` |
| 2 | `PA3/documents/Use Case Specification.pdf` |
| 3 | `PA3/documents/VisionDocument_v1.1.pdf` |
| 4 | `PA3/documents/SoftwareDevelopmentPlan_v1.1.pdf` |
| 5 | `PA4/Part A - Revised Use-Case Specification/Use Case Specification.pdf` |
| 6 | `PA4/Part A - Revised Use-Case Specification/changes.pdf` |
| 7 | `PA5/PA5-TestPlan-and-TestCases.pdf` *(new)* |
| 8 | `PA5/PA5-ReflectiveReport.pdf` *(new)* |
| 9 | `PA5/PA5-DemoRunbook.pdf` *(new)* |
| 10 | `PA4/Part F - .../Weekly Report.pdf` *(new, after the minutes are filled in)* |

Use the same exporter the team used for the earlier PDFs so the styling stays consistent across the
package.

## 5. Final checks before zipping

- [ ] Every document section carries a completed *Performed by / Reviewed by / Edited by* line with
      real names — no `[name]` placeholders remain.
- [ ] No `.env` file, `node_modules/`, `.next/`, or `dist/` folder is inside the archive.
- [ ] Every Markdown file has its matching PDF, and both are the same version.
- [ ] Every relative link and image path in the Markdown resolves inside the extracted package.
- [ ] Git logs exported for all three repositories.
- [ ] Archive is named exactly `PA5-Group04.zip`.
