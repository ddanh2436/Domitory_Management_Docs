# Dormify — Software Development Plan

**Dormitory Management System**  
**Version:** 1.0 — Scrum-Aligned PA2  
**Date:** 8 July 2026  
**Development process:** Scrum  
**Delivery structure:** Five sprints, with Sprint 1–5 corresponding to PA1–PA5  
**Repository baseline:** Dormitory Management Frontend and Backend repositories

## Revision History
**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

| Date | Version | Description | Author |
|---|---:|---|---|
| 08 July 2026 | 1.0 | Initial Software Development Plan. | Trần Huỳnh Mạnh Đạt |

> **Supersession note (26 August 2026).** This file is the PA2 baseline and is kept as the historical
> v1.0 record. It has been superseded by `PA3/documents/SoftwareDevelopmentPlan_v1.1.md`, which now
> carries version 1.2 after being synchronised with PA4. The functional scope matrix and the Sprint 4
> backlog in that document record the delivered state, including the Dormify AI assistant (`FR31`),
> the violation appeal and revocation flow (`FR32`), and the two-way feedback inbox.


# 1. Introduction
**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

This Software Development Plan defines how the Dormify team will plan, implement, review, test, and demonstrate the Dormitory Management System by applying Scrum. Development is organized into five sprints, and each sprint corresponds to one course Project Assignment. The plan is an initial PA2 baseline and will be updated in PA3 after teaching-assistant feedback and changes in the Trello board.

Dormify consists of a Next.js frontend and a NestJS/MongoDB backend maintained in separate repositories. The current product areas include authentication, user management, rooms, bookings, contracts, invoices, maintenance, notifications, violations, room transfers, and absences.

## 1.1 Purpose
**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

The purpose of this plan is to:

- Establish the Product Goal, sprint structure, team responsibilities, and delivery rules.
- Define the Product Backlog, Sprint Backlog, Increment, Definition of Ready, and Definition of Done.
- Provide detailed Sprint 2 tasks with one owner, a different reviewer, and a due date.
- Provide planned work for later sprints.
- Define five formal integration builds and the testing purpose of each build.
- Keep the written schedule consistent with Trello.
- Identify project risks and mitigation strategies.
- Ensure that coding, documentation, report writing, testing, and self-training are treated as valid project tasks.

## 1.2 References
**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

- PA1-2026 Project Proposal and App Survey.
- PA2-2026 Project Assignment.
- *NHỮNG TÍNH NĂNG CHÍNH CỦA HỆ THỐNG QUẢN LÝ KTX*.
- Backend repository: https://github.com/ddanh2436/Domitory_Management_Backend
- Frontend repository: https://github.com/ddanh2436/Domitory_Management_Frontend
- Trello tracklog.
- Weekly Reports, AI Usage Report, Spec Kit artifacts, and training evidence.

# 2. Project Overview
**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

## 2.1 Product Goal
**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

The Product Goal is to deliver a secure, role-based web application that centralizes principal dormitory workflows for students and authorized staff. By the final sprint, the team aims to demonstrate an integrated and testable product covering authentication, rooms and bookings, contracts, invoices, maintenance, absences, violations, notifications, room transfers, and selected dashboard capabilities.

## 2.2 Project Objectives
**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Tô Trần Hoàng Triệu | **Edited by:** Trần Huỳnh Mạnh Đạt

1. Deliver usable Product Increments at the end of every sprint.
2. Maintain one ordered Product Backlog in Trello.
3. Assign every task to one owner and one different reviewer.
4. Integrate frontend and backend work at least twice per sprint.
5. Produce five formal builds for testing and review.
6. Pass all critical acceptance scenarios before the PA5 demonstration.
7. Keep all submitted documents in English and Markdown, with Mermaid diagrams where required.
8. Clearly distinguish implemented, partial, planned, and future capabilities.

## 2.3 Scope
**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

### Committed product areas

- Authentication, Google sign-in, JWT, user profiles, account locking, and fixed role-based authorization.
- Room management, room search, booking requests, occupancy control, and contract records.
- Invoice generation, debt status, mock payment, scheduled overdue processing, revenue statistics, and real-time invoice notifications.
- Maintenance requests, status tracking, image evidence, rating, and a planned Maintenance Staff workspace.
- Absence records, violations, behavior scores, announcements, user notifications, and notification retention.
- Transactional room-transfer requests, manager decisions, occupancy updates, contract updates, history, and real-time notifications.
- Responsive student and management interfaces, documentation, testing, deployment rehearsal, and final demonstration.

### Planned or partial capabilities

- Automatic room allocation based on student preferences.
- Contract renewal, termination, check-in/check-out, and verified PDF export.
- Real payment-provider integration.
- Meter readings, meter images, and consumption history.
- Maintenance assignment, before-and-after images, staff history, and a maintenance dashboard.
- Visitor registration, full temporary-residence procedures, feedback, complaints, activity logs, and administrative backup/restore.

### Outside the committed PA2 baseline

- Native mobile applications.
- Offline synchronization.
- Physical access-control hardware.
- Broad integration with unrelated university systems.
- AI maintenance classification and a rules chatbot unless they are approved, estimated, and added to the Product Backlog without displacing critical scope.

## 2.4 Project Deliverables
**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Tô Trần Hoàng Triệu | **Edited by:** Trần Huỳnh Mạnh Đạt

| ID | Deliverable | Sprint | Acceptance evidence |
|---|---|---|---|
| D-01 | PA1 Project Proposal and App Survey | Sprint 1 | Approved product concept, survey, initial features, and team setup |
| D-02 | Vision Document in Markdown and PDF | Sprint 2 | Required sections, feature paragraphs, Mermaid workflows, and measurable quality targets |
| D-03 | Software Development Plan in Markdown and PDF | Sprint 2 | Scrum roles, five sprints, detailed current-sprint tasks, risks, Trello control, and build plan |
| D-04 | Spec Kit initialization artifacts | Sprint 2 | `constitution.md` and generated Markdown files stored inside `/src` |
| D-05 | Spec Kit training evidence | Sprint 2 | Evidence for every team member |
| D-06 | AI Usage Report | Every sprint | Declaration and detailed log when AI tools are used |
| D-07 | Weekly Report | Every sprint | Sprint Planning, Scrum meetings, Sprint Review, and Trello screenshots |
| D-08 | Refined Product Backlog and Use Cases | Sprint 3 | Prioritized stories, acceptance criteria, use cases, and traceability |
| D-09 | Operational Beta and Test Package | Sprint 4 | Integrated workflows, regression results, defects, and known limitations |
| D-10 | Final Product Increment and PA5 Demonstration | Sprint 5 | Accepted features demonstrated from a reproducible build |
| D-11 | Final submission package | Sprint 5 | Markdown, PDF, Git log, Trello evidence, screenshots, reports, and required ZIP structure |

## 2.5 Assumptions and Constraints
**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

| ID | Assumption or constraint | Scrum response |
|---|---|---|
| A-01 | Five team members remain available during the semester. | Plan Sprint Backlogs using demonstrated capacity and re-prioritize when availability changes. |
| A-02 | A Product Owner or authorized representative can clarify dormitory rules. | Do not commit unclear stories until acceptance criteria are sufficient. |
| A-03 | Trello contains the latest task keys, owners, reviewers, due dates, and statuses. | Treat Trello as the operational source of truth and update this plan when it changes. |
| A-04 | Frontend and backend remain in separate repositories. | Integrate frequently and link both repository commits to the same Trello item or build. |
| A-05 | Google authentication, MongoDB, Cloudinary, and deployment services remain available. | Use documented mocks or fallbacks where practical. |
| C-01 | PA deadlines and the final demonstration date are fixed. | Remove or postpone lower-priority Product Backlog items before reducing critical quality work. |
| C-02 | Real payment credentials may be unavailable. | Use the current mock-payment path for demonstration and label real gateways as planned. |
| C-03 | The system handles sensitive information. | Use synthetic data, role checks, secret management, and negative authorization tests. |
| C-04 | PA2 is an initial planning version. | Inspect and adapt the plan in PA3 after feedback and actual velocity evidence. |

# 3. Scrum Organization
**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

## 3.1 Scrum Roles
**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

| Scrum role / contributor | Assigned person or unit | Responsibilities |
|---|---|---|
| Product Owner | Dormify stakeholder representative — name to be confirmed | Owns the Product Goal, orders the Product Backlog, clarifies acceptance criteria, and accepts or rejects Product Increments. |
| Scrum Master | Đào Duy Anh | Facilitates Scrum events, removes impediments, protects the sprint, coordinates Trello discipline, and supports continuous improvement. |
| Developers — Backend / Database | Trần Huỳnh Mạnh Đạt, Đào Duy Anh | Implement APIs, authorization, business rules, database operations, integrations, and backend tests. |
| Developers — Frontend / UI/UX | Hồ Phúc Kiên, Tô Trần Hoàng Triệu, Đào Duy Anh | Implement responsive workflows, frontend validation, API integration, and usability improvements. |
| Quality and Test Lead | Tô Trần Hoàng Triệu | Coordinates test design, acceptance evidence, regression testing, and defect reporting. |
| Documentation and Analysis | Trần Huỳnh Mạnh Đạt | Maintains Vision, Plan, backlog traceability, reports, and submission artifacts. |
| Research and Training Support | Trần Hoàng Quốc Khánh | Maintains training evidence, AI Usage Report support, and research for approved future items. |
| Project Supervisor / Lecturer | Hồ Tuấn Thanh - Mai Anh Tuấn | Reviews academic deliverables and provides course guidance. |

## 3.2 Scrum Artifacts
**Performed by:** Đào Duy Anh | **Reviewed by:** Trần Huỳnh Mạnh Đạt | **Edited by:** Trần Huỳnh Mạnh Đạt

### Product Backlog
**Performed by:** Đào Duy Anh | **Reviewed by:** Trần Huỳnh Mạnh Đạt | **Edited by:** Trần Huỳnh Mạnh Đạt

The Product Backlog contains epics, user stories, bugs, research tasks, documentation, training, and technical work. The Product Owner orders it by business value, risk, dependency, and readiness. Trello stores the current backlog order and task status.

### Sprint Backlog
**Performed by:** Đào Duy Anh | **Reviewed by:** Tô Trần Hoàng Triệu | **Edited by:** Trần Huỳnh Mạnh Đạt

At Sprint Planning, the Developers select Product Backlog Items that support the Sprint Goal and fit the team's forecast capacity. Each selected task has one owner, one different reviewer, acceptance criteria, an estimate, and a due date.

### Product Increment
**Performed by:** Tô Trần Hoàng Triệu | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

The Increment is the integrated frontend, backend, documentation, and test evidence completed during the sprint. It must be usable, reviewed, and compatible with previous accepted work. Incomplete work is not counted as part of the Increment.

## 3.3 Scrum Events
**Performed by:** Đào Duy Anh | **Reviewed by:** Tô Trần Hoàng Triệu | **Edited by:** Trần Huỳnh Mạnh Đạt

| Event | Timing | Expected result |
|---|---|---|
| Sprint Planning | Beginning of each sprint | Sprint Goal, selected Sprint Backlog, task owners, reviewers, estimates, due dates, dependencies, and build target |
| Scrum meeting | At least three times per week or as required by the course | Progress, next work, impediments, integration needs, and risks |
| Backlog Refinement | At least once per week | Clearer stories, acceptance criteria, estimates, dependencies, and updated ordering |
| Sprint Review | End of each sprint | Demonstrated Increment, acceptance decisions, feedback, and Product Backlog updates |
| Sprint Retrospective | After the Sprint Review | Process improvements and concrete actions for the next sprint |

## 3.4 Definition of Ready
**Performed by:** Tô Trần Hoàng Triệu | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

A Product Backlog Item is Ready when:

- Its user or stakeholder value is clear.
- Acceptance criteria are testable.
- Dependencies and affected repositories are identified.
- It has one owner and one different reviewer.
- It can be completed within one sprint.
- The team has enough information to estimate it.
- Required designs, APIs, data, or business decisions are available.

## 3.5 Definition of Done
**Performed by:** Tô Trần Hoàng Triệu | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

A task or Product Backlog Item is Done when:

- Acceptance criteria are satisfied.
- The implementation or document is committed.
- A different team member has reviewed the work.
- Relevant lint, build, unit, integration, or manual acceptance checks pass.
- Security and authorization effects have been considered.
- Documentation and API examples are updated where necessary.
- Trello status and evidence links are updated.
- No unresolved critical defect prevents use.
- The work is integrated into the current Product Increment.

# 4. Risk Management
**Performed by:** Tô Trần Hoàng Triệu | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

| ID | Risk | Probability / impact | Mitigation and contingency | Owner |
|---|---|---|---|---|
| R-01 | Scope creep from real payments, visitor management, AI, backup, or other incomplete features | High / High | Order the Product Backlog using Must/Should/Could; do not add work to an active sprint without agreement; postpone lower-value items. | Product Owner / Scrum Master |
| R-02 | Member unavailability | Medium / High | Forecast work from actual availability, keep tasks small, cross-review critical work, and re-plan the next Sprint Backlog. | Scrum Master |
| R-03 | Frontend and backend integration failure | Medium / High | Integrate at least twice per sprint, maintain shared API examples, and run smoke tests before each formal build. | Technical Lead |
| R-04 | External service failure | Medium / Medium | Preserve mock or sandbox paths and prevent provider failure from corrupting internal records. | Backend / DevOps |
| R-05 | Unauthorized access to sensitive data | Medium / High | Apply JWT and role guards, perform negative authorization tests, protect secrets, and use synthetic data. | QA / Backend |
| R-06 | Schedule differs from Trello | Medium / High | Inspect Trello during Sprint Planning and before submission; revise the plan rather than maintaining conflicting dates. | Scrum Master |
| R-07 | Documents claim features without code or test evidence | High / Medium | Label features as implemented, partial, planned, or future; review the labels at every Sprint Review. | Document Owner / QA |
| R-08 | Insufficient regression testing before PA5 | Medium / High | Maintain five formal builds, add tests incrementally, freeze risky changes before the final build, and rehearse the demonstration. | QA Lead |

# 5. Five-Sprint Product Plan
**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt


| Sprint | Provisional dates | Assignment | Sprint Goal | Planned Product Backlog Items | Sprint Increment |
|---|---|---|---|---|---|
| Sprint 1 | 25 May–14 June 2026 | PA1 | Establish the product concept and development workspace. | Proposal, app survey, initial features, team setup, repositories, and early prototype. | Approved concept and first demonstrable technical baseline. |
| Sprint 2 | 24 June–14 July 2026 | PA2 | Produce compliant planning artifacts and stabilize the current repository baseline. | Vision, Software Development Plan, Mermaid workflows, Spec Kit, training evidence, AI report, weekly report, Trello evidence, authentication review, overdue notifications, and room-transfer verification. | PA2 documentation package and Build 2. |
| Sprint 3 | NULL | PA3 | Refine requirements and complete the accommodation core. | TA feedback, refined Product Backlog, use cases, rooms, bookings, contracts, role authorization, API contracts, and automated tests. | Integrated accommodation workflow and Build 3. |
| Sprint 4 | NULL | PA4 | Complete and integrate operational workflows. | Invoices, overdue processing, maintenance, Maintenance Staff workspace, absences, violations, notifications, dashboards, and regression testing. | Operational Beta and Build 4. |
| Sprint 5 | NULL | PA5 | Harden, deploy, and demonstrate the accepted product. | Room transfers, final integration, security checks, performance checks, UAT, deployment, user guide, release notes, and full demonstration. | Final Product Increment and Build 5. |

A documentation and submission buffer may follow Sprint 5 for approved defect correction, PDF conversion, packaging, Git-log evidence, and submission administration. It does not create another sprint.

## 5.1 Detailed Sprint 2 Backlog
**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

> Add the actual Trello key and status to every item before submission.

| Sprint Backlog Item | Owner | Reviewer | Due date | Done evidence |
|---|---|---|---:|---|
| Audit PA2 requirements against the current documents | Trần Huỳnh Mạnh Đạt | Đào Duy Anh | 10 July 2026 | Compliance checklist |
| Compare the repositories with the documented feature scope | Đào Duy Anh | Trần Huỳnh Mạnh Đạt | 10 July 2026 | Repository alignment matrix |
| Rewrite the Vision Document for Scrum and PA2 | Trần Huỳnh Mạnh Đạt | Tô Trần Hoàng Triệu | 10 July 2026 | Reviewed Markdown file |
| Add two Mermaid workflow diagrams | Hồ Phúc Kiên | Trần Hoàng Quốc Khánh | 10 July 2026 | Rendered diagrams |
| Rewrite the Software Development Plan around five Scrum sprints | Trần Huỳnh Mạnh Đạt | Đào Duy Anh | 10 July 2026 | Reviewed Markdown file |
| Initialize Spec Kit and store generated files in `/src` | Đào Duy Anh | Trần Hoàng Quốc Khánh | 10 July 2026 | Committed Spec Kit files |
| Complete Spec Kit training evidence — Đào Duy Anh | Đào Duy Anh | Trần Hoàng Quốc Khánh | 10 July 2026 | Screenshot or summary |
| Complete Spec Kit training evidence — Trần Huỳnh Mạnh Đạt | Trần Huỳnh Mạnh Đạt | Trần Hoàng Quốc Khánh | 10 July 2026 | Screenshot or summary |
| Complete Spec Kit training evidence — Hồ Phúc Kiên | Hồ Phúc Kiên | Đào Duy Anh | 10 July 2026 | Screenshot or summary |
| Complete Spec Kit training evidence — Tô Trần Hoàng Triệu | Tô Trần Hoàng Triệu | Trần Huỳnh Mạnh Đạt | 10 July 2026 | Screenshot or summary |
| Complete Spec Kit training evidence — Trần Hoàng Quốc Khánh | Trần Hoàng Quốc Khánh | Đào Duy Anh | 10 July 2026 | Screenshot or summary |
| Verify overdue-invoice scheduling and notifications | Trần Huỳnh Mạnh Đạt | Tô Trần Hoàng Triệu | 10 July 2026 | Test evidence |
| Verify room-transfer transaction and notifications | Đào Duy Anh | Trần Huỳnh Mạnh Đạt | 10 July 2026 | End-to-end checklist |
| Verify transfer and notification frontend screens | Hồ Phúc Kiên | Tô Trần Hoàng Triệu | 10 July 2026 | Screenshots and smoke results |
| Prepare the AI Usage Report | Đào Duy Anh | Trần Hoàng Quốc Khánh | 10 July 2026 | Completed report |
| Prepare the Weekly Report and Trello screenshots | Đào Duy Anh | Hồ Phúc Kiên | 10 July 2026 | Meeting records and screenshots |
| Export the Git log and package PA2 files | Trần Huỳnh Mạnh Đạt | Đào Duy Anh | 10 July 2026 | Git evidence and ZIP |
| Conduct the Sprint Review and final submission review | Đào Duy Anh | Hồ Phúc Kiên | 11 July 2026 | Review notes and checklist |

## 5.2 Planned Backlog for Later Sprints
**Performed by:** Đào Duy Anh | **Reviewed by:** Trần Hoàng Quốc Khánh | **Edited by:** Trần Huỳnh Mạnh Đạt

### Sprint 3
**Performed by:** Đào Duy Anh | **Reviewed by:** Trần Hoàng Quốc Khánh | **Edited by:** Trần Huỳnh Mạnh Đạt

- Apply TA feedback to PA2 documents.
- Refine user stories and acceptance criteria.
- Complete room, booking, and contract business rules.
- Add authorization and capacity tests.
- Finalize API contracts between frontend and backend.
- Decide whether contract PDF export is achievable in the sprint.

### Sprint 4
**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

- Complete maintenance assignment and Maintenance Staff workspace.
- Add repair-result and before-and-after evidence.
- Complete absence and violation workflows.
- Complete invoice and overdue regression tests.
- Validate dashboards against API data.
- Conduct security and cross-role regression testing.

### Sprint 5
**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

- Finish accepted room-transfer and operational scope.
- Resolve critical and high-priority defects.
- Run performance and authorization checks.
- Rehearse deployment, backup, and recovery.
- Conduct user acceptance testing.
- Complete user guides, release notes, final report, and demonstration.

# 6. Build and Test Plan
**Performed by:** Đào Duy Anh | **Reviewed by:** Tô Trần Hoàng Triệu | **Edited by:** Trần Huỳnh Mạnh Đạt

The team will create five formal integration builds. Local and pull-request builds may occur more frequently but do not replace the formal build records.

| Build | Target date | Sprint | Scope | Testing purpose |
|---|---:|---|---|---|
| Build 1 — Concept Prototype | 14 June 2026 | Sprint 1 | Initial repositories and representative interface/API foundation | Confirm technical feasibility and repository setup |
| Build 2 — PA2 Baseline | 11 July 2026 | Sprint 2 | Authentication, roles, current modules, overdue notifications, and room-transfer smoke path | Create a stable documented baseline |
| Build 3 — Accommodation Core | NULL | Sprint 3 | Rooms, bookings, contracts, profile, and authorization | End-to-end accommodation and capacity testing |
| Build 4 — Operational Beta | NULL | Sprint 4 | Invoices, maintenance, absences, violations, notifications, and dashboards | Broad regression and stakeholder review |
| Build 5 — Final Demo Release | NULL | Sprint 5 | All accepted features, fixes, deployment, guides, and release notes | PA5 acceptance and complete feature demonstration |

Every formal build records:

- Frontend commit.
- Backend commit.
- Build identifier or tag.
- Environment configuration version.
- Database and test-data version.
- Test result summary.
- Open defects and known limitations.
- Demonstration instructions.

# 7. Trello, Repository, and Configuration Rules
**Performed by:** Đào Duy Anh | **Reviewed by:** Trần Huỳnh Mạnh Đạt | **Edited by:** Trần Huỳnh Mạnh Đạt

- Trello is the source of truth for Product Backlog order, Sprint Backlog membership, task keys, owners, reviewers, estimates, due dates, and status. https://trello.com/invite/b/6a224f42348b85effb90cdb9/ATTI9656023471ab91abfe41300246dfe534A549C230/task-nmcnpm
- Every coding, documentation, training, testing, and report task must exist in Trello.
- A pull request or documented peer review is required before merge.
- Frontend and backend changes for the same story should reference the same Trello item.
- API changes must update request/response examples.
- Secrets and real credentials must not be committed.
- Formal builds should use matching identifiers in both repositories.
- A capability is described as implemented only when code and validation evidence exist.

# 8. Monitoring and Reporting
**Performed by:** Tô Trần Hoàng Triệu | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

| Measure or report | Target / frequency |
|---|---|
| Sprint Goal progress | Reviewed at each Scrum meeting |
| Sprint Backlog completion | Target at least 80%; incomplete work returns to the Product Backlog |
| Independent review coverage | 100% of tasks have a reviewer different from the owner |
| Frontend/backend integration | At least twice per sprint |
| Formal build completion | Five of five planned builds |
| Critical acceptance tests before PA5 | 100% pass |
| Open critical defects before Build 5 | Zero |
| Weekly Report | Every sprint according to course instructions |
| AI Usage Report | Every sprint |
| Risk review | Weekly and at Sprint Review |
| Product Backlog refinement | At least once per week |

# 9. PA2 Submission Checklist
**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

- [ ] Vision Document is in English Markdown.
- [ ] Software Development Plan is in English Markdown.
- [ ] Both documents have PDF versions.
- [ ] The Vision contains two valid Mermaid workflows.
- [ ] Every section includes performer, reviewer, and editor information.
- [ ] The project plan contains exactly five PA-aligned sprints.
- [ ] The current sprint contains detailed tasks, one owner, a different reviewer, and due dates.
- [ ] The build plan contains five formal builds.
- [ ] Trello dates and assignments match this document.
- [ ] Spec Kit files are inside `/src`.
- [ ] Training evidence exists for all members.
- [ ] AI Usage Report is complete.
- [ ] Weekly Report contains Sprint Planning, Scrum meetings, Sprint Review, and Trello screenshots.
- [ ] Git log evidence is included.
- [ ] The final folder is named `PA2-Group[GroupId]`.
- [ ] Markdown, PDF, reports, screenshots, and evidence are included in the ZIP.

*End of Software Development Plan — Version 1.0*
