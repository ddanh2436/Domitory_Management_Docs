# Dormify — Software Development Plan

> Dormitory Management System

| Field | Details |
| --- | --- |
| Version | 1.1 — PA3 |
| Date | 23 July 2026 |
| Development process | Scrum |
| Delivery structure | Five sprints, with Sprint 1–5 corresponding to PA1–PA5 |
| Repositories | Dormitory Management Frontend and Backend |
| Compared version | SDP v1.0 / PA2 baseline |

## Delivery Snapshot

| Review item | Summary |
| --- | --- |
| Current assignment | PA4 (this plan was written for PA3 and synchronised at PA4) |
| Development model | Scrum with five PA-aligned sprints |
| PA3 focus | UI improvement, AI research, missing Admin and Student workflows, and one end-to-end functional group |
| PA4 focus | Software architecture documentation (C4 levels 1-3 and deployment), spec-kit driven feature delivery, and the Dormify AI assistant |
| Selected functional group | Maintenance Management (`FR26-FR28`, `ST17-ST18`, `MT01-MT08`) at PA3; Dormify AI assistance (`FR31`) and violation appeal (`FR32`) at PA4 |
| Formal build target | Build 3 on 25 July 2026 |
| Operational evidence | Trello board, Git logs, Weekly Report, AI Usage Report, Spec Kit artifacts, Markdown files, and PDF exports |

## Revision History

> _Performed by:_ Trần Huỳnh Mạnh Đạt | _Reviewed by:_ Đào Duy Anh | _Edited by:_ Trần Huỳnh Mạnh Đạt

| Date | Version | Description | Author |
| --- | ---: | --- | --- |
| 08 July 2026 | 1.0 | Initial Software Development Plan. | Trần Huỳnh Mạnh Đạt |
| 23 July 2026 | 1.1 | Improve Software Development Plan based on TA's comments. | Trần Huỳnh Mạnh Đạt |
| 26 August 2026 | 1.2 | Synchronised with PA4: added the Dormify AI and violation-appeal scope rows, recorded the delivered Sprint 4 items, and added the spec-kit driven tasks completed during PA4. | Đào Duy Anh |

## Table of Contents

- [Delivery Snapshot](#delivery-snapshot)
- [Revision History](#revision-history)
- [1. Introduction](#1-introduction)
- [2. Project Overview](#2-project-overview)
- [3. Product Scope and Feature Traceability](#3-product-scope-and-feature-traceability)
- [4. Scrum Organization](#4-scrum-organization)
- [5. Five-Sprint Product Plan](#5-five-sprint-product-plan)
- [6. Build and Test Plan](#6-build-and-test-plan)
- [7. Trello, Repository, and Configuration Rules](#7-trello-repository-and-configuration-rules)
- [8. Monitoring and Reporting](#8-monitoring-and-reporting)
- [9. PA3 Submission Checklist](#9-pa3-submission-checklist)

---

## 1. Introduction

> _Performed by:_ Trần Huỳnh Mạnh Đạt | _Reviewed by:_ Đào Duy Anh | _Edited by:_ Trần Huỳnh Mạnh Đạt

This Software Development Plan defines how the Dormify team will plan, implement, review, test, and demonstrate the Dormitory Management System by applying Scrum. Development is organized into five sprints, and each sprint corresponds to one course Project Assignment. The plan is an updated version for PA3 after teaching-assistant feedback and changes in the Trello board.

The previous version was an initial PA2 planning baseline. This PA3 revision incorporates the teaching-assistant feedback as follows:

| Feedback area | PA3 response |
| --- | --- |
| Sprint planning detail | Adds detailed tasks for every sprint instead of only the current sprint. |
| Task traceability | Adds owner, reviewer, estimate, dependency, due date, acceptance criterion, Jira/Trello ID, and evidence fields where applicable. |
| Scope alignment | Aligns the project scope with the revised Use-Case Model. |
| PA3 deliverables | Adds `Changes.md`, use-case specifications, prototypes, Spec Kit artifacts, source code, and the narrated demo video. |
| AI reporting | Expands the AI Usage Report and its audit trail requirements. |
| Document quality | Improves Markdown and PDF consistency. |

### 1.1 Purpose

> _Performed by:_ Trần Huỳnh Mạnh Đạt | _Reviewed by:_ Đào Duy Anh | _Edited by:_ Trần Huỳnh Mạnh Đạt

The purpose of this plan is to:

- Establish the Product Goal, sprint structure, team responsibilities, and delivery rules.
- Define the Product Backlog, Sprint Backlog, Increment, Definition of Ready, and Definition of Done.
- Provide all detailed information of all Sprints, especially Sprint 3 tasks with one owner, a different reviewer, and a due date.
- Provide planned work for later sprints.
- Define five formal integration builds and the testing purpose of each build.
- Keep the written schedule consistent with Trello.
- Identify project risks and mitigation strategies.
- Ensure that coding, documentation, report writing, testing, and self-training are treated as valid project tasks.

### 1.2 References

> _Performed by:_ Trần Huỳnh Mạnh Đạt | _Reviewed by:_ Đào Duy Anh | _Edited by:_ Trần Huỳnh Mạnh Đạt

- PA1-2026 Project Proposal and App Survey.
- PA2-2026 Project Assignment.
- PA3-2026 Project Assignment.
- *NHỮNG TÍNH NĂNG CHÍNH CỦA HỆ THỐNG QUẢN LÝ KTX*.
- Backend repository: [Dormitory Management Backend](https://github.com/ddanh2436/Domitory_Management_Backend).
- Frontend repository: [Dormitory Management Frontend](https://github.com/ddanh2436/Domitory_Management_Frontend).
- Trello tracking log.
- Weekly Reports, AI Usage Report, Spec Kit artifacts, and training evidence.

---

## 2. Project Overview

> _Performed by:_ Trần Huỳnh Mạnh Đạt | _Reviewed by:_ Đào Duy Anh | _Edited by:_ Trần Huỳnh Mạnh Đạt

### 2.1 Product Goal

> _Performed by:_ Trần Huỳnh Mạnh Đạt | _Reviewed by:_ Đào Duy Anh | _Edited by:_ Trần Huỳnh Mạnh Đạt

The Product Goal is to deliver a secure, role-based web application that centralizes primary dormitory workflows for students and authorized staff. By the final sprint, the team aims to demonstrate an integrated and testable product covering authentication, rooms and bookings, contracts, invoices, maintenance, absences, violations, notifications, room transfers, and selected dashboard capabilities.

### 2.2 Project Objectives

> _Performed by:_ Trần Huỳnh Mạnh Đạt | _Reviewed by:_ Đào Duy Anh | _Edited by:_ Trần Huỳnh Mạnh Đạt

1. Deliver usable Product Increments at the end of every sprint.
2. Maintain one ordered Product Backlog in Trello.
3. Assign every task to one owner and one different reviewer.
4. Integrate frontend and backend work at least twice per sprint.
5. Produce five formal builds for testing and review.
6. Pass all critical acceptance scenarios before the PA5 demonstration.
7. Keep all submitted documents in English and Markdown, with Mermaid diagrams where required.
8. Clearly distinguish implemented, partial, planned, and future capabilities.

### 2.3 Technical Baseline

> _Performed by:_ Trần Huỳnh Mạnh Đạt | _Reviewed by:_ Đào Duy Anh | _Edited by:_ Trần Huỳnh Mạnh Đạt

| Layer | Technology or tool | Responsibility |
| --- | --- | --- |
| Frontend | Next.js and TypeScript | Responsive role-based screens and API integration |
| Backend | NestJS and TypeScript | REST APIs, validation, authorization, and business logic |
| Database | MongoDB | Persistent user, room, contract, invoice, request, and audit data |
| Real-time communication | Socket.IO | Notifications and status updates where applicable |
| Authentication | JWT and Google OAuth | Login, authorization, and external authentication |
| File storage | Cloudinary or approved storage service | Prototype and maintenance image evidence |
| Planning | Scrum and Trello | Backlog ordering, sprint planning, and task tracking |
| Specification | Spec Kit and Markdown | Specification, planning, and task artifacts |

### 2.4 Assumptions and Constraints

> _Performed by:_ Trần Huỳnh Mạnh Đạt | _Reviewed by:_ Đào Duy Anh | _Edited by:_ Trần Huỳnh Mạnh Đạt

| ID | Assumption or constraint | Response |
| --- | --- | --- |
| A-01 | Team members remain available during the academic schedule. | Forecast capacity at Sprint Planning and re-plan incomplete work. |
| A-02 | Dormitory rules can be clarified by the project representative or lecturer. | Do not commit unclear items without acceptance criteria. |
| A-03 | Trello contains the latest task owner, reviewer, date, estimate, and status. | Treat Trello as the operational source of truth. |
| A-04 | Frontend and backend are maintained in separate repositories. | Link related frontend and backend changes to one task or story. |
| A-05 | Real payment credentials may not be available. | Use a mock or sandbox payment flow and label real gateways as planned. |
| A-06 | The system handles sensitive student information. | Use synthetic data, role guards, secret management, and negative authorization tests. |
| C-01 | PA deadlines are fixed. | Postpone lower-priority features before reducing critical quality work. |
| C-02 | Some features are specified but not implemented in the current increment. | Mark each capability as implemented, partial, planned, or removed. |

---

## 3. Product Scope and Feature Traceability

> _Performed by:_ Trần Huỳnh Mạnh Đạt | _Reviewed by:_ Đào Duy Anh | _Edited by:_ Trần Huỳnh Mạnh Đạt

### 3.1 Actors and Responsibility Boundaries

> _Performed by:_ Trần Huỳnh Mạnh Đạt | _Reviewed by:_ Đào Duy Anh | _Edited by:_ Trần Huỳnh Mạnh Đạt

| Actor | Responsibility |
| --- | --- |
| Applicant / Guest | Submits preliminary residence information and starts the accommodation process. |
| Student | Manages personal information, room applications, contracts, invoices, residence declarations, repair requests, feedback, and conduct information. |
| System Admin | Manages accounts, roles, permissions, audit logs, and system administration. |
| Dormitory Manager | Manages rooms, students, contracts, checkout, finance, residence, maintenance, feedback, and conduct. |
| Maintenance Staff | Views assigned maintenance work and updates repair status, results, and evidence. |
| Google OAuth Provider | Supports Google authentication. |
| Email / SMS Service | Sends contact notifications and password-reset codes. |
| Payment Gateway | Processes supported online payments. |
| Scheduled Trigger | Starts time-based jobs such as overdue processing and reminders. |

The previously proposed **Floor Manager** role has been removed. Its necessary responsibilities are assigned to the Dormitory Manager.

### 3.2 Functional Scope Matrix

> _Performed by:_ Trần Huỳnh Mạnh Đạt | _Reviewed by:_ Đào Duy Anh | _Edited by:_ Trần Huỳnh Mạnh Đạt

| Functional group | Requirements and features | PA3 status | Related evidence |
| --- | --- | --- | --- |
| Authentication and profile | FR01-FR03, ST01-ST06 | Specified; implementation status must be verified | Auth module, Use-Case Model |
| System administration | FR04-FR07 | Specified; implementation status must be verified | Admin screens and permissions |
| Room and student management | FR09-FR13, ST07-ST10 | Core project scope | Room module and room application tests |
| Contract management | FR14-FR17, ST11-ST12 | Planned or partial | Contract specifications and PDF evidence |
| Checkout and deposit refund | FR18, FR20-FR21 | Planned or partial | Checkout workflow |
| Finance and invoices | FR22-FR24, ST13-ST16 | Planned or partial | Invoice APIs, payment mock, reports |
| Residence management | FR25, ST21-ST24 | Specified; implementation status must be verified | Residence declarations |
| Maintenance management | FR26-FR28, MT01-MT08, ST17-ST18 | Selected PA3 end-to-end functional group | Maintenance specification and implementation |
| Conduct and evaluation | FR29-FR30, FR32, ST27-ST28, ST31 | Implemented, including the PA4 appeal and revocation flow | Conduct records, rules, appeal queue, `violations.service.spec.ts` |
| Feedback and notifications | ST19-ST20, ST25-ST26 | Implemented, including the PA4 two-way feedback inbox | Feedback inbox with management responses, notification workflows |
| Dormify AI assistance | FR31, ST29-ST30, MT09 | Selected PA4 end-to-end functional group | Chatbot module, knowledge and feedback schemas, ingestion scripts, chat widget |

### 3.3 Scope Decisions

> _Performed by:_ Trần Huỳnh Mạnh Đạt | _Reviewed by:_ Đào Duy Anh | _Edited by:_ Trần Huỳnh Mạnh Đạt

- **FR08 - Back up and restore data** is removed from the current user-facing project scope.
- **FR19 - Inspect assets** is removed as a standalone use case. Damaged or missing items may be entered manually during checkout when compensation is calculated.
- **FR24 - Generate revenue reports** remains in scope as a planned management capability.
- The Floor Manager actor is removed. Meter, residence, room, conduct, feedback, and repair-monitoring responsibilities are assigned to the Dormitory Manager.
- Real payment providers remain planned unless the team has a working sandbox integration.
- A capability is described as implemented only when code, review, and validation evidence exist.

#### Planned or Partial Capabilities

- Automatic room allocation based on student preferences.
- Contract renewal, termination, check-in/check-out, and verified PDF export.
- Real payment-provider integration.
- Meter readings, meter images, and consumption history.
- Maintenance assignment, before-and-after images, staff history, and a maintenance dashboard.
- Visitor registration, full temporary-residence procedures, feedback, complaints and activity logs.
- Administrative backup/restore has been removed from the committed project scope.

#### Outside the Committed PA3 Scope

- Native mobile applications.
- Offline synchronization.
- Physical access-control hardware.
- Broad integration with unrelated university systems.
- AI maintenance classification and a rules chatbot unless they are approved, estimated, and added to the Product Backlog without displacing critical scope.

---

## 4. Scrum Organization

> _Performed by:_ Trần Huỳnh Mạnh Đạt | _Reviewed by:_ Đào Duy Anh | _Edited by:_ Trần Huỳnh Mạnh Đạt

### 4.1 Scrum Roles

> _Performed by:_ Trần Huỳnh Mạnh Đạt | _Reviewed by:_ Đào Duy Anh | _Edited by:_ Trần Huỳnh Mạnh Đạt

| Scrum role | Assigned member | Responsibilities |
| --- | --- | --- |
| Scrum Master | Đào Duy Anh | Facilitates Scrum events, removes impediments, and maintains planning discipline. |
| Backend / Database | Trần Huỳnh Mạnh Đạt, Đào Duy Anh and Trần Hoàng Quốc Khánh | APIs, authorization, business rules, database operations, and backend tests. |
| Frontend / UI/UX | Hồ Phúc Kiên and Đào Duy Anh | Responsive screens, validation, API integration, and usability. |
| Quality and Testing | Trần Huỳnh Mạnh Đạt | Test design, acceptance evidence, regression testing, and defect reporting. |
| Documentation and Analysis | Trần Huỳnh Mạnh Đạt | Vision, SDP, traceability, reports, and submission artifacts. |
| Research and Training | Trần Hoàng Quốc Khánh | Training evidence, approved research, and AI report support. |

#### Team Membership Update

| Member | Updated status | Reason | Planning impact |
| --- | --- | --- | --- |
| Tô Trần Hoàng Triệu | Removed from the active project group in the PA3 revision. | The member did not provide meaningful contribution, repeatedly missed agreed deadlines, and submitted work that required significant correction and rework by other members. | Future responsibilities and review duties are reassigned to active members; prior evidence involving this member must be checked before submission. |

### 4.2 Scrum Artifacts and Events

> _Performed by:_ Trần Huỳnh Mạnh Đạt | _Reviewed by:_ Đào Duy Anh | _Edited by:_ Trần Huỳnh Mạnh Đạt

| Artifact or event | Required content or result | Evidence |
| --- | --- | --- |
| Product Backlog | Epics, user stories, bugs, research, documentation, training, and technical tasks ordered by value and risk. | Trello board export |
| Sprint Backlog | Selected tasks with owner, reviewer, estimate, dependency, due date, acceptance criteria, and status. | Sprint board screenshot |
| Product Increment | Integrated and reviewed work that satisfies the Definition of Done. | Build record and demonstration |
| Sprint Planning | Sprint Goal, selected backlog items, capacity, dependencies, and build target. | Weekly Report |
| Scrum meeting | Progress, next work, impediments, integration needs, and risks. | Meeting notes |
| Sprint Review | Demonstrated increment, feedback, acceptance decisions, and backlog updates. | Review record and screenshots |
| Sprint Retrospective | Improvement actions for the next sprint. | Retrospective notes |

### 4.3 Definition of Ready

> _Performed by:_ Trần Huỳnh Mạnh Đạt | _Reviewed by:_ Đào Duy Anh | _Edited by:_ Trần Huỳnh Mạnh Đạt

A backlog item is ready when its value and actor are clear, acceptance criteria are testable, dependencies are identified, one owner and one different reviewer are assigned, and the team can estimate and complete it within a sprint.

### 4.4 Definition of Done

> _Performed by:_ Trần Huỳnh Mạnh Đạt | _Reviewed by:_ Đào Duy Anh | _Edited by:_ Trần Huỳnh Mạnh Đạt

A task is done when its acceptance criteria are satisfied, the result is committed, a different team member has reviewed it, relevant tests pass, security effects are considered, documentation is updated, Trello evidence is linked, and no unresolved critical defect prevents use.

---

## 5. Five-Sprint Product Plan


> _Performed by:_ Trần Huỳnh Mạnh Đạt | _Reviewed by:_ Đào Duy Anh | _Edited by:_ Trần Huỳnh Mạnh Đạt

| Sprint | Sample dates | Assignment | Sprint Goal | Main increment |
| --- | --- | --- | --- | --- |
| Sprint 1 | 23 May – 06 June 2026 | PA1 | Establish the product concept and project workspace. | Approved proposal, survey, repositories, and initial prototype. |
| Sprint 2 | 06 June – 11 July 2026 | PA2 | Establish authentication, authorization, profile, room-management foundation, and planning baselines. | Authentication/core workflows, Vision, SDP, Spec Kit setup, reports, and baseline integration. |
| Sprint 3 | 11 – 25 July 2026 | PA3 | Improve the UI, research AI capabilities, complete missing Admin and Student features, and implement one end-to-end functional group. | Improved UI, AI research/POC, Admin and Student workflows, Use-Case Model, specifications, Maintenance Management increment, and Build 3. |
| Sprint 4 | 25 July – 08 August 2026 | PA4 | Integrate finance, residence, conduct, notifications, and operational workflows. | Operational Beta and Build 4. |
| Sprint 5 | 08 – 22 August 2026 | PA5 | Harden, deploy, and demonstrate the accepted product. | Final Product Increment, UAT, release evidence, and Build 5. |

### 5.1 Sprint Task Standard

> _Performed by:_ Trần Huỳnh Mạnh Đạt | _Reviewed by:_ Đào Duy Anh | _Edited by:_ Trần Huỳnh Mạnh Đạt

Every sprint backlog uses a consistent task schema so reviewers can scan ownership, review responsibility, traceability, estimates, dependencies, acceptance criteria, and delivery status.

| Field | Display rule |
| --- | --- |
| Task ID | Use the sprint-prefix key from Trello or the SDP backlog. |
| Requirement / UC | Link the task to the relevant functional requirement, story, use case, or assignment item when applicable. |
| Detailed task | State the work as a concrete action with an observable outcome. |
| Owner | Assign exactly one accountable owner. |
| Reviewer | Assign one reviewer who is different from the owner. |
| Estimate | Record the planned effort in hours. |
| Dependency | Identify prerequisite work or evidence when the task depends on another item. |
| Acceptance criteria | Define how the team will know the task is complete. |
| Status | Use a consistent state such as Completed, In Progress, or Planned. |

| Status | Meaning |
| --- | --- |
| Completed | Acceptance criteria are satisfied and review evidence is available. |
| In Progress | Work has started and still needs completion, review, or final evidence. |
| Planned | Work is forecast for a later sprint or has not started yet. |

### 5.2 Sprint 1 Backlog – Project Foundation

> _Performed by:_ Trần Huỳnh Mạnh Đạt | _Reviewed by:_ Đào Duy Anh | _Edited by:_ Trần Huỳnh Mạnh Đạt

| Task ID | Detailed task | Owner | Reviewer | Estimate | Acceptance criteria | Status |
| --- | --- | --- | --- | ---: | --- | --- |
| S1-PRO-01 | Define the Dormify problem, target users, and business goals. | Đạt | Duy Anh | 4h | Proposal contains approved problem and target-user statements. | Completed |
| S1-PRO-02 | Survey existing dormitory-management applications and alternatives. | Kiên | Duy Anh | 5h | Survey evidence and comparison table are included. | Completed |
| S1-PRO-03 | Create frontend and backend repositories with a documented baseline. | Duy Anh | Đạt | 3h | Both repositories can be cloned and started. | Completed |
| S1-PRO-04 | Create the initial feature list and actor list. | Đạt | Duy Anh | 4h | Initial features are recorded and traceable to the proposal. | Completed |
| S1-PRO-05 | Create the initial prototype and project presentation. | Khánh | Triệu | 6h | Prototype demonstrates the selected product direction. | Completed |

### 5.3 Sprint 2 Backlog – PA2 Planning Baseline

> _Performed by:_ Trần Huỳnh Mạnh Đạt | _Reviewed by:_ Đào Duy Anh | _Edited by:_ Trần Huỳnh Mạnh Đạt

| Task ID | Requirement / UC | Detailed task | Owner | Reviewer | Estimate | Dependency | Acceptance criteria | Status |
| --- | --- | --- | --- | --- | ---: | --- | --- | --- |
| S2-DOC-01 | PA2 | Audit PA2 requirements against the Vision Document and SDP. | Đạt | Duy Anh | 5h | PA2 rubric | Compliance checklist is complete. | Completed |
| S2-AUTH-01 | FR02-FR03 | Define the user, credential, role, permission, and session data model. | Duy Anh | Đạt | 5h | Repository baseline | Required fields and role boundaries are documented. | Completed |
| S2-AUTH-02 | FR01 | Implement preliminary residence-profile submission for an Applicant / Guest. | Duy Anh | Đạt | 6h | S2-AUTH-01 | Valid applicant information is stored and an acknowledgment is returned. | Completed |
| S2-AUTH-03 | FR02 | Implement email/CCCD login, logout, JWT validation, and Google OAuth flow. | Duy Anh | Đạt | 10h | S2-AUTH-01 | Valid users can log in and invalid credentials are rejected. | Completed |
| S2-AUTH-04 | FR02 | Implement forgotten-password and OTP verification flow. | Đạt | Khánh | 6h | S2-AUTH-03 | OTP expiry, invalid OTP, and successful password reset are tested. | Completed |
| S2-PRO-01 | FR03 | Implement view profile, update contact information, and change password. | Duy Anh | Đạt | 6h | S2-AUTH-03 | Users can update allowed fields and unauthorized identity changes are blocked. | Completed |
| S2-ADM-01 | FR05 | Create the initial role and permission matrix for Admin, Student, Dormitory Manager, and Maintenance Staff. | Đạt | Duy Anh | 4h | S2-AUTH-01 | Each protected feature has an identified permitted role. | Completed |
| S2-ROOM-01 | FR09 | Define building, floor, room, bed, capacity, and occupancy data structures. | Duy Anh | Triệu | 6h | S2-AUTH-01 | Room capacity and bed availability can be represented consistently. | Completed |
| S2-ROOM-02 | ST07-ST08 | Implement room search, filtering, room details, and available-bed display. | Kiên | Duy Anh | 8h | S2-ROOM-01 | Student can filter by room type, building, price, and available beds. | Completed |
| S2-NOT-01 | ST25-ST26 | Define the notification model and basic user-notification delivery path. | Duy Anh | Triệu | 5h | S2-AUTH-03 | Notifications are associated with the correct authorized user. | Completed |
| S2-DOC-02 | PA2 | Write the Vision Document with functional and non-functional requirements. | Đạt | Duy Anh | 10h | S2-DOC-01 | Required sections and quality targets are present. | Completed |
| S2-DOC-03 | PA2 | Write the SDP around Scrum roles, artifacts, events, and five sprints. | Đạt | Duy Anh | 8h | S2-DOC-01 | SDP contains a consistent schedule and evidence rules. | Completed |
| S2-SPEC-01 | PA2 | Initialize Spec Kit and store the generated artifacts in the approved project path. | Duy Anh | Khánh | 4h | Repository baseline | Spec, plan, and task files are committed. | Completed |
| S2-TEST-01 | FR01-FR03 | Test registration, login, logout, OTP, profile authorization, and core room queries. | Đạt | Triệu | 8h | S2-AUTH-04, S2-PRO-01, S2-ROOM-02 | Test results and known limitations are recorded. | Completed |
| S2-REPORT-01 | PA2 | Prepare the AI Usage Report and Weekly Report. | Duy Anh | Khánh | 6h | Sprint evidence | Reports contain required logs and screenshots. | Completed |
| S2-EVID-01 | PA2 | Export Git log, Trello screenshots, and the PA2 package. | Đạt | Duy Anh | 3h | Final documents | Evidence is included in the PA2 folder. | Completed |

### 5.4 Sprint 3 Backlog – PA3 and Maintenance Management

> _Performed by:_ Trần Huỳnh Mạnh Đạt | _Reviewed by:_ Đào Duy Anh | _Edited by:_ Trần Huỳnh Mạnh Đạt

**Selected PA3 functional group:** Maintenance Management (`FR26-FR28`, `ST17-ST18`, and `MT01-MT08`).

| Workstream | Task IDs | Review focus |
| --- | --- | --- |
| Requirements and specification | S3-REQ, S3-UC, S3-SPEC | Maintenance actors, lifecycle, use cases, and complete specifications. |
| UI and shared experience | S3-UI, S3-STU, S3-FE | Consistent screens, shared components, student workflows, and staff workspaces. |
| Admin capabilities | S3-ADM | Account, permission, audit log, and dashboard coverage. |
| Maintenance implementation | S3-DB, S3-BE, S3-INT, S3-TEST | Data model, APIs, integration, image evidence, authorization, and test results. |
| AI research | S3-AI | Research, dataset definition, RAG architecture, risk review, and POC evidence. |
| Submission evidence | S3-PA3 | `Changes.md`, reports, screenshots, Git/Trello evidence, Markdown, and PDF outputs. |

| Task ID | Requirement / UC | Detailed task | Owner | Reviewer | Estimate | Dependency | Acceptance criteria | Status |
| --- | --- | --- | --- | --- | ---: | --- | --- | --- |
| S3-REQ-01 | FR26-FR28 | Refine maintenance actors, request lifecycle, priorities, and statuses. | Đạt | Duy Anh | 4h | Vision feedback | Approved requirements identify Student, Dormitory Manager, and Maintenance Staff flows. | Completed |
| S3-UI-01 | All roles | Audit the current UI for inconsistent spacing, typography, colors, navigation, and terminology. | Kiên | Đạt | 5h | Existing screens | UI issue list is prioritized by user impact. | Completed |
| S3-UI-02 | All roles | Create a shared design system for buttons, forms, tables, cards, status badges, and modal dialogs. | Kiên | Duy Anh | 8h | S3-UI-01 | Shared components are reused in at least the Student and Admin screens. | Completed |
| S3-UI-03 | All roles | Add consistent loading, empty, validation-error, success, and unauthorized states. | Kiên | Đạt | 6h | S3-UI-02 | Every selected workflow has documented state handling. | Completed |
| S3-UI-04 | Admin / Student | Improve dashboard navigation, filters, data tables, and accessibility labels. | Kiên | Duy Anh | 8h | S3-UI-03 | Main screens pass the team's visual and accessibility checklist. | Completed |
| S3-AI-01 | AI-01 | Research NLP approaches for maintenance-category and priority classification. | Khánh | Duy Anh | 6h | S3-REQ-01 | Research compares feasible models, data needs, latency, cost, and limitations. | Planned |
| S3-AI-02 | AI-01 | Define a labeled sample dataset and taxonomy for Plumbing, Electrical, Carpentry, Normal, and Urgent tickets. | Đạt | Khánh | 5h | S3-AI-01 | Dataset format and labeling rules are documented with example records. | Completed |
| S3-AI-03 | AI-02 | Research RAG architecture, embedding storage, chunking, retrieval, citations, and fallback behavior. | Khánh | Duy Anh | 8h | Approved rulebook sources | Architecture decision and data-flow diagram are documented. | In Progress |
| S3-AI-04 | AI-01, AI-02 | Review privacy, hallucination, prompt-injection, confidence, human-override, and escalation risks. | Khánh | Duy Anh | 5h | S3-AI-01, S3-AI-03 | AI risk register and mitigation rules are approved. | Completed |
| S3-AI-05 | AI-01, AI-02 | Create a small proof-of-concept or mock evaluation for both proposed AI features. | Duy Anh | Khánh | 8h | S3-AI-02, S3-AI-03 | POC output, test queries, limitations, and recommendation are recorded. | Planned |
| S3-ADM-01 | FR04 | Implement Admin account list, search, view, activate, deactivate, lock, and unlock actions. | Duy Anh | Đạt | 8h | S2-AUTH-03 | Admin can manage accounts according to the permission matrix. | Completed |
| S3-ADM-02 | FR05 | Implement role and permission assignment for System Admin and Dormitory Manager. | Duy Anh | Triệu | 8h | S2-ADM-01 | Unauthorized users cannot change roles or permissions. | Completed |
| S3-ADM-03 | FR06 | Implement audit-log recording, filtering, and detail view. | Đạt | Duy Anh | 8h | S3-ADM-01 | Important account and permission actions produce searchable audit entries. | Completed |
| S3-ADM-04 | FR07 | Improve the administration dashboard with account, role, warning, and activity summaries. | Kiên | Đạt | 6h | S3-ADM-01, S3-UI-05 | Dashboard values match approved backend data. | Completed |
| S3-STU-01 | ST01-ST06 | Complete student profile, contact information, current residence, roommate, and residence-history screens. | Kiên | Duy Anh | 8h | S2-PRO-01 | Student sees only their authorized personal and residence information. | Completed |
| S3-STU-02 | ST07-ST10 | Complete room details, application submission, application status, and room-transfer status screens. | Kiên | Đạt | 10h | S2-ROOM-02 | Student can submit and track an approved room workflow. | Completed |
| S3-STU-03 | ST11-ST12 | Complete contract viewing and contract PDF download screens. | Kiên | Triệu | 6h | Contract API | Student can view only their own contract and download the approved file. | Completed |
| S3-STU-04 | ST13-ST16 | Complete invoice viewing, payment, payment history, and invoice PDF screens. | Kiên | Duy Anh | 8h | Invoice API | Student can see invoice status and payment history correctly. | Completed |
| S3-STU-05 | ST17-ST18 | Complete student maintenance-request submission, attachment, and progress tracking. | Kiên | Đạt | 8h | S3-REQ-01 | Student can submit a repair request and track its status. | Completed |
| S3-STU-06 | ST19-ST24 | Complete feedback, suggestion, overnight absence, temporary residence, temporary absence, and visitor screens. | Kiên | Khánh | 10h | Residence and feedback requirements | Each submission has validation, status, and confirmation behavior. | Completed |
| S3-STU-07 | ST25-ST26 | Complete notification list, read state, and message-center screens. | Kiên | Triệu | 6h | S2-NOT-01 | Student receives and reads authorized notifications. | Completed |
| S3-STU-08 | ST27-ST28 | Complete evaluation-history and violation-history screens. | Kiên | Đạt | 5h | Conduct API | Student can view only their own records. | Completed |
| S3-UC-01 | UC-MNT-01 to UC-MNT-10 | Create Mermaid maintenance use-case diagram with actors and relationships. | Đạt | Duy Anh | 4h | S3-REQ-01 | Diagram covers submit, assign, track, update, evidence, history, and dashboard flows. | In Progress |
| S3-SPEC-01 | UC-MNT-01 to UC-MNT-10 | Write maintenance basic flows, alternatives, preconditions, postconditions, and special requirements. | Đạt | Kiên | 10h | S3-UC-01 | Every maintenance use case has a complete specification. | Completed |
| S3-DB-01 | FR26-FR28 | Define maintenance request, assignment, status, result, rating, and image fields. | Duy Anh | Đạt | 5h | S3-REQ-01 | Schema validates required fields and status transitions. | Completed |
| S3-BE-01 | FR26-FR28 | Implement the API for submitting and viewing repair requests. | Duy Anh | Triệu | 8h | S3-DB-01 | Authorized students can create and track their own requests. | Completed |
| S3-BE-02 | MT01-MT04 | Implement manager assignment and maintenance-staff status updates. | Duy Anh | Đạt | 8h | S3-BE-01 | Only authorized roles can assign or update repair work. | Completed |
| S3-BE-03 | MT05-MT08 | Implement repair-result, image-evidence, history, and dashboard endpoints. | Duy Anh | Triệu | 8h | S3-BE-02 | Results and before/after evidence are persisted and retrievable. | Completed |
| S3-FE-01 | MT01-MT08 | Implement manager and maintenance-staff workspaces using the shared UI components. | Kiên | Đạt | 10h | S3-UI-02, S3-BE-02 | Staff can view assigned jobs and update repair progress. | Completed |
| S3-INT-01 | FR26-FR28 | Integrate maintenance frontend, backend, database, and image storage. | Triệu | Duy Anh | 6h | S3-BE-03, S3-FE-01 | End-to-end maintenance workflow persists data after refresh. | Completed |
| S3-TEST-01 | FR26-FR28 | Test authorization, validation, status transitions, image upload, and error handling. | Đạt | Triệu | 8h | S3-INT-01 | All acceptance scenarios pass and defects have reproduction steps. | Completed |
| S3-PA3-01 | PA3 | Complete `Changes.md`, AI Usage Report, Weekly Report, and Git/Trello evidence. | Đạt | Duy Anh | 6h | Sprint evidence | All PA3 documents are included in Markdown and PDF form. | Completed |

### 5.5 Sprint 4 Backlog – Operational Workflows

> _Performed by:_ Trần Huỳnh Mạnh Đạt | _Reviewed by:_ Đào Duy Anh | _Edited by:_ Trần Huỳnh Mạnh Đạt

| Task ID | Requirement group | Detailed task | Owner | Reviewer | Estimate | Acceptance criteria | Status |
| --- | --- | --- | --- | --- | ---: | --- | --- |
| S4-FIN-01 | FR22-FR24, ST13-ST16 | Implement invoice generation, invoice viewing, payment mock, and payment history. | Duy Anh | Đạt | 10h | Student can view invoices and complete the approved payment flow. | Planned |
| S4-FIN-02 | FR23-FR24 | Implement debt summary, overdue processing, reminders, and revenue report data. | Duy Anh | Đạt | 8h | Overdue invoices and reminders are generated according to the documented rule. | Planned |
| S4-RES-01 | FR25, ST21-ST24 | Implement overnight absence, temporary residence, temporary absence, and visitor workflows. | Kiên | Đạt | 8h | Student submissions are stored and visible to the Dormitory Manager. | Planned |
| S4-COND-01 | FR29-FR30, ST27-ST28 | Implement violation records, conduct-score rules, and evaluation history. | Đạt | Duy Anh | 8h | Rules are applied consistently and history is visible to authorized users. | Done — `violations` module with conduct-score deduction and student history |
| S4-COND-02 | FR32, ST31 | Implement the violation appeal queue: student appeal with reason, management accept/reject with note, score restoration, and direct revocation of a mistaken record. | Duy Anh | Đạt | 8h | An accepted appeal revokes the violation and restores the deducted points exactly once, capped at the maximum score; a rejected appeal leaves the score unchanged. | Done — spec-kit `002-violation-appeal-revocation`, `UC-COND-06` to `UC-COND-08`, unit tests in `violations.service.spec.ts` |
| S4-NOT-01 | ST25-ST26 | Integrate notifications and message-center updates for important events. | Duy Anh | Khánh | 6h | Relevant status changes create the documented notification. | Done — Socket.IO gateway with per-user rooms and TTL-managed notification records |
| S4-FBK-01 | ST19-ST20 | Implement the two-way feedback inbox: student submission plus management response. | Kiên | Đạt | 6h | A student can submit feedback and read the management response on the same record. | Done — spec-kit `003-feedback-inbox`, `feedback` module with the respond endpoint |
| S4-AI-01 | AI-01 | Build a labeled maintenance-ticket evaluation set and measure category, priority, and routing performance. | Đạt | Khánh | 6h | Evaluation results and known failure cases are documented. | Planned |
| S4-AI-02 | AI-01 | Integrate Smart Ticketing predictions into the manager maintenance queue with confidence display and manual override. | Duy Anh | Đạt | 8h | Manager can accept or correct AI output, and the correction is auditable. | Planned |
| S4-AI-03 | AI-02, FR31 | Collect and approve rulebooks, FAQs, and procedures for the RAG knowledge base. | Khánh | Đạt | 5h | Every indexed source is approved, versioned, and traceable. | Done — Markdown knowledge sources with the ingestion pipeline and `KnowledgeSchema` |
| S4-AI-04 | AI-02, FR31 | Prototype retrieval, grounded response, source citation, and fallback behavior for the virtual assistant. | Duy Anh | Kiên | 8h | Test questions receive grounded answers or a safe escalation response. | Done — vector plus keyword retrieval, streamed answers, source chips, and a not-found response instead of an invented one |
| S4-AI-05 | FR31, ST29-ST30 | Add answer feedback, administrator feedback review, and knowledge-base rebuild for Dormify AI. | Khánh | Duy Anh | 5h | A user can rate an answer, an administrator can review the negative ratings, and the knowledge base can be rebuilt after the documents change. | Done — `UC-AI-02` to `UC-AI-04` |
| S4-INT-01 | All operational groups | Integrate finance, residence, conduct, maintenance, and notification workflows. | Duy Anh | Đạt | 8h | Regression smoke tests pass across the integrated modules. | Planned |
| S4-TEST-01 | All operational groups | Run role, API, database, and regression tests. | Đạt | Duy Anh | 8h | No unresolved critical defect remains before Build 4. | Planned |

### 5.6 Sprint 5 Backlog – Release and Demonstration

> _Performed by:_ Trần Huỳnh Mạnh Đạt | _Reviewed by:_ Đào Duy Anh | _Edited by:_ Trần Huỳnh Mạnh Đạt

| Task ID | Release activity | Owner | Reviewer | Estimate | Acceptance criteria | Status |
| --- | --- | --- | --- | ---: | --- | --- |
| S5-INT-01 | Complete final frontend/backend integration and resolve high-priority defects. | Duy Anh | Đạt | 10h | All accepted critical workflows pass the regression suite. | Planned |
| S5-SEC-01 | Run authentication, authorization, input-validation, and secret-management checks. | Đạt | Duy Anh | 8h | No critical unauthorized-access path remains open. | Planned |
| S5-PERF-01 | Run basic performance checks for key APIs and dashboards. | Khánh | Duy Anh | 5h | Results are recorded against the documented targets. | Planned |
| S5-UAT-01 | Conduct user acceptance testing with representative scenarios. | Đạt | Kiên | 8h | UAT scenarios are accepted or have documented limitations. | Planned |
| S5-DEP-01 | Rehearse deployment and application-environment recovery. | Duy Anh | Khánh | 5h | The application can be deployed using documented steps. | Planned |
| S5-DOC-01 | Complete user guide, release notes, final reports, and PDF conversion. | Đạt | Duy Anh | 8h | All documents are readable, consistent, and linked from README. | Planned |
| S5-DEMO-01 | Record the narrated PA5 demonstration video. | Kiên | Đạt | 5h | Video demonstrates the selected functional group and alternative flows. | Planned |
| S5-PKG-01 | Package Markdown, PDF, source code, evidence, and Git log into the required ZIP. | Đạt | Duy Anh | 4h | ZIP follows `PA5-Group[GroupId]` structure and excludes generated folders. | Planned |

---

## 6. Build and Test Plan

> _Performed by:_ Đào Duy Anh | _Reviewed by:_ Trần Hoàng Quốc Khánh | _Edited by:_ Trần Huỳnh Mạnh Đạt

The team will create five formal integration builds. Local and pull-request builds may occur more frequently but do not replace the formal build records.

| Build | Target date | Sprint | Scope | Testing purpose |
| --- | ---: | --- | --- | --- |
| Build 1 — Concept Prototype | 06 June 2026 | Sprint 1 | Initial repositories and representative interface/API foundation | Confirm technical feasibility and repository setup |
| Build 2 — PA2 Baseline | 11 July 2026 | Sprint 2 | Authentication, roles, current modules, overdue notifications, and room-transfer smoke path | Create a stable documented baseline |
| Build 3 — PA3 Maintenance Increment | 25 July 2026 | Sprint 3 | Maintenance requests, assignment, status updates, image evidence, Admin/Student UI improvements, and authorization regression | End-to-end maintenance workflow, role authorization, and integration testing |
| Build 4 — Operational Beta | 08 August 2026 | Sprint 4 | Invoices, maintenance, absences, violations, notifications, and dashboards | Broad regression and stakeholder review |
| Build 5 — Final Demo Release | 22 August 2026 | Sprint 5 | All accepted features, fixes, deployment, guides, and release notes | PA5 acceptance and complete feature demonstration |

### 6.1 Build Record Checklist

Every formal build records the following evidence:

| Evidence item | Purpose |
| --- | --- |
| Frontend commit | Identifies the exact frontend source state. |
| Backend commit | Identifies the exact backend source state. |
| Build identifier or tag | Gives the build a stable reference for review. |
| Environment configuration version | Records the runtime and deployment settings. |
| Database and test-data version | Keeps tests reproducible. |
| Test result summary | Shows what passed, failed, or needs retesting. |
| Open defects and known limitations | Makes unresolved risk visible. |
| Demonstration instructions | Allows reviewers to repeat the demo path. |

### 6.2 Quality Gate Summary

| Gate | Minimum evidence |
| --- | --- |
| Integration | Frontend, backend, database, and storage paths run together for the selected workflows. |
| Authorization | Student, Admin, Dormitory Manager, and Maintenance Staff access rules are checked. |
| Validation | Required fields, invalid inputs, file evidence, and status transitions are tested. |
| Regression | Previously completed authentication, room, profile, and notification paths remain usable. |
| Documentation | SDP, Vision, use-case specifications, reports, and evidence package are updated. |

---

## 7. Trello, Repository, and Configuration Rules

> _Performed by:_ Đào Duy Anh | _Reviewed by:_ Trần Huỳnh Mạnh Đạt | _Edited by:_ Trần Huỳnh Mạnh Đạt

- Trello is the source of truth for Product Backlog order, Sprint Backlog membership, task keys, owners, reviewers, estimates, due dates, and status. [Trello board](https://trello.com/invite/b/6a224f42348b85effb90cdb9/ATTI9656023471ab91abfe41300246dfe534A549C230/task-nmcnpm).
- Every coding, documentation, training, testing, and report task must exist in Trello.
- A pull request or documented peer review is required before merge.
- Frontend and backend changes for the same story should reference the same Trello item.
- API changes must update request/response examples.
- Secrets and real credentials must not be committed.
- Formal builds should use matching identifiers in both repositories.
- A capability is described as implemented only when code and validation evidence exist.

---

## 8. Monitoring and Reporting

> _Performed by:_ Trần Huỳnh Mạnh Đạt | _Reviewed by:_ Đào Duy Anh | _Edited by:_ Trần Huỳnh Mạnh Đạt

| Measure or report | Target / frequency |
| --- | --- |
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

---

## 9. PA3 Submission Checklist

> _Performed by:_ Trần Huỳnh Mạnh Đạt | _Reviewed by:_ Đào Duy Anh | _Edited by:_ Trần Huỳnh Mạnh Đạt

- [x] Vision Document is in English Markdown.
- [x] Software Development Plan is in English Markdown.
- [x] Both documents have PDF versions.
- [x] The Vision contains two valid Mermaid workflows.
- [x] Every section includes performer, reviewer, and editor information.
- [x] The project plan contains exactly five PA-aligned sprints.
- [x] The current sprint contains detailed tasks, one owner, a different reviewer, and due dates.
- [x] The build plan contains five formal builds.
- [x] Trello dates and assignments match this document.
- [x] Spec Kit files are inside `/src`.
- [x] Training evidence exists for all members.
- [x] AI Usage Report is complete.
- [x] Weekly Report contains Sprint Planning, Scrum meetings, Sprint Review, and Trello screenshots.
- [x] Git log evidence is included.
- [x] The final folder is named `PA3-Group[GroupId]`.
- [x] Markdown, PDF, reports, screenshots, and evidence are included in the ZIP.

*End of Software Development Plan — Version 1.1*
