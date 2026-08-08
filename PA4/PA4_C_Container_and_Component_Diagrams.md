# Software Architecture: Container Diagram and Component Diagrams

**Project:** Dormify – Dormitory Management System  
**Assignment:** PA4-2026  
**Architecture Views:** C4 Model Level 2 and Level 3  
**Source Baseline:** Latest `main` branches of the frontend and backend repositories reviewed on August 2, 2026

---

## 1. Purpose

**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

This section documents the internal software architecture of Dormify using:

- **C4 Level 2 – Container Diagram**
- **C4 Level 3 – Frontend Component Diagram**
- **C4 Level 3 – Backend Component Diagram**

The diagrams reflect the current implementation in:

- `Domitory_Management_Frontend`
- `Domitory_Management_Backend`

All diagrams use standard Mermaid `flowchart` syntax with C4-style labels so that they can be rendered by common Markdown tools.

---

# 2. C4 Level 2 – Container Diagram

**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

## 2.1 Container Diagram

**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

```mermaid
flowchart LR
    %% People
    student["Person<br/><b>Student / Resident</b><br/>Uses accommodation, billing, maintenance,<br/>absence, transfer, checkout, and chatbot features"]

    management["Person<br/><b>Administrative Users</b><br/>System Administrator, Dormitory Manager,<br/>and Floor Manager"]

    maintenanceStaff["Person<br/><b>Maintenance Staff</b><br/>Processes assigned maintenance requests"]

    %% Dormify system boundary
    subgraph dormify["Software System: Dormify Dormitory Management System"]
        web["Container: Web Application<br/><b>Next.js 16, React 19, TypeScript,<br/>Tailwind CSS, Recharts</b><br/><br/>Provides role-based browser interfaces,<br/>route protection, dashboards, forms,<br/>chatbot UI, and notification UI"]

        api["Container: Backend API Application<br/><b>NestJS 11, Node.js, TypeScript</b><br/><br/>Provides REST APIs, JWT authentication,<br/>business logic, authorization, scheduled jobs,<br/>SSE chatbot streaming, and Socket.IO events"]

        database[("Container: Operational Database<br/><b>MongoDB Atlas + Mongoose</b><br/><br/>Stores users, rooms, bookings, contracts,<br/>invoices, maintenance requests, notifications,<br/>knowledge vectors, feedback, and audit data")]
    end

    %% External systems
    google["External System<br/><b>Google Identity Services</b><br/>Verifies Google Sign-In identity tokens"]

    cloudinary["External System<br/><b>Cloudinary</b><br/>Stores maintenance evidence images"]

    ollama["External System / Local AI Runtime<br/><b>Ollama</b><br/>Runs chat generation and embedding models"]

    %% People to web
    student -->|"Uses through web browser<br/>HTTPS"| web
    management -->|"Uses management dashboards<br/>HTTPS"| web
    maintenanceStaff -->|"Uses staff workspace<br/>HTTPS"| web

    %% Frontend to backend
    web -->|"REST-style API requests<br/>HTTP/HTTPS + JSON + JWT"| api
    web -->|"Real-time notifications<br/>Socket.IO / WebSocket"| api
    web -->|"Streams chatbot responses<br/>HTTP Server-Sent Events"| api

    %% Backend to persistence/external
    api -->|"Reads and writes domain data<br/>Mongoose / MongoDB protocol over TLS"| database
    api -->|"Uses Atlas Vector Search and text search<br/>MongoDB aggregation/query"| database
    api -->|"Verifies Google identity token<br/>OAuth 2.0 / HTTPS"| google
    api -->|"Uploads maintenance images<br/>Cloudinary API / HTTPS"| cloudinary
    api -->|"Requests embeddings and generated responses<br/>HTTP / JSON"| ollama
```

---

## 2.2 Container Descriptions

**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

## 2.2.1 Web Application

**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

### Responsibility

The Web Application is the browser-based user interface of Dormify. It provides:

- Public authentication pages
- Student dashboards and resident services
- Administrative dashboards
- Maintenance staff workspace
- Role-based navigation
- Forms and tables for domain operations
- Dashboard charts
- Real-time notification display
- AI chatbot user interface
- Confirmation dialogs, toast messages, and shared visual components

### Technology

| Technology | Purpose |
|---|---|
| Next.js 16 | Application framework, App Router, layouts, routing, and production build |
| React 19 | Interactive UI components |
| TypeScript 5 | Static typing |
| Tailwind CSS 4 | Responsive styling |
| Recharts | Administrative and operational charts |
| Socket.IO Client | Real-time notifications |
| Google OAuth React | Google Sign-In UI |
| Lucide React / React Icons | Icons |

### Communication

The Web Application communicates with:

- The Backend API through HTTP/HTTPS JSON requests
- The Backend Socket.IO gateway through WebSocket or Socket.IO fallback transport
- The Backend chatbot streaming endpoint through Server-Sent Events
- Google Identity Services indirectly during Google Sign-In

JWT data is used for authenticated API calls, role checks, and protected navigation.

---

## 2.2.2 Backend API Application

**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

### Responsibility

The Backend API Application is the main business and security boundary. It provides:

- Local and Google authentication
- JWT creation and verification
- Role-based authorization
- User and profile management
- Room and bed management
- Booking and accommodation registration
- Room assignment
- Contract management
- Invoice and fee management
- Maintenance request processing
- Notification persistence and real-time delivery
- Violation and conduct management
- Temporary absence management
- Room transfer management
- Checkout and refund processing
- Audit logging
- RAG chatbot and personalized student queries
- Scheduled overdue-invoice processing

### Technology

| Technology | Purpose |
|---|---|
| NestJS 11 | Modular backend framework |
| Node.js | Runtime |
| TypeScript | Static typing |
| Mongoose | MongoDB object modeling |
| Passport JWT | Authentication strategy |
| bcrypt | Password hashing |
| class-validator | Request validation |
| Socket.IO | Real-time notifications |
| RxJS | Chatbot streaming event flow |
| NestJS Schedule | Scheduled background jobs |
| Google Auth Library | Google token verification |
| Cloudinary SDK | Image upload |
| Native `fetch` | Calls Ollama HTTP endpoints |

### Communication

The Backend API communicates with:

- The Web Application using HTTP/HTTPS, JSON, SSE, and Socket.IO
- MongoDB Atlas through Mongoose
- Google Identity Services using token verification
- Cloudinary using HTTPS API calls
- Ollama using local or network HTTP calls

The current backend listens on port `3001`.

---

## 2.2.3 Operational Database

**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

### Responsibility

The Operational Database stores all persistent business data, including:

- User accounts and profiles
- Roles and access status
- Rooms and room occupancy
- Bookings
- Room assignments
- Contracts
- Invoices and payment status
- Maintenance requests
- Notifications
- Violations
- Transfers
- Absences
- Checkout records
- Audit logs
- Chatbot knowledge chunks
- Vector embeddings
- Chatbot feedback

### Technology

- MongoDB Atlas
- Mongoose schemas and models
- MongoDB text indexes
- MongoDB Atlas Vector Search

### Communication

Only the Backend API accesses the database. The frontend never connects directly to MongoDB.

The chatbot uses both:

- Vector similarity search
- MongoDB text search

The results are merged before context is sent to the AI runtime.

---

## 2.2.4 Google Identity Services

**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

### Responsibility

Google Identity Services validates Google accounts used for Google Sign-In.

The frontend obtains an identity token and sends it to the backend. The backend verifies the token, resolves or creates the local Dormify account, and returns a Dormify JWT.

### Communication

- OAuth 2.0 / OpenID-style identity token
- HTTPS

---

## 2.2.5 Cloudinary

**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

### Responsibility

Cloudinary stores images attached to maintenance requests.

The backend uploads image data and saves the returned secure URL in MongoDB.

### Communication

- Cloudinary SDK
- HTTPS

---

## 2.2.6 Ollama AI Runtime

**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

### Responsibility

Ollama executes the models used by the chatbot:

- Chat generation model
- Text embedding model

The default models in the current backend are:

- `qwen2.5:3b`
- `nomic-embed-text`

The Ollama endpoint defaults to:

```text
http://localhost:11434
```

It may be changed using environment variables.

### Communication

The backend calls Ollama using HTTP JSON requests for:

- `/api/embeddings`
- Chat generation or streaming operations

Ollama is represented as a separate container-level dependency because it runs as a separate process from NestJS.

---

# 3. C4 Level 3 – Frontend Component Diagram

**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

## 3.1 Scope

**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

The frontend component diagram focuses on the components that best represent the internal structure:

- Authentication and route protection
- Role-based application areas
- Shared UI components
- Notification communication
- Chatbot communication
- Backend API access

The frontend repository uses the Next.js App Router and organizes pages mainly under:

- `app/(auth)`
- `app/student`
- `app/admin`
- `app/staff`
- `app/components`
- `app/context`
- `app/utils`

---

## 3.2 Frontend Component Diagram

**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

```mermaid
flowchart LR
    user["User<br/>Student, Administrator,<br/>Manager, or Maintenance Staff"]

    subgraph frontend["Container: Dormify Web Application<br/>Next.js 16 + React 19 + TypeScript"]

        proxy["Component: Route Protection Proxy<br/><b>proxy.ts</b><br/><br/>Reads authentication information,<br/>protects role-based routes, and redirects<br/>users to the permitted application area"]

        authUI["Component: Authentication UI<br/><b>app/(auth)</b><br/><br/>Provides login, registration,<br/>password-related forms, and Google Sign-In"]

        studentUI["Component: Student Application Area<br/><b>app/student</b><br/><br/>Provides student dashboard, profile,<br/>booking, room, invoice, maintenance,<br/>absence, transfer, checkout, and violation views"]

        adminUI["Component: Administrative Application Area<br/><b>app/admin</b><br/><br/>Provides dashboards and management pages<br/>for rooms, students, bookings, invoices,<br/>maintenance, absences, transfers,<br/>announcements, and permissions"]

        staffUI["Component: Maintenance Staff Area<br/><b>app/staff</b><br/><br/>Displays assigned maintenance work<br/>and supports status updates"]

        sharedUI["Component: Shared UI Components<br/><b>app/components</b><br/><br/>RoleGuard, NotificationBell, ToastProvider,<br/>ConfirmProvider, room filters, bed map,<br/>avatar viewer, and reusable controls"]

        socketContext["Component: Socket Context<br/><b>SocketContext.tsx</b><br/><br/>Creates the authenticated Socket.IO connection<br/>and exposes it to React components"]

        chatbotWidget["Component: Chatbot Widget<br/><b>ChatbotWidget.tsx</b><br/><br/>Maintains conversation history, sends questions,<br/>renders streamed text, status, sources,<br/>invoice cards, and feedback controls"]

        apiAccess["Component: Backend API Access<br/><b>Fetch-based client logic</b><br/><br/>Sends JWT-authenticated HTTP requests<br/>and maps JSON responses to page state"]

        stateAndFeedback["Component: Client State and Feedback<br/><b>React state and providers</b><br/><br/>Coordinates UI state, confirmations,<br/>toasts, loading states, and local interaction data"]
    end

    backend["Container: NestJS Backend API<br/>REST + SSE + Socket.IO"]

    google["External System<br/>Google Identity Services"]

    user -->|"Navigates"| proxy
    proxy -->|"Allows or redirects"| authUI
    proxy -->|"Allows authorized student routes"| studentUI
    proxy -->|"Allows authorized management routes"| adminUI
    proxy -->|"Allows authorized staff routes"| staffUI

    authUI -->|"Google login interaction"| google
    authUI -->|"Login and registration requests"| apiAccess

    studentUI -->|"Uses reusable components"| sharedUI
    adminUI -->|"Uses reusable components"| sharedUI
    staffUI -->|"Uses reusable components"| sharedUI

    studentUI -->|"Reads and changes domain data"| apiAccess
    adminUI -->|"Reads and changes domain data"| apiAccess
    staffUI -->|"Reads and changes maintenance data"| apiAccess

    sharedUI -->|"Consumes notifications and shared state"| socketContext
    sharedUI -->|"Uses dialogs and toast state"| stateAndFeedback

    studentUI -->|"Opens chatbot"| chatbotWidget
    adminUI -->|"May access chatbot/feedback features"| chatbotWidget

    chatbotWidget -->|"SSE stream, ask, and feedback requests"| backend
    apiAccess -->|"HTTP/HTTPS JSON + JWT"| backend
    socketContext -->|"Socket.IO handshake with JWT"| backend
```

---

## 3.3 Frontend Component Descriptions

**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

## 3.3.1 Route Protection Proxy

**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

### Responsibility

The Route Protection Proxy executes before protected page access. It:

- Checks authentication information
- Reads role data
- Protects student, administrative, and staff URL areas
- Redirects unauthenticated users
- Redirects authenticated users away from areas they are not authorized to use

### Relationships

- Receives navigation requests from the browser
- Routes users to Authentication UI or the appropriate role-specific area
- Complements backend authorization but does not replace it

---

## 3.3.2 Authentication UI

**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

### Responsibility

The Authentication UI provides:

- Local login
- User registration
- Google Sign-In
- Authentication-related feedback and redirection

### Relationships

- Communicates with Google Identity Services for the client-side Google login flow
- Sends login and registration data to the Backend API
- Receives a Dormify JWT and role information
- Redirects the user to the correct application area

---

## 3.3.3 Student Application Area

**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

### Responsibility

The Student Application Area contains resident-facing pages and workflows, including:

- Dashboard
- Profile
- Room and bed information
- Accommodation booking
- Contract information
- Invoices and current simulated payment flow
- Maintenance request submission
- Maintenance status and rating
- Absence reporting
- Transfer requests
- Checkout requests
- Violation and conduct information
- Notifications
- Chatbot access

### Relationships

- Uses Shared UI Components
- Sends HTTP requests through Backend API Access
- Uses Socket Context for real-time updates
- Opens the Chatbot Widget

---

## 3.3.4 Administrative Application Area

**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

### Responsibility

The Administrative Application Area supports administrators and management roles. It provides pages for:

- Dashboard statistics
- Student management
- Room management
- Booking management
- Invoice management
- Maintenance management
- Absence management
- Transfer management
- Announcements
- Permissions and account access
- Profile management

### Relationships

- Uses Shared UI Components
- Calls the Backend API
- Receives Socket.IO notifications
- Displays statistics using Recharts

---

## 3.3.5 Maintenance Staff Area

**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

### Responsibility

The Maintenance Staff Area provides the focused operational interface for maintenance workers. It:

- Displays assigned requests
- Shows request details and evidence
- Allows status updates
- Supports completion or rejection information

### Relationships

- Calls the maintenance endpoints in the Backend API
- Receives real-time assignment and status notifications
- Uses common feedback components

---

## 3.3.6 Shared UI Components

**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

### Responsibility

Shared UI Components provide reusable application behavior and presentation.

Important current components include:

| Component | Responsibility |
|---|---|
| `RoleGuard` | Restricts rendering based on role |
| `NotificationBell` | Displays notifications |
| `ToastProvider` | Displays transient success and error messages |
| `ConfirmProvider` | Provides confirmation dialogs |
| `ChatbotWidget` | Provides AI assistant interaction |
| `RoomFilterBar` | Filters room data |
| `VisualBedMap` | Displays room-bed occupancy visually |
| `AvatarLightbox` | Enlarges profile or evidence images |

### Relationships

These components are used by student, administrative, and staff pages.

---

## 3.3.7 Socket Context

**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

### Responsibility

The Socket Context:

- Creates a Socket.IO client connection
- Sends the JWT during the connection handshake
- Makes the socket instance available through React context
- Allows components such as the notification bell to listen for events

### Relationships

- Connects directly to the Backend Notification Gateway
- Is consumed by shared and role-specific frontend components

---

## 3.3.8 Chatbot Widget

**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

### Responsibility

The Chatbot Widget is a substantial frontend component that:

- Sends chatbot questions
- Includes recent conversation history
- Consumes Server-Sent Events
- Displays streaming text
- Displays processing status
- Displays retrieved source labels
- Displays structured invoice cards
- Displays not-found suggestions
- Submits positive or negative answer feedback

### Relationships

- Calls `/api/chatbot/stream`
- Calls chatbot feedback endpoints
- Receives typed streaming events from the backend
- Is embedded in authenticated application layouts

---

## 3.3.9 Backend API Access

**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

### Responsibility

The frontend currently uses fetch-based request logic to:

- Add authorization information
- Send JSON payloads
- Parse responses
- Update page-level React state
- Report request errors to the UI

### Relationships

- Used by authentication and all role-specific application areas
- Communicates only with the NestJS Backend API

---

# 4. C4 Level 3 – Backend Component Diagram

**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

## 4.1 Scope

**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

The backend contains many domain modules. The Level 3 diagram focuses on the most representative architectural components:

- API and security layer
- Core residence-management modules
- Finance and contract modules
- Maintenance and notification modules
- Checkout and audit modules
- AI chatbot module
- Persistence integration
- External integration adapters

The current root `AppModule` registers:

- `AuthModule`
- `UsersModule`
- `RoomsModule`
- `BookingsModule`
- `InvoicesModule`
- `ContractsModule`
- `MaintenanceModule`
- `NotificationsModule`
- `ViolationsModule`
- `TransfersModule`
- `AbsencesModule`
- `CheckoutsModule`
- `AssignmentsModule`
- `AuditLogsModule`
- `ChatbotModule`

---

## 4.2 Backend Component Diagram

**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

```mermaid
flowchart TB
    frontend["Container: Next.js Web Application"]

    subgraph backend["Container: Dormify Backend API<br/>NestJS 11 + TypeScript"]

        controllers["Component: HTTP Controllers<br/><b>NestJS Controllers</b><br/><br/>Expose REST-style endpoints,<br/>receive DTOs, and return JSON responses"]

        security["Component: Authentication and Authorization<br/><b>AuthModule, JwtAuthGuard, RolesGuard</b><br/><br/>Authenticates local and Google users,<br/>issues JWTs, validates tokens,<br/>and enforces role access"]

        userResidence["Component: User and Residence Management<br/><b>Users, Rooms, Bookings, Assignments</b><br/><br/>Manages accounts, profiles, rooms,<br/>beds, booking applications, and allocations"]

        contractFinance["Component: Contract and Finance Management<br/><b>Contracts, Invoices</b><br/><br/>Manages contracts, fee records,<br/>invoice status, mock payment confirmation,<br/>and overdue processing"]

        residentLifecycle["Component: Resident Lifecycle Management<br/><b>Transfers, Absences, Checkouts</b><br/><br/>Processes temporary absences,<br/>room transfers, checkout, deposit,<br/>and departure-related workflows"]

        conduct["Component: Conduct Management<br/><b>Violations</b><br/><br/>Stores and manages resident violation<br/>and conduct-related records"]

        maintenance["Component: Maintenance Management<br/><b>MaintenanceModule</b><br/><br/>Creates repair requests, validates evidence,<br/>assigns staff, controls status transitions,<br/>stores resolution data, and accepts ratings"]

        notifications["Component: Notification Service and Gateway<br/><b>NotificationsModule + Socket.IO Gateway</b><br/><br/>Persists notifications, authenticates socket clients,<br/>joins private user rooms, and emits events"]

        audit["Component: Audit Logging<br/><b>AuditLogsModule</b><br/><br/>Records important administrative<br/>and business operations for traceability"]

        chatbot["Component: RAG Chatbot<br/><b>ChatbotModule</b><br/><br/>Handles questions, conversation history,<br/>personalized data lookup, hybrid retrieval,<br/>SSE responses, ingestion, and feedback"]

        scheduler["Component: Scheduled Jobs<br/><b>NestJS Schedule</b><br/><br/>Runs recurring business tasks such as<br/>changing expired pending invoices to overdue"]

        persistence["Component: Persistence Layer<br/><b>Mongoose Models and Schemas</b><br/><br/>Maps domain objects to MongoDB collections<br/>and provides query and aggregation access"]

        cloudinaryAdapter["Component: Cloudinary Integration<br/><b>Cloudinary SDK</b><br/><br/>Uploads maintenance evidence images"]

        googleAdapter["Component: Google Identity Adapter<br/><b>google-auth-library</b><br/><br/>Verifies Google identity tokens"]

        ollamaAdapter["Component: Ollama Client<br/><b>HTTP fetch client</b><br/><br/>Requests embeddings and generated responses"]
    end

    mongo[("Container: MongoDB Atlas<br/>Operational data + vector index")]
    cloudinary["External System: Cloudinary"]
    google["External System: Google Identity Services"]
    ollama["External System / Process: Ollama"]

    frontend -->|"HTTP JSON requests"| controllers
    frontend -->|"Socket.IO connection"| notifications
    frontend -->|"SSE chatbot stream"| chatbot

    controllers -->|"Delegates protected requests"| security
    controllers -->|"Calls business services"| userResidence
    controllers -->|"Calls business services"| contractFinance
    controllers -->|"Calls business services"| residentLifecycle
    controllers -->|"Calls business services"| conduct
    controllers -->|"Calls business services"| maintenance
    controllers -->|"Calls chatbot operations"| chatbot

    security -->|"Reads/updates users"| persistence
    security -->|"Verifies Google token"| googleAdapter

    userResidence -->|"Reads/writes entities"| persistence
    contractFinance -->|"Reads/writes contracts and invoices"| persistence
    residentLifecycle -->|"Reads/writes lifecycle records"| persistence
    conduct -->|"Reads/writes violations"| persistence
    maintenance -->|"Reads/writes maintenance records"| persistence
    notifications -->|"Stores and reads notifications"| persistence
    audit -->|"Stores audit records"| persistence
    chatbot -->|"Reads knowledge, feedback, users,<br/>contracts, invoices, and rooms"| persistence
    scheduler -->|"Updates expired invoices"| contractFinance

    userResidence -->|"Creates activity records"| audit
    contractFinance -->|"Creates notifications"| notifications
    residentLifecycle -->|"Creates notifications and audit records"| notifications
    residentLifecycle -->|"Writes trace records"| audit
    maintenance -->|"Emits request and status events"| notifications
    maintenance -->|"Uploads evidence"| cloudinaryAdapter
    chatbot -->|"Requests embedding and chat output"| ollamaAdapter

    persistence -->|"Mongoose / MongoDB protocol"| mongo
    chatbot -->|"Atlas Vector Search and text search"| mongo
    cloudinaryAdapter -->|"HTTPS API"| cloudinary
    googleAdapter -->|"HTTPS token verification"| google
    ollamaAdapter -->|"HTTP JSON"| ollama
```

---

## 4.3 Backend Component Descriptions

**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

## 4.3.1 HTTP Controllers

**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

### Responsibility

NestJS controllers form the HTTP entry layer. They:

- Define routes
- Receive query, path, and body parameters
- Apply guards and role decorators
- Receive authenticated user data
- Delegate work to services
- Return JSON or SSE responses

The chatbot controller also provides a Server-Sent Events endpoint.

### Relationships

- Called by the frontend
- Uses authentication and authorization guards
- Delegates to domain services

---

## 4.3.2 Authentication and Authorization

**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

### Responsibility

The security component includes:

- Local authentication
- Google authentication
- Password verification using bcrypt
- JWT generation
- JWT request authentication
- Socket authentication support
- Role-based authorization
- Locked-account enforcement

### Relationships

- Reads user records through Mongoose
- Uses the Google Identity Adapter
- Protects domain controllers
- Supplies authenticated user identity to services

---

## 4.3.3 User and Residence Management

**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

### Responsibility

This component groups modules that manage:

- User accounts
- Student profiles
- Dormitory rooms
- Beds and occupancy
- Booking requests
- Resident-room assignments

### Relationships

- Persists users, rooms, bookings, and assignments
- Supports contracts, invoices, transfers, and maintenance
- May record administrative actions in the Audit Logging component

---

## 4.3.4 Contract and Finance Management

**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

### Responsibility

This component manages:

- Dormitory contracts
- Monthly fee records
- Electricity and water fees
- Invoice creation
- Invoice status
- Administrative payment confirmation
- Current mock-payment flow
- Overdue invoice detection

### Relationships

- Reads resident and room data
- Stores contracts and invoices
- Sends payment and overdue notifications
- Is invoked by Scheduled Jobs

No real third-party payment gateway is currently integrated.

---

## 4.3.5 Resident Lifecycle Management

**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

### Responsibility

This component handles processes that occur during or at the end of residence:

- Temporary absence reports
- Room transfer requests
- Checkout requests
- Deposit and refund-related data
- Resident departure processing

### Relationships

- Uses user, room, assignment, contract, and invoice data
- Sends notifications
- Records traceable operations through audit logs

---

## 4.3.6 Conduct Management

**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

### Responsibility

The Violations module manages:

- Resident violations
- Conduct-related records
- Administrative review data

### Relationships

- Associates violation data with users
- Exposes records to student and management interfaces
- Stores records through Mongoose

---

## 4.3.7 Maintenance Management

**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

### Responsibility

The Maintenance module:

- Creates maintenance requests
- Accepts image evidence
- Validates the requesting student
- Stores room and requester information
- Supports staff assignment
- Restricts staff to assigned requests
- Enforces maintenance status transitions
- Requires rejection reasons
- Stores completion and resolution notes
- Accepts resident ratings
- Produces notifications throughout the workflow

### Relationships

- Uses Cloudinary Integration for images
- Uses Persistence Layer for maintenance records
- Sends events through Notification Service and Gateway
- Uses user and room data

---

## 4.3.8 Notification Service and Gateway

**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

### Responsibility

This component combines persisted notifications and real-time delivery.

The gateway:

1. Receives the JWT in the Socket.IO handshake.
2. Verifies the token.
3. Disconnects invalid clients.
4. Joins the user to a private room named `user_<userId>`.
5. Emits `newNotification` events to individual users or broadcasts.

### Relationships

- Receives notification requests from invoice, maintenance, and lifecycle modules
- Stores notifications in MongoDB
- Sends events to the frontend Socket Context

---

## 4.3.9 Audit Logging

**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

### Responsibility

The Audit Logs module records important operations to improve:

- Traceability
- Accountability
- Administrative review
- Debugging and investigation

### Relationships

- Receives significant actions from business modules
- Stores records in MongoDB
- May be queried by authorized management users

---

## 4.3.10 RAG Chatbot

**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

### Responsibility

The Chatbot module provides an authenticated AI assistant.

Its current responsibilities include:

- Receiving normal and streaming questions
- Accepting recent chat history
- Detecting follow-up questions
- Handling greetings without unnecessary retrieval
- Generating embeddings
- Running MongoDB Atlas Vector Search
- Running MongoDB text search
- Merging vector and keyword results
- Applying relevance thresholds
- Returning source labels
- Reading personalized student data
- Returning structured invoice cards
- Streaming status, text, sources, invoices, and not-found events
- Storing thumbs-up and thumbs-down feedback
- Allowing administrators to view feedback
- Allowing administrators to trigger knowledge ingestion

### Personalized data

The chatbot may read:

- User profile data
- Contract data
- Invoice data
- Room data

This enables questions such as:

- Which room am I staying in?
- What is my contract status?
- Which invoices are unpaid?

### Relationships

- Protected by JWT and role guards
- Reads several MongoDB collections
- Uses MongoDB Atlas Vector Search
- Calls Ollama for embeddings and chat generation
- Streams responses directly to the frontend Chatbot Widget

---

## 4.3.11 Scheduled Jobs

**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

### Responsibility

The scheduler runs internal recurring operations.

A representative current task checks invoices and:

- Finds pending invoices past their due date
- Changes them to `OVERDUE`
- Records the overdue time
- Creates student notifications

### Relationships

- Calls Contract and Finance Management
- Uses Notification Service
- Runs inside the NestJS process

---

## 4.3.12 Persistence Layer

**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

### Responsibility

The Persistence Layer consists of Mongoose schemas and models. It:

- Maps TypeScript domain structures to MongoDB documents
- Defines collection relationships
- Performs queries and updates
- Supports population of referenced documents
- Runs aggregation pipelines
- Supports text and vector retrieval for chatbot knowledge

### Relationships

All business modules access MongoDB through this layer.

---

## 4.3.13 External Integration Adapters

**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

### Google Identity Adapter

Verifies Google ID tokens and returns trusted identity information to AuthModule.

### Cloudinary Integration

Uploads maintenance images and returns secure URLs.

### Ollama Client

Calls the configured Ollama endpoint to:

- Generate embeddings
- Generate chatbot responses
- Support streamed AI output

Adapters keep external-service code separate from most domain logic.

---

# 5. Main Communication Mechanisms

**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

| Source | Destination | Mechanism | Purpose |
|---|---|---|---|
| Browser | Next.js Web Application | HTTPS | Loads pages and user interface |
| Web Application | Backend API | HTTP/HTTPS + JSON | Authentication and business operations |
| Web Application | Backend API | JWT | Authenticated requests |
| Socket Context | Notification Gateway | Socket.IO | Real-time notification delivery |
| Chatbot Widget | Chatbot Controller | HTTP + SSE | Streaming chatbot responses |
| Backend | MongoDB Atlas | Mongoose / MongoDB TLS | Persistent domain data |
| Chatbot | MongoDB Atlas | Vector Search + text search | RAG knowledge retrieval |
| Backend | Google Identity Services | HTTPS | Google token verification |
| Maintenance | Cloudinary | HTTPS API | Evidence image storage |
| Chatbot | Ollama | HTTP + JSON | Embedding and text generation |

---

# 6. Important Implementation Notes

**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

## 6.1 Frontend authorization is not the final security boundary

**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

The frontend uses:

- Route protection
- Role guards
- Conditional navigation

However, all sensitive operations must also be protected by backend guards and role checks. A user may bypass frontend UI restrictions by sending requests directly.

---

## 6.2 The chatbot is now an active implemented component

**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

Unlike an earlier architecture draft, the current backend registers `ChatbotModule` in `AppModule`.

The current implementation uses:

- Ollama
- `qwen2.5:3b`
- `nomic-embed-text`
- MongoDB Atlas Vector Search
- MongoDB text search
- Server-Sent Events
- Chat feedback persistence
- Personalized invoice, contract, room, and user data

Therefore, Ollama and the RAG Chatbot must now appear in the architecture diagrams.

---

## 6.3 Payment remains internal

**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

Invoice payment currently uses:

- A simulated student payment action
- Administrative payment confirmation

No real banking, card, wallet, or payment-gateway container should be shown until one is integrated in source code.

---

## 6.4 The database is one logical container

**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

Although MongoDB stores many collections, C4 Level 2 represents it as one database container.

Individual schemas and collections belong in code-level or data-model documentation rather than separate Level 2 containers.

---

## 6.5 Scheduled processing is part of the backend container

**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

NestJS Schedule runs in the backend process. It is therefore modeled as a Level 3 backend component, not as a separate Level 2 container.

---

# 7. Architecture Consistency Checklist

**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

Before submission, the team should verify:

- [ ] `AppModule` still registers the modules shown in this document.
- [ ] Frontend directories still match the component areas shown.
- [ ] Chatbot still uses Ollama and MongoDB Atlas Vector Search.
- [ ] No real payment gateway has been added without updating the diagrams.
- [ ] Any new external email, OTP, storage, or AI service is added to Level 1 and Level 2.
- [ ] Any new independently deployed application is added as a Level 2 container.
- [ ] New major frontend or backend modules are reflected in Level 3 diagrams.
- [ ] Communication labels match the implementation protocol.
- [ ] Technology versions are checked before the final PA4 submission.

---

# 8. Conclusion

**Performed by:** Trần Huỳnh Mạnh Đạt | **Reviewed by:** Đào Duy Anh | **Edited by:** Trần Huỳnh Mạnh Đạt

The current Dormify architecture consists of three main internal containers:

1. A Next.js web application
2. A NestJS backend API application
3. A MongoDB Atlas operational database

The system integrates with:

- Google Identity Services
- Cloudinary
- Ollama

The frontend is organized into authentication, student, administrative, maintenance staff, shared component, socket, and chatbot areas.

The backend follows a modular NestJS architecture and separates authentication, residence management, finance, maintenance, notifications, resident lifecycle, audit logging, and RAG chatbot responsibilities.

These diagrams are aligned with the source code reviewed on August 2, 2026 and should be updated whenever the implementation changes.
