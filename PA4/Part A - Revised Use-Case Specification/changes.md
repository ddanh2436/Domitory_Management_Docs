# PA4 Change Log

**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

## 1. Use Case Specification AI Feature Update

**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

**Updated file:** `PA4/Use Case Specification.md`  
**Update date:** 08 August 2026  
**Purpose:** Add the implemented Dormify AI chatbot feature that was missing from the PA4 Use Case Specification.

| Change ID | Section updated | Change made | Repository evidence |
| --- | --- | --- | --- |
| PA4-UCS-AI-01 | Document metadata and snapshot | Updated version to `1.1 — PA4 AI update`, date to `08 August 2026`, scope to PA4, use-case count from 72 to 76, functional groups from 11 to 12, and added Dormify AI as the selected PA4 addition. | `PA4/Use Case Specification.md` |
| PA4-UCS-AI-02 | PA4 Structure Checklist | Renamed the checklist from PA3 to PA4 and clarified that PA4 AI use cases use repository evidence because no PA3 AI prototype screenshot exists. | `Domitory_Management_Frontend/app/components/ChatbotWidget.tsx` |
| PA4-UCS-AI-03 | Table of Contents and Functional Group Index | Added `13. AI Chatbot Assistance` and renumbered traceability appendices to sections 14-17. Added the AI functional group with 4 use cases. | `PA4/Use Case Specification.md` |
| PA4-UCS-AI-04 | Introduction and actors | Added a modeling decision that Dormify AI is active because `ChatbotModule` is registered in the backend root module. Added `Authenticated User` and `Ollama AI Runtime` actors, and updated Student/System Admin responsibilities. | `Domitory_Management_Backend/src/app.module.ts`, `Domitory_Management_Backend/src/chatbot/chatbot.module.ts` |
| PA4-UCS-AI-05 | New section 13 | Added the `AI Chatbot Assistance` functional group with four use cases: `UC-AI-01 Ask Dormify AI Question`, `UC-AI-02 Submit AI Answer Feedback`, `UC-AI-03 Review AI Answer Feedback`, and `UC-AI-04 Rebuild AI Knowledge Base`. | `Domitory_Management_Backend/src/chatbot/chatbot.controller.ts`, `Domitory_Management_Backend/src/chatbot/chatbot.service.ts` |
| PA4-UCS-AI-06 | UC-AI-01 | Defined authenticated chatbot asking, SSE streaming, recent-history handling, vector plus keyword retrieval, source chips, not-found guidance, personalized profile/contract/invoice context, and structured invoice-card display. | `ChatbotController.streamChat`, `ChatbotService.prepareContext`, `ChatbotService.searchKnowledgeDetailed`, `ChatbotService.getPersonalContext`, `ChatbotService.getInvoiceCard`, `ChatbotWidget.tsx` |
| PA4-UCS-AI-07 | UC-AI-02 | Defined thumbs-up/thumbs-down answer feedback, optimistic frontend state, backend validation, and upsert behavior keyed by user and question. | `ChatbotController.submitFeedback`, `ChatbotService.saveFeedback`, `ChatFeedbackSchema` |
| PA4-UCS-AI-08 | UC-AI-03 | Defined administrator review of chatbot feedback, negative-only filtering, admin role protection, sorting, and populated user identity fields. | `GET /api/chatbot/feedback`, `@Roles('ADMIN')`, `ChatbotService.listFeedback` |
| PA4-UCS-AI-09 | UC-AI-04 | Defined administrator knowledge-base rebuild from Markdown documents, chunking with source labels, Ollama embedding generation, MongoDB knowledge storage, and normalized keyword-search text. | `POST /api/chatbot/ingest`, `ChatbotService.ingestData`, `KnowledgeSchema`, `scripts/run-ingest.ts` |
| PA4-UCS-AI-10 | Traceability appendices | Added `FR31`, `ST29–ST30`, and `MT09` traceability rows for Dormify AI, then updated the document end note to `Version 1.1 / PA4`. | `PA4/Use Case Specification.md` |

## 2. Verification

**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

| Check | Result |
| --- | --- |
| Detailed use-case headings | 76 |
| Use-case rows in functional group tables | 76 |
| New AI use-case IDs | `UC-AI-01` to `UC-AI-04` |
| Functional groups after update | 12 |
| Traceability sections after renumbering | Sections 14-17 |
| Backend evidence checked | `ChatbotModule`, `ChatbotController`, `ChatbotService`, `KnowledgeSchema`, `ChatFeedbackSchema`, ingestion scripts |

## 3. Use Case Specification Conduct-Appeal Update

**Performed by:** Đào Duy Anh | **Reviewed by:** Trần Huỳnh Mạnh Đạt | **Edited by:** Đào Duy Anh

**Updated file:** `PA4/Part A - Revised Use-Case Specification/Use Case Specification.md`  
**Update date:** 26 August 2026  
**Purpose:** Add the implemented violation appeal and revocation feature, which was specified in `PA4/Part E - Spec kit/002-violation-appeal-revocation` and shipped to the backend, but had no corresponding use cases in the Use Case Specification.

| Change ID | Section updated | Change made | Repository evidence |
| --- | --- | --- | --- |
| PA4-UCS-CA-01 | Document metadata and snapshot | Updated version to `1.2 — PA4 AI and conduct-appeal update`, date to `26 August 2026`, use-case count from 76 to 79, and generalised the PA4 addition row to cover both the AI chatbot and the conduct-appeal flow. | `PA4/Part A - Revised Use-Case Specification/Use Case Specification.md` |
| PA4-UCS-CA-02 | Functional Group Index | Raised the `Conduct and Student Evaluation` count from 5 to 8 use cases; the group total now matches the 79 detailed use-case headings. | `PA4/Part A - Revised Use-Case Specification/Use Case Specification.md` |
| PA4-UCS-CA-03 | Section 11 group table and description | Added `UC-COND-06`, `UC-COND-07`, and `UC-COND-08` rows and extended the group description to mention the appeal and revocation flow that reverses a deduction. | `Domitory_Management_Backend/src/violations/violations.controller.ts` |
| PA4-UCS-CA-04 | UC-COND-06 | Defined student-initiated appeal: ownership check, `ACTIVE`-only precondition, mandatory reason capped at 500 characters, transition to `APPEAL_PENDING`, and notification of the management board. | `ViolationsService.appealViolation`, `AppealViolationDto`, `POST /api/violations/:id/appeal` |
| PA4-UCS-CA-05 | UC-COND-07 | Defined management review of a pending appeal: `APPEAL_PENDING`-only precondition, `ACCEPT` / `REJECT` decision, review note and reviewer audit fields, conduct-score restoration capped at 100 on acceptance, and student notification of the outcome. | `ViolationsService.reviewAppeal`, `ViolationsService.restoreScore`, `ReviewAppealDto`, `PATCH /api/violations/:id/review` |
| PA4-UCS-CA-06 | UC-COND-08 | Defined direct revocation of a mistakenly recorded violation, including the guard that blocks a second revocation so conduct points are never restored twice. | `ViolationsService.revokeViolation`, `DELETE /api/violations/:id` |
| PA4-UCS-CA-07 | Traceability appendices | Added `FR32` covering `UC-COND-06` to `UC-COND-08`, added `ST31 Violation appeal`, and extended `FM13` to include the review and revocation use cases. Updated the document end note to `Version 1.2 / PA4`. | `PA4/Part A - Revised Use-Case Specification/Use Case Specification.md` |
| PA4-UCS-CA-08 | Prototype screen links | Repointed all 92 screenshot links from `../assets/...` to `../../PA3/assets/...`. When the specification was copied from `PA3/documents/` into `PA4/Part A - .../`, the relative paths kept resolving to a non-existent `PA4/assets` folder, so every screenshot was broken. One link that was already broken in PA3 (`UC-PRO-01.png`) was repointed to the file its alt text names, `studentprofile.png`. | `PA3/assets/` |

## 4. Verification

**Performed by:** Đào Duy Anh | **Reviewed by:** Trần Huỳnh Mạnh Đạt | **Edited by:** Đào Duy Anh

| Check | Result |
| --- | --- |
| Detailed use-case headings | 79 |
| Use-case rows in functional group tables | 79 |
| Sum of the Functional Group Index counts | 79 |
| New conduct use-case IDs | `UC-COND-06` to `UC-COND-08` |
| Screenshot links resolving to a real file | 92 of 92 |
| Backend evidence checked | `ViolationsController`, `ViolationsService`, `AppealViolationDto`, `ReviewAppealDto`, `ViolationSchema`, `ViolationStatus`, `violations.service.spec.ts` |
