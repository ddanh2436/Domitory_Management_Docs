# Project Proposal: DMS (Dormitory Management System)

## 1. Introduction
The Dormitory Management System is a comprehensive digital transformation platform aimed at automating and optimizing residential operations. The project solves complex problems ranging from facility management, student records, and financial processes to daily security control. The core value of the application is to minimize manual paperwork, enhance transparency, support the management board in making quick decisions, and provide a modern, safe, and convenient living experience for students.

## 2. Target Users and Environments
* **Target Users (5 Actors):**
  1. **System Admin:** Has full control over system configuration and manages accounts for other roles.
  2. **Dormitory Manager:** Manages overall security, order, announcements, and discipline across the entire dormitory.
  3. **Floor Manager:** Monitors detailed room status, hygiene, attendance, and incidents on their assigned floor.
  4. **Office Staff:** Includes accountants (handling invoices, fee collection) and receptionists (handling check-in/check-out procedures).
  5. **Student:** Residents living in the dorm, using the system to track personal information, pay fees, and interact with the management board.
* **Environments:**
Due to the limit deadline of this project*, only web application is deployed. All users can be granted access to the system with limit features for their roles. For instance, Admins have the right to set roles for each roles.

## 3. Key Features
The system is divided into 8 main functional groups, comprehensively serving the management and daily living processes within the dormitory:

**3.1. Identity & Profile Management**
This functional group supports secure user registration and login (including Google Authentication and ID card verification) and manages personal and contact information throughout the residency. This allows students to easily set up their initial profiles while ensuring the system authenticates users in a strict, secure, and synchronized manner.

**3.2. System Administration & Security**
Provides System Administrators with tools to manage accounts, assign roles, monitor system logs, and perform data backup and recovery. This helps maintain the stability and data security of the application while strictly controlling the access permissions of each management level.

**3.3. Room Registration & Digital Contracts**
Allows students to search for available rooms using filters, supports automatic or manual bed allocation, and manages the entire lifecycle of contracts (signing, renewing, terminating, and exporting to PDF). This functional group minimizes manual paperwork, making the check-in and check-out processes transparent and time-saving for both the management board and the students.

**3.4. Financial & Billing Operations**
Integrates the workflow for recording initial and final electricity/water meter readings (with support for uploading proof photos), automatically generates invoices, and allows students to pay via multiple platforms (VNPay, MoMo, ZaloPay, Internet Banking). This feature brings convenience to students while helping the accounting team automate debt tracking and revenue reporting.

**3.5. Maintenance & Incident Tracking**
Enables students to create facility repair requests, which the system then routes to maintenance staff, allowing them to update the task status with before-and-after photos. Establishing a closed-loop processing workflow helps resolve incidents quickly, improves service quality, and keeps a historical log for asset protection.

**3.6. Residency & Visitor Control**
Fully digitizes administrative procedures such as registering temporary residence/absence, reporting overnight absences, and registering dormitory visitors. This function helps Floor Managers and Dormitory Managers accurately grasp the real-time occupancy status, ensuring security, order, and compliance with legal regulations.

**3.7. Disciplinary & Evaluation System**
Provides tools for the management board to create violation reports, record disciplinary history, and conduct periodic evaluations of students' conduct points. Digitizing this rulebook helps maintain discipline in an objective and fair manner, providing transparent data whenever cross-referencing is needed.

**3.8. Communication & Feedback Hub**
Acts as a two-way information portal where the management board can send important announcements and students can submit feedback or complaints. This feature eliminates reliance on traditional physical bulletin boards, ensuring that information reaches the right people at the right time and that inquiries are resolved promptly.

## 4. AI Features
To enhance operational efficiency and user experience, the system integrates artificial intelligence into its core workflows. Below are the proposed AI capabilities:

**4.1. Smart Ticketing & Maintenance Routing (NLP)**
* **Feature Description:** When a student reports a facility issue, instead of the management board manually reading and routing the ticket, an AI model utilizing Natural Language Processing (NLP) takes over. By analyzing the student's text description (e.g., "the shower on the 3rd floor is leaking" or "the ceiling light is flickering"), the AI automatically categorizes the issue (Plumbing, Electrical, Carpentry) and estimates the priority level (Normal, Urgent). 
* **User Value:** This eliminates the manual triage process for managers and instantly routes the task to the appropriate maintenance staff. It significantly reduces repair response times and ensures that critical issues (like severe water leaks) are addressed immediately.

**4.2. 24/7 Virtual Assistant for Rules & Procedures (RAG Chatbot)**
* **Feature Description:** Students frequently ask repetitive administrative questions (e.g., "Where do I pay the electricity bill?", "What is the curfew time?", "How do I report a lost key?"). The system implements an intelligent chatbot powered by Retrieval-Augmented Generation (RAG). By embedding the dormitory's official rulebooks, guidelines, and FAQs into a vector database, the chatbot extracts precise information to answer student queries 24/7.
* **User Value:** This feature drastically reduces the administrative burden on office staff and dorm managers. It provides students with instant, accurate, and reliable support at any time of the day without human intervention.