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
