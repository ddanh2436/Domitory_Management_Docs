# PA3-2026 Change Log

**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt


## Contents

1. [Summary of TA Feedback and Revisions](#1-summary-of-ta-feedback-and-revisions)
2. [Revised Software Development Plan](#2-revised-software-development-plan)
3. [Revised Vision Document](#3-revised-vision-document)
4. [Revised AI Usage Report](#4-revised-ai-usage-report)
5. [Global Formatting and Presentation Improvements](#5-global-formatting-and-presentation-improvements)
6. [Consolidated Change Traceability Matrix](#6-consolidated-change-traceability-matrix)
7. [Final Submission Verification](#7-final-submission-verification)

---

## 1. Summary of TA Feedback and Revisions

**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

| Feedback ID | TA feedback | Affected document | Revision made | Status |
| --- | --- | --- | --- | --- |
| TA-01 | Tasks in each sprint need to be described in greater detail. | Software Development Plan | Expanded every sprint into smaller, assignable, estimable, and verifiable tasks. Added owners, priorities, dependencies, estimates, acceptance criteria, deliverables, and Trello references. | **Completed** |
| TA-02 | The AI Audit Report needs more detail. | AI Usage Report | Added an AI-use declaration, tool information, a detailed usage log, prompt purposes, generated outputs, human verification, corrections, affected artifacts, and audit evidence. | **Completed** |
| TA-03 | The document format should be more polished and readable. | All revised documents | Standardized headings, contribution lines, tables, spacing, terminology, diagrams, captions, cross-references, and Markdown-to-PDF formatting. | **Completed** |

### Revision Principles

- Every change must identify the affected document and section.
- Descriptions must explain what changed, not only state that a section was updated.
- New claims must be traceable to a requirement, Trello issue, meeting record, prototype, commit, or other project evidence.
- Placeholder text must be replaced before the final submission.
- The Markdown and PDF versions of the same document must contain equivalent information.


---

## 2. Revised Software Development Plan

**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

**Files:**

- `documents/SoftwareDevelopmentPlan.md`
- `documents/SoftwareDevelopmentPlan.pdf`

### 2.1 Revision Overview

| Change ID | PA2 issue | PA3 revision | Updated section | Verification evidence |
| --- | --- | --- | --- | --- |
| SDP-01 | Sprint tasks were too broad and difficult to assign. | Divided each feature into analysis, design, frontend, backend, database, integration, testing, documentation, and review tasks where applicable. | Sprint Plan / Work Breakdown Structure | Trello backlog and sprint board |
| SDP-02 | Tasks did not clearly state who was responsible. | Added an owner or assignee to every task. | Sprint Plan | Trello assignee field |
| SDP-03 | Task duration and effort were unclear. | Added story points or estimated hours and expected start/end dates. | Schedule and Sprint Plan | Trello estimates and project schedule |
| SDP-04 | Completion conditions were not measurable. | Added a deliverable and acceptance criteria to every task. | Sprint Task Details | Pull requests, screenshots, test results, or documents |
| SDP-05 | Dependencies between tasks were missing. | Added task dependencies and identified blocking relationships. | Sprint Task Details | Trello issue links and dependency column |
| SDP-06 | Sprint objectives were not explicit. | Added a sprint goal, expected increment, milestone, and exit criteria for every sprint. | Sprint Overview | Sprint Review record |
| SDP-07 | Progress tracking was difficult to verify. | Added Trello issue IDs, task statuses, and links to related commits or artifacts. | Sprint Task Details | Trello screenshots and Git log |
| SDP-08 | The plan's presentation was inconsistent. | Standardized headings, tables, terminology, spacing, and contribution information. | Entire document | Markdown and exported PDF review |

### 2.2 Detailed Sprint Structure

Each sprint in the revised plan uses the following structure:

#### Sprint [NUMBER] - [SPRINT NAME]

**Sprint duration:** [START DATE] to [END DATE]  
**Sprint goal:** [CLEAR AND MEASURABLE GOAL]  
**Functional scope:** [FEATURES OR USE-CASE IDS]  
**Expected increment:** [DEMONSTRABLE PRODUCT INCREMENT]  
**Sprint owner:** [NAME]  
**Definition of Done:** [SPRINT-LEVEL COMPLETION CONDITIONS]

##### Sprint Task Backlog

| Task ID | Requirements / UC | Detailed task | Owner | Reviewer | Estimate | Dependency | Acceptance | Status
| --- | --- | --- | --- | --- | --- | ---: | --- | --- |
| S[NO]-T01 | [US/UC ID] | [Specific analysis or clarification task] | Analysis | [NAME] | High | [POINTS/HOURS] | None | None |
| S[NO]-T02 | [US/UC ID] | [Specific UI/UX task] | Design | [NAME] | High | [POINTS/HOURS] | S[NO]-T01 | None |
| S[NO]-T03 | [US/UC ID] | [Specific database task] | Database | [NAME] | High | [POINTS/HOURS] | S[NO]-T01 | None |
| S[NO]-T04 | [US/UC ID] | [Specific API or business-logic task] | Backend | [NAME] | High | [POINTS/HOURS] | S[NO]-T03 | None |
| S[NO]-T05 | [US/UC ID] | [Specific interface task] | Frontend | [NAME] | High | [POINTS/HOURS] | S[NO]-T02, S[NO]-T04 | None |
| S[NO]-T06 | [US/UC ID] | [Specific integration task] | Integration | [NAME] | Medium | [POINTS/HOURS] | S[NO]-T04, S[NO]-T05 | None |
| S[NO]-T07 | [US/UC ID] | [Specific test task] | Testing | [NAME] | High | [POINTS/HOURS] | S[NO]-T06 | None |
| S[NO]-T08 | [US/UC ID] | [Specific documentation and review task] | Documentation | [NAME] | Medium | [POINTS/HOURS] | S[NO]-T07 | None |

### 2.3 Example of a More Detailed Task

#### Before PA3

| Task | Assignee | Status |
| --- | --- | --- |
| Implement invoice management | [NAME] | To do |

#### After Revision

| Task ID | Requirement / UC | Detailed task | Owner | Reviewer | Estimate | Dependency | Acceptance criteria | Status |
| --- | --- | --- | --- | --- | ---: | --- | --- | --- |
| S2-DOC-01 | PA2 | Audit PA2 requirements against the Vision Document and SDP. | Đạt | Duy Anh | 5h | PA2 rubric | Compliance checklist is complete. | Completed |

### 2.4 New Task-Level Information

The PA3 version adds information that was not consistently present in the PA2 later-sprint plan:

- Requirement, feature, or use-case references.
- A specific task description instead of a broad feature name.
- One owner and one independent reviewer.
- Estimated effort in hours.
- Dependencies between tasks.
- Acceptance criteria.
- Task status such as `Completed`, `In Progress`, or `Planned`.
- Evidence through Trello, commits, tests, screenshots, documents, or demonstrations.

Example of the revised task style:

| PA2-style task | PA3 detailed tasks |
| --- | --- |
| Complete maintenance assignment and Maintenance Staff workspace. | Implement manager assignment, implement Maintenance Staff status updates, implement repair-result and image-evidence endpoints, implement the Maintenance Staff workspace, integrate the workflow, and test authorization and status transitions. |

### 2.5 Scope and Role Changes

The PA3 version introduces the following scope changes:

- The Floor Manager role is removed.
- Floor-related duties are reassigned to the Dormitory Manager.
- `FR08 - Back up and restore data` is removed from the current user-facing scope.
- `FR19 - Inspect assets` is removed as a standalone use case.
- `FR24 - Generate revenue reports` remains a planned capability.
- The functional scope is mapped to actors, requirements, student features, and maintenance-staff features.

---

## 3. Revised Vision Document

**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

This section records only the visual and presentation improvements made to `PA3/VD_v1.1.md`.

| Change ID | Appearance improvement | Result |
| --- | --- | --- |
| VIS-APP-01 | Replaced the loose bold metadata block with a clean front-matter table. | The first page now looks more structured and professional. |
| VIS-APP-02 | Added a compact Vision Snapshot near the top of the document. | Reviewers can understand the product, audience, PA3 focus, and evidence before reading the full document. |
| VIS-APP-03 | Added a Table of Contents with direct links to all major sections. | The document is easier to navigate in Markdown and PDF form. |
| VIS-APP-04 | Standardized the heading hierarchy so the document has one H1 and consistent H2/H3 sections. | The rendered outline is cleaner and less visually noisy. |
| VIS-APP-05 | Converted repeated performer, reviewer, and editor lines into lighter blockquote styling. | Required contribution information remains visible without overpowering the content. |
| VIS-APP-06 | Standardized Markdown table separators, column names, spacing, and repository links. | Tables render more cleanly and are easier to compare. |
| VIS-APP-07 | Added a Feature Status Summary before the detailed F-01 to F-10 feature descriptions. | Long feature content becomes easier to scan. |
| VIS-APP-08 | Updated old labels, role notes, footer version, and spelling consistency. | The Vision Document looks like a polished PA3 revision instead of an unfinished PA2 draft. |

---

## 4. Revised AI Usage Report

**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

**Files:**

- `documents/AIUsageReport.md`
- `documents/AIUsageReport.pdf`

### 4.1 Revision Overview

| Change ID | PA2 issue | PA3 revision | Updated section | Verification evidence |
| --- | --- | --- | --- | --- |
| AI-01 | The report did not clearly declare whether AI was used. | Added an explicit AI-use declaration and identified the sprint covered by the report. | AI Usage Declaration | Signed team confirmation |
| AI-02 | AI tools were named without enough context. | Added tool name, model or version when known, user, date, and purpose. | Tools and Usage Scope | Tool records |
| AI-03 | Prompts and outputs were not traceable to project work. | Added a unique usage ID, related Trello issue, affected artifact, prompt purpose, output summary, and link to evidence. | Detailed AI Usage Log | Trello, commits, and document history |
| AI-04 | Human review was unclear. | Added the reviewer, verification method, corrections made, and final acceptance or rejection decision for every material AI output. | Human Verification | Review notes and commits |
| AI-05 | Incorrect or unused AI output was not documented. | Added rejected outputs, identified problems, and corrective actions. | Rejected or Corrected Outputs | Correction log |
| AI-06 | Privacy and academic-integrity controls were missing. | Added rules for confidential data, credentials, copyrighted content, attribution, and human responsibility. | Risk and Compliance Review | Team review checklist |
| AI-07 | The audit trail was difficult to inspect. | Added consistent tables, evidence references, summaries by member/tool, and contribution lines. | Entire report | Markdown and PDF review |

### 4.2 Required AI Usage Log Structure

The revised AI Usage Report records each material use of AI using two compact records. Splitting the log this way keeps the report readable in both Markdown and PDF.

##### AI usage record

| Usage ID | Date | Team member | AI tool / model | Related task or Trello ID | Final decision |
| --- | --- | --- | --- | --- | --- |
| AI-[NO] | [DATE] | [NAME] | [TOOL/MODEL] | [TASK/TRELLO-ID] | Accepted / Modified / Rejected |

##### Purpose, verification, and evidence

| Usage ID | Purpose and prompt summary | Output used | Human verification | Corrections made | Affected artifact | Evidence |
| --- | --- | --- | --- | --- | --- | --- |
| AI-[NO] | [WHY AI WAS USED AND WHAT WAS REQUESTED] | [SUMMARY OF OUTPUT] | [HOW ACCURACY AND QUALITY WERE CHECKED] | [CHANGES MADE BY THE TEAM] | [FILE/SECTION/COMMIT] | [LINK/PATH/SCREENSHOT] |

### 4.3 AI Output Verification Categories

Every AI-assisted result must record at least one applicable verification method:

- Compared with the approved Vision Document or use-case specification.
- Executed and tested locally.
- Reviewed through a pull request or peer review.
- Checked against official technical documentation.
- Validated using test cases and expected outputs.
- Compared with the database schema or API contract.
- Proofread for factual, grammatical, and formatting errors.
- Rejected because the output was incorrect, incomplete, insecure, or outside the project scope.

### 4.4 AI Usage Summary

| Team member | Tool | Number of recorded uses | Main purposes | Accepted | Modified | Rejected |
| --- | --- | ---: | --- | ---: | ---: | ---: |
| [NAME] | [TOOL] | [COUNT] | [PURPOSES] | [COUNT] | [COUNT] | [COUNT] |
| **Total** | - | **[COUNT]** | - | **[COUNT]** | **[COUNT]** | **[COUNT]** |

### 4.5 AI Risk and Compliance Additions

- No password, API key, access token, private key, or production credential is included in an AI prompt.
- Personally identifiable student data is removed or anonymized before AI use.
- AI-generated code is reviewed for security, authorization, validation, and error handling.
- AI-generated text is checked against the actual project scope and implementation.
- AI-generated diagrams and specifications are reviewed for consistency with the Vision Document.
- Team members remain responsible for every submitted artifact, including AI-assisted content.

---

## 5. Global Formatting and Presentation Improvements

**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

The following presentation improvements were applied consistently across the revised documents.

| Change ID | Formatting issue | Improvement | Applied to |
| --- | --- | --- | --- |
| FMT-01 | Heading styles and numbering were inconsistent. | Applied a consistent numbered heading hierarchy and descriptive section titles. | All reports |
| FMT-02 | Contributor information was missing or placed inconsistently. | Added `Performed by`, `Reviewed by`, and `Edited by` immediately after each main section header. | All reports |
| FMT-03 | Long paragraphs were difficult to scan. | Converted suitable content into concise paragraphs, bullet lists, and structured tables. | All reports |
| FMT-04 | Terminology varied between documents. | Standardized actor names, module names, requirement IDs, use-case IDs, and status labels. | Vision, SDP, Use-Case Model, specifications |
| FMT-05 | Tables were difficult to compare. | Added consistent column names, alignment, IDs, and short evidence-oriented descriptions. | All reports |
| FMT-06 | Diagrams lacked consistent presentation. | Standardized Mermaid syntax, diagram titles, actor labels, boundaries, and relationship notation. | Documents containing diagrams |
| FMT-07 | Figures and prototypes were difficult to reference. | Added figure numbers, captions, and references from the relevant text or use-case specification. | Use-Case Specifications |
| FMT-08 | Markdown and PDF output could differ visually. | Reviewed page breaks, table width, diagram scaling, spacing, and readability after PDF conversion. | All Markdown/PDF document pairs |
| FMT-09 | Navigation was limited. | Added a table of contents or clear section hierarchy where appropriate. | Long reports |
| FMT-10 | File navigation was unclear. | Standardized filenames and linked deliverables from `README.md`. | Submission package |

### Formatting Verification Checklist

- [ ] Heading levels are sequential and consistent.
- [ ] Each main section includes performer, reviewer, and editor information on the first line after its header.
- [ ] Tables render correctly in both Markdown and PDF.
- [ ] Mermaid diagrams render without syntax errors.
- [ ] Figures and prototype screenshots have captions.
- [ ] Requirement IDs and use-case IDs are consistent across documents.
- [ ] No obsolete role, feature, date, or placeholder remains.
- [ ] No table, diagram, or image is clipped in the PDF.
- [ ] Page breaks do not separate a heading from its first paragraph or table.
- [ ] English wording has been proofread for clarity and consistency.

---

## 6. Consolidated Change Traceability Matrix

**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

Use this table to provide direct evidence that every declared change was applied.

| Change ID | Document | Updated section | Related TA feedback | Responsible member | Review status | Evidence or location |
| --- | --- | --- | --- | --- | --- | --- |
| SDP-01 to SDP-08 | Software Development Plan | Sprint Plan, Schedule, Task Details | TA-01, TA-03 | [NAME] | Reviewed | [FILE SECTION/TRELLO/LINK] |
| VIS-APP-01 to VIS-APP-08 | Vision Document | Appearance, formatting, navigation, table styling, feature scanning, and visual polish | TA-03 | Trần Huỳnh Mạnh Đạt | Reviewed | `PA3/VD_v1.1.md` |
| AI-01 to AI-07 | AI Usage Report | Declaration, Usage Log, Verification, Compliance | TA-02, TA-03 | [NAME] | Reviewed | [FILE SECTION/LINK] |
| FMT-01 to FMT-10 | All revised documents | Entire documents | TA-03 | [NAME] | Reviewed | [FILES/PDF REVIEW] |

---

## 7. Final Submission Verification

**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

### Document completeness

- [ ] This is the only `Changes.md` file in the PA3 submission package.
- [ ] Every document revised after PA2 has its own section in this file.
- [ ] Every declared change is present in the corresponding document.
- [ ] Updated schedules, roles, requirements, and document names are consistent.

### Sprint and AI audit quality

- [ ] Sprint tasks are detailed, assignable, estimable, and verifiable.
- [ ] Each sprint includes a goal, expected increment, task breakdown, and completion criteria.
- [ ] The AI Usage Report contains a detailed log of every material AI use.
- [ ] Human verification and corrections are documented for AI-assisted outputs.

### Presentation and final review

- [ ] Formatting improvements are visible in both Markdown and PDF.
- [ ] All remaining placeholders in square brackets have been replaced.
- [ ] Contributor information is correct in every section.
- [ ] The final change log has been reviewed against the TA feedback and PA3 requirements.
