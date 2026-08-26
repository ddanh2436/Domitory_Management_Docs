# Weekly Report — Sprint 4 (PA4)

> **How to use this file.** The *Repository Activity Record* sections below are generated from the
> commit history of the three project repositories (`Domitory_Management_Docs`,
> `Domitory_Management_Backend`, `Domitory_Management_Frontend`) and every row is verifiable with
> `git log`. The *Scrum Meeting* sections follow the PA3 report format and must be completed by the
> team from the actual meetings — attendance and per-member answers are not derivable from the
> repositories and are deliberately left blank rather than invented.

---

## 1. Repository Activity Record — 01 Aug to 26 Aug 2026

**Performed by:** Đào Duy Anh | **Reviewed by:** Trần Huỳnh Mạnh Đạt | **Edited by:** Đào Duy Anh

### 1.1 Backend — `Domitory_Management_Backend`

| Date | Git author | Change |
| --- | --- | --- |
| 07 Aug 2026 | ddanh2436 | Violation appeal and revocation feature: `violations` module extended with the appeal, review, and revoke flows |
| 07 Aug 2026 | KHÁNH TRẦN HOÀNG QUỐC | Feedback inbox feature and line-ending normalisation |
| 22 Aug 2026 | Loch | End-to-end test scripts implemented (`e2e-tests/`) |
| 25 Aug 2026 | KHÁNH TRẦN HOÀNG QUỐC | `ChangePasswordDto`, `UsersService.changePassword`, and `PATCH /api/users/change-password` |

### 1.2 Frontend — `Domitory_Management_Frontend`

| Date | Git author | Change |
| --- | --- | --- |
| 01 Aug 2026 | Loch | PDF contract export, bill creation card, bulk invoice generation, homepage logo effect |
| 06 Aug 2026 | Loch | UI effects and homepage FAQ section |
| 07 Aug 2026 | ddanh2436 | Violation appeal and revocation screens; feature menu selection |
| 07 Aug 2026 | KHÁNH TRẦN HOÀNG QUỐC | Feedback inbox screens |
| 21 Aug 2026 | Loch | Final UI update |
| 25 Aug 2026 | KHÁNH TRẦN HOÀNG QUỐC | Shared `ChangePasswordForm` plus change-password pages for student, admin, and staff; staff avatar dropdown |

### 1.3 Documentation — `Domitory_Management_Docs`

| Date | Git author | Change |
| --- | --- | --- |
| 07 Aug 2026 | KHÁNH TRẦN HOÀNG QUỐC | Feedback inbox spec-kit; C4 container diagram fix |
| 07 Aug 2026 | Đào Duy Anh | PA4 folder structure and files |
| 08 Aug 2026 | HueoBee | PA4 updates (Use Case Specification AI revision, architecture documents) |
| 21 Aug 2026 | Loch | PA5 folder and section A |
| 22 Aug 2026 | Loch | Test plan and test cases; use cases 1 and 2 tested |

> **Author mapping to confirm.** The git identities `KHÁNH TRẦN HOÀNG QUỐC` and `ddanh2436` /
> `Đào Duy Anh` map to Trần Hoàng Quốc Khánh (24127057) and Đào Duy Anh (24127012). The identities
> `Loch` and `HueoBee` need to be confirmed by the team before submission.

---

## 2. PA4 Deliverables Completed

**Performed by:** Đào Duy Anh | **Reviewed by:** Trần Huỳnh Mạnh Đạt | **Edited by:** Đào Duy Anh

| PA4 part | Deliverable | Status |
| --- | --- | --- |
| A | Revised Use-Case Specification (79 use cases, 12 functional groups) and change log | Complete |
| B | Software architecture — system context diagram (C4 level 1) | Complete |
| C | Container and component diagram (C4 levels 2 and 3) | Complete |
| D | Deployment diagram | Complete |
| E | Spec kit — `002-violation-appeal-revocation` and `003-feedback-inbox` | Complete |
| F | AI Usage Report; this Weekly Report | AI Usage Report complete; meeting minutes below pending |

---

## 3. Features Delivered in Sprint 4

**Performed by:** Đào Duy Anh | **Reviewed by:** Trần Huỳnh Mạnh Đạt | **Edited by:** Đào Duy Anh

| Feature | Requirement | Use cases | Evidence |
| --- | --- | --- | --- |
| Dormify AI assistant | FR31 | UC-AI-01 to UC-AI-04 | `chatbot` module, `KnowledgeSchema`, `ChatFeedbackSchema`, ingestion scripts, `ChatbotWidget.tsx`, `chatbot.service.spec.ts` |
| Violation appeal and revocation | FR32 | UC-COND-06 to UC-COND-08 | `violations` module, spec-kit `002`, `violations.service.spec.ts` |
| Two-way feedback inbox | ST19–ST20 | UC-FBK-01 to UC-FBK-03 | `feedback` module, spec-kit `003` |
| Self-service password change | FR02 | UC-PRO-03 | `PATCH /api/users/change-password`, `ChangePasswordForm.tsx` |

---

## 4. Sprint 4 Scrum Meetings

**To be completed by the team.** Use the PA3 format in `PA3/documents/WeeklyReport-PA3.md`: one block
per meeting, each with the meeting date and title, members present, members absent, a summary of the
meeting, and — for the scrum meetings — each member's answers to the three questions (*What have I
done? What will I do next? What issues or obstacles do I have?*).

### ===== [date] Sprint 4 Planning =====

**Team members present:**

**Team members absent:**

**Summary of the meeting:**

**Task assignments documented on Trello:**

### ===== [date] Sprint 4 Scrum Meeting 1 =====

**Status reports:**

### ===== [date] Sprint 4 Scrum Meeting 2 =====

**Status reports:**

### ===== [date] Sprint 4 Review and Retrospective =====

**Summary:**

**What went well:**

**What to improve:**
