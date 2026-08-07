# 24/06/2026

**MEETING MINUTES**

**Project Name:** Smart Dormitory Management System  
**Course:** Introduction to Software Engineering  
**Meeting Date:** 24th June 2026  
T**ime:** 21:00 – 21:30  
**Location/Platform:** Google Meet

# **1\. Attendance**

- Attendees:  
  - Đào Duy Anh \- 24127012  
  - Hồ Phúc Kiên \- 24127067  
  - Trần Huỳnh Mạnh Đạt \- 24127024  
  - Trần Hoàng Quốc Khánh \- 24127057  
- Absentees:  
  - Tô Trần Hoàng Triệu \- 23127  
-  Minute Taker: Trần Huỳnh Mạnh Đạt

# **2\. Meeting Objectives / Agenda**

1\.       Review the current progress of the project proposal.  
2\.       Discussing PA2 requirements.  
3\.       Assign tasks for the upcoming week.

# **3\. Discussion & Progress Updates**

- AI Features Finalization: The team agreed to drop the rule-based roommate matching from the AI section and officially finalized two concrete AI capabilities: "Smart Ticketing (NLP)" and the "RAG Chatbot".  
- PA2 Requirements Review: The team thoroughly analyzed the guidelines for PA2. Core architectural constraints and technical stacks were evaluated to ensure the project alignment is correct from the start.  
- Workflow Adjustments: The team reviewed the proposed changes to the new user registration flow. After evaluating the complexity and system stability, it was agreed to retain the current registration flow instead of pursuing the previously planned modifications.  
- Development Initiation: Initial repository setup and database schema design based on the PA2 requirements have officially commenced.  
- Code Review & Technical Feedback (Addressed to Hoang Trieu): The Team Leader raised several critical technical issues regarding the recent code push that need immediate rectification:  
- Frontend/Backend Desynchronization: Data definitions and JWT configurations were altered on the frontend without applying the mandatory corresponding updates to the backend (schema, services, modules, and controllers). Pushing only the frontend has caused severe data mismatches and broken the API communication.  
- Code Quality & Feature Regression: The UI implementation in the recent commit did not meet the project's quality standards. Furthermore, several existing and stable features were overwritten or completely removed during the push.  
- API Instability & Repository Clutter: The update introduced multiple unhandled API errors and included a large number of unnecessary/unrelated files, cluttering the codebase.

# **4\. Decisions Made**

- Decision 1: The final AI feature will be the "24/7 Virtual Assistant for Rules & Procedures (RAG Chatbot)".  
- Decision 2: The project will strictly proceed according to the baseline of the PA2 requirements.  
- Decision 3: Retain the current account registration flow for new users, all planned changes related to this flow are officially canceled.

# **5\. Task Assigning**

| Task | Assignee | Deadline | Status  |
| :---- | :---- | :---- | :---- |
| Improve UI/UX of Admin, Login/register form | Hồ Phúc Kiên | 28 thg 6, 2026 | Completed |
| Maintenance Request page optimization, Complete Remaining Contract Features | Trần Hoàng Quốc Khánh | 1 thg 7, 2026 | Completed |
| Implement backend APIs for user authentication (login) and account management (lock/unlock)  | Đào Duy Anh | 28 thg 6, 2026 | Completed |
| Fix and improve the UI/UX of the recently implemented features  | Tô Trần Hoàng Triệu | 27 thg 6, 2026 | Blocked |
| Implement backend logic for room registration and utility booking, Develop backend for maintenance requests and real-time notification logic  | Đào Duy Anh | 3 thg 7, 2026 | Completed |
| Secure WebSockets (Socket.IO) with JWT validation in [notifications.gateway.ts](http://notifications.gateway.ts),Implement Task Scheduling (Cron Job) for automated OVERDUE invoice updates & notifications  | Trần Huỳnh Mạnh Đạt | 30 thg 6, 2026 | Completed |

# **6\. Next Meeting**

·         Date: 1 thg 7, 2026 21:00 GMT+7  
·         Location: Google Meets  
·         Objective: Review the completed tasks from the current sprint and begin fulfilling the PA2 requirements.

# Thẻ 2

**MEETING MINUTES**

**Project Name:** Smart Dormitory Management System  
**Course:** Introduction to Software Engineering  
**Meeting Date:** 24th June 2026  
T**ime:** 21:00 – 21:30  
**Location/Platform:** Google Meet

# **1\. Attendance**

- Attendees:  
  - Đào Duy Anh \- 24127012  
  - Hồ Phúc Kiên \- 24127067  
  - Trần Huỳnh Mạnh Đạt \- 24127024  
  - Trần Hoàng Quốc Khánh \- 24127057  
- Absentees:  
  - Tô Trần Hoàng Triệu \- 23127  
-  Minute Taker: Trần Huỳnh Mạnh Đạt

# **2\. Meeting Objectives / Agenda**

1\.       Review the current progress of the project proposal.  
2\.       Discussing PA2 requirements.  
3\.       Assign tasks for the upcoming week.

# **3\. Discussion & Progress Updates**

- AI Features Finalization: The team agreed to drop the rule-based roommate matching from the AI section and officially finalized two concrete AI capabilities: "Smart Ticketing (NLP)" and the "RAG Chatbot".  
- PA2 Requirements Review: The team thoroughly analyzed the guidelines for PA2. Core architectural constraints and technical stacks were evaluated to ensure the project alignment is correct from the start.  
- Workflow Adjustments: The team reviewed the proposed changes to the new user registration flow. After evaluating the complexity and system stability, it was agreed to retain the current registration flow instead of pursuing the previously planned modifications.  
- Development Initiation: Initial repository setup and database schema design based on the PA2 requirements have officially commenced.  
- Code Review & Technical Feedback (Addressed to Hoang Trieu): The Team Leader raised several critical technical issues regarding the recent code push that need immediate rectification:  
- Frontend/Backend Desynchronization: Data definitions and JWT configurations were altered on the frontend without applying the mandatory corresponding updates to the backend (schema, services, modules, and controllers). Pushing only the frontend has caused severe data mismatches and broken the API communication.  
- Code Quality & Feature Regression: The UI implementation in the recent commit did not meet the project's quality standards. Furthermore, several existing and stable features were overwritten or completely removed during the push.  
- API Instability & Repository Clutter: The update introduced multiple unhandled API errors and included a large number of unnecessary/unrelated files, cluttering the codebase.

# **4\. Decisions Made**

- Decision 1: The final AI feature will be the "24/7 Virtual Assistant for Rules & Procedures (RAG Chatbot)".  
- Decision 2: The project will strictly proceed according to the baseline of the PA2 requirements.  
- Decision 3: Retain the current account registration flow for new users, all planned changes related to this flow are officially canceled.

# **5\. Task Assigning**

| Task | Assignee | Deadline | Status  |
| :---- | :---- | :---- | :---- |
| Improve UI/UX of Admin, Login/register form | Hồ Phúc Kiên | 28 thg 6, 2026 | In progress |
| Maintenance Request page optimization, Complete Remaining Contract Features | Trần Hoàng Quốc Khánh | 1 thg 7, 2026 | In progress |
| Implement backend APIs for user authentication (login) and account management (lock/unlock)  | Đào Duy Anh | 28 thg 6, 2026 | In progress |
| Fix and improve the UI/UX of the recently implemented features  | Tô Trần Hoàng Triệu | 27 thg 6, 2026 | In progress |
| Implement backend logic for room registration and utility booking, Develop backend for maintenance requests and real-time notification logic  | Đào Duy Anh | 3 thg 7, 2026 | In progress |
| Secure WebSockets (Socket.IO) with JWT validation in [notifications.gateway.ts](http://notifications.gateway.ts),Implement Task Scheduling (Cron Job) for automated OVERDUE invoice updates & notifications  | Trần Huỳnh Mạnh Đạt | 28 thg 6, 2026 | In progress |

# **6\. Next Meeting**

·         Date: 1 thg 7, 2026 21:00 GMT+7  
·         Location: Google Meets  
·         Objective: Review the completed tasks from the current sprint and begin fulfilling the PA2 requirements.

# Thẻ 3

**MEETING MINUTES**

**Project Name:** Smart Dormitory Management System  
**Course:** Introduction to Software Engineering  
**Meeting Date:** 24th June 2026  
T**ime:** 21:00 – 21:30  
**Location/Platform:** Google Meet

# **1\. Attendance**

- Attendees:  
  - Đào Duy Anh \- 24127012  
  - Hồ Phúc Kiên \- 24127067  
  - Trần Huỳnh Mạnh Đạt \- 24127024  
  - Trần Hoàng Quốc Khánh \- 24127057  
- Absentees:  
  - Tô Trần Hoàng Triệu \- 23127  
-  Minute Taker: Trần Huỳnh Mạnh Đạt

# **2\. Meeting Objectives / Agenda**

1\.       Review the current progress of the project proposal.  
2\.       Discussing PA2 requirements.  
3\.       Assign tasks for the upcoming week.

# **3\. Discussion & Progress Updates**

- AI Features Finalization: The team agreed to drop the rule-based roommate matching from the AI section and officially finalized two concrete AI capabilities: "Smart Ticketing (NLP)" and the "RAG Chatbot".  
- PA2 Requirements Review: The team thoroughly analyzed the guidelines for PA2. Core architectural constraints and technical stacks were evaluated to ensure the project alignment is correct from the start.  
- Workflow Adjustments: The team reviewed the proposed changes to the new user registration flow. After evaluating the complexity and system stability, it was agreed to retain the current registration flow instead of pursuing the previously planned modifications.  
- Development Initiation: Initial repository setup and database schema design based on the PA2 requirements have officially commenced.  
- Code Review & Technical Feedback (Addressed to Hoang Trieu): The Team Leader raised several critical technical issues regarding the recent code push that need immediate rectification:  
- Frontend/Backend Desynchronization: Data definitions and JWT configurations were altered on the frontend without applying the mandatory corresponding updates to the backend (schema, services, modules, and controllers). Pushing only the frontend has caused severe data mismatches and broken the API communication.  
- Code Quality & Feature Regression: The UI implementation in the recent commit did not meet the project's quality standards. Furthermore, several existing and stable features were overwritten or completely removed during the push.  
- API Instability & Repository Clutter: The update introduced multiple unhandled API errors and included a large number of unnecessary/unrelated files, cluttering the codebase.

# **4\. Decisions Made**

- Decision 1: The final AI feature will be the "24/7 Virtual Assistant for Rules & Procedures (RAG Chatbot)".  
- Decision 2: The project will strictly proceed according to the baseline of the PA2 requirements.  
- Decision 3: Retain the current account registration flow for new users, all planned changes related to this flow are officially canceled.

# **5\. Task Assigning**

| Task | Assignee | Deadline | Status  |
| :---- | :---- | :---- | :---- |
| Improve UI/UX of Admin, Login/register form | Hồ Phúc Kiên | 28 thg 6, 2026 | In progress |
| Maintenance Request page optimization, Complete Remaining Contract Features | Trần Hoàng Quốc Khánh | 1 thg 7, 2026 | In progress |
| Implement backend APIs for user authentication (login) and account management (lock/unlock)  | Đào Duy Anh | 28 thg 6, 2026 | In progress |
| Fix and improve the UI/UX of the recently implemented features  | Tô Trần Hoàng Triệu | 27 thg 6, 2026 | In progress |
| Implement backend logic for room registration and utility booking, Develop backend for maintenance requests and real-time notification logic  | Đào Duy Anh | 3 thg 7, 2026 | In progress |
| Secure WebSockets (Socket.IO) with JWT validation in [notifications.gateway.ts](http://notifications.gateway.ts),Implement Task Scheduling (Cron Job) for automated OVERDUE invoice updates & notifications  | Trần Huỳnh Mạnh Đạt | 28 thg 6, 2026 | In progress |

# **6\. Next Meeting**

·         Date: 1 thg 7, 2026 21:00 GMT+7  
·         Location: Google Meets  
·         Objective: Review the completed tasks from the current sprint and begin fulfilling the PA2 requirements.

# Thẻ 4

**MEETING MINUTES**

**Project Name:** Smart Dormitory Management System  
**Course:** Introduction to Software Engineering  
**Meeting Date:** 24th June 2026  
T**ime:** 21:00 – 21:30  
**Location/Platform:** Google Meet

# **1\. Attendance**

- Attendees:  
  - Đào Duy Anh \- 24127012  
  - Hồ Phúc Kiên \- 24127067  
  - Trần Huỳnh Mạnh Đạt \- 24127024  
  - Trần Hoàng Quốc Khánh \- 24127057  
- Absentees:  
  - Tô Trần Hoàng Triệu \- 23127  
-  Minute Taker: Trần Huỳnh Mạnh Đạt

# **2\. Meeting Objectives / Agenda**

1\.       Review the current progress of the project proposal.  
2\.       Discussing PA2 requirements.  
3\.       Assign tasks for the upcoming week.

# **3\. Discussion & Progress Updates**

- AI Features Finalization: The team agreed to drop the rule-based roommate matching from the AI section and officially finalized two concrete AI capabilities: "Smart Ticketing (NLP)" and the "RAG Chatbot".  
- PA2 Requirements Review: The team thoroughly analyzed the guidelines for PA2. Core architectural constraints and technical stacks were evaluated to ensure the project alignment is correct from the start.  
- Workflow Adjustments: The team reviewed the proposed changes to the new user registration flow. After evaluating the complexity and system stability, it was agreed to retain the current registration flow instead of pursuing the previously planned modifications.  
- Development Initiation: Initial repository setup and database schema design based on the PA2 requirements have officially commenced.  
- Code Review & Technical Feedback (Addressed to Hoang Trieu): The Team Leader raised several critical technical issues regarding the recent code push that need immediate rectification:  
- Frontend/Backend Desynchronization: Data definitions and JWT configurations were altered on the frontend without applying the mandatory corresponding updates to the backend (schema, services, modules, and controllers). Pushing only the frontend has caused severe data mismatches and broken the API communication.  
- Code Quality & Feature Regression: The UI implementation in the recent commit did not meet the project's quality standards. Furthermore, several existing and stable features were overwritten or completely removed during the push.  
- API Instability & Repository Clutter: The update introduced multiple unhandled API errors and included a large number of unnecessary/unrelated files, cluttering the codebase.

# **4\. Decisions Made**

- Decision 1: The final AI feature will be the "24/7 Virtual Assistant for Rules & Procedures (RAG Chatbot)".  
- Decision 2: The project will strictly proceed according to the baseline of the PA2 requirements.  
- Decision 3: Retain the current account registration flow for new users, all planned changes related to this flow are officially canceled.

# **5\. Task Assigning**

| Task | Assignee | Deadline | Status  |
| :---- | :---- | :---- | :---- |
| Improve UI/UX of Admin, Login/register form | Hồ Phúc Kiên | 28 thg 6, 2026 | In progress |
| Maintenance Request page optimization, Complete Remaining Contract Features | Trần Hoàng Quốc Khánh | 1 thg 7, 2026 | In progress |
| Implement backend APIs for user authentication (login) and account management (lock/unlock)  | Đào Duy Anh | 28 thg 6, 2026 | In progress |
| Fix and improve the UI/UX of the recently implemented features  | Tô Trần Hoàng Triệu | 27 thg 6, 2026 | In progress |
| Implement backend logic for room registration and utility booking, Develop backend for maintenance requests and real-time notification logic  | Đào Duy Anh | 3 thg 7, 2026 | In progress |
| Secure WebSockets (Socket.IO) with JWT validation in [notifications.gateway.ts](http://notifications.gateway.ts),Implement Task Scheduling (Cron Job) for automated OVERDUE invoice updates & notifications  | Trần Huỳnh Mạnh Đạt | 28 thg 6, 2026 | In progress |

# **6\. Next Meeting**

·         Date: 1 thg 7, 2026 21:00 GMT+7  
·         Location: Google Meets  
·         Objective: Review the completed tasks from the current sprint and begin fulfilling the PA2 requirements.

# Thẻ 5

**MEETING MINUTES**

**Project Name:** Smart Dormitory Management System  
**Course:** Introduction to Software Engineering  
**Meeting Date:** 24th June 2026  
T**ime:** 21:00 – 21:30  
**Location/Platform:** Google Meet

# **1\. Attendance**

- Attendees:  
  - Đào Duy Anh \- 24127012  
  - Hồ Phúc Kiên \- 24127067  
  - Trần Huỳnh Mạnh Đạt \- 24127024  
  - Trần Hoàng Quốc Khánh \- 24127057  
- Absentees:  
  - Tô Trần Hoàng Triệu \- 23127  
-  Minute Taker: Trần Huỳnh Mạnh Đạt

# **2\. Meeting Objectives / Agenda**

1\.       Review the current progress of the project proposal.  
2\.       Discussing PA2 requirements.  
3\.       Assign tasks for the upcoming week.

# **3\. Discussion & Progress Updates**

- AI Features Finalization: The team agreed to drop the rule-based roommate matching from the AI section and officially finalized two concrete AI capabilities: "Smart Ticketing (NLP)" and the "RAG Chatbot".  
- PA2 Requirements Review: The team thoroughly analyzed the guidelines for PA2. Core architectural constraints and technical stacks were evaluated to ensure the project alignment is correct from the start.  
- Workflow Adjustments: The team reviewed the proposed changes to the new user registration flow. After evaluating the complexity and system stability, it was agreed to retain the current registration flow instead of pursuing the previously planned modifications.  
- Development Initiation: Initial repository setup and database schema design based on the PA2 requirements have officially commenced.  
- Code Review & Technical Feedback (Addressed to Hoang Trieu): The Team Leader raised several critical technical issues regarding the recent code push that need immediate rectification:  
- Frontend/Backend Desynchronization: Data definitions and JWT configurations were altered on the frontend without applying the mandatory corresponding updates to the backend (schema, services, modules, and controllers). Pushing only the frontend has caused severe data mismatches and broken the API communication.  
- Code Quality & Feature Regression: The UI implementation in the recent commit did not meet the project's quality standards. Furthermore, several existing and stable features were overwritten or completely removed during the push.  
- API Instability & Repository Clutter: The update introduced multiple unhandled API errors and included a large number of unnecessary/unrelated files, cluttering the codebase.

# **4\. Decisions Made**

- Decision 1: The final AI feature will be the "24/7 Virtual Assistant for Rules & Procedures (RAG Chatbot)".  
- Decision 2: The project will strictly proceed according to the baseline of the PA2 requirements.  
- Decision 3: Retain the current account registration flow for new users, all planned changes related to this flow are officially canceled.

# **5\. Task Assigning**

| Task | Assignee | Deadline | Status  |
| :---- | :---- | :---- | :---- |
| Improve UI/UX of Admin, Login/register form | Hồ Phúc Kiên | 28 thg 6, 2026 | In progress |
| Maintenance Request page optimization, Complete Remaining Contract Features | Trần Hoàng Quốc Khánh | 1 thg 7, 2026 | In progress |
| Implement backend APIs for user authentication (login) and account management (lock/unlock)  | Đào Duy Anh | 28 thg 6, 2026 | In progress |
| Fix and improve the UI/UX of the recently implemented features  | Tô Trần Hoàng Triệu | 27 thg 6, 2026 | In progress |
| Implement backend logic for room registration and utility booking, Develop backend for maintenance requests and real-time notification logic  | Đào Duy Anh | 3 thg 7, 2026 | In progress |
| Secure WebSockets (Socket.IO) with JWT validation in [notifications.gateway.ts](http://notifications.gateway.ts),Implement Task Scheduling (Cron Job) for automated OVERDUE invoice updates & notifications  | Trần Huỳnh Mạnh Đạt | 28 thg 6, 2026 | In progress |

# **6\. Next Meeting**

·         Date: 1 thg 7, 2026 21:00 GMT+7  
·         Location: Google Meets  
·         Objective: Review the completed tasks from the current sprint and begin fulfilling the PA2 requirements.

