# Use-Case Model — Dormify (Dormitory Management System)

<!-- Performed by: <member>; Reviewed by: <member>; Edited by: <member> -->

> PA3 Section C. Diagrams are drawn in Mermaid (`flowchart` syntax: rectangles = actors, stadium shapes = use cases, dashed arrows = `«include»`/`«extend»`). All functional requirements FR01–FR30 from the vision/spec document are covered; traceability table at the end.

## Actors

| Actor | Description |
| --- | --- |
| **Student** | Dormitory resident: registers, books rooms, pays invoices, reports incidents. |
| **System Admin** | Full control: accounts, permissions, system logs, dashboards. |
| **Dormitory Manager** | Day-to-day operations: rooms, contracts, finance, discipline. |
| **Maintenance Staff** | Handles repair requests assigned to them. |
| **System (scheduler)** | Automated cron jobs: contract-expiry reminders, overdue invoices. |

`System Admin` and `Dormitory Manager` share most operational use cases (generalized as **Manager** in the diagrams).

## 1. Authentication & Profile (FR01–FR03)

```mermaid
flowchart LR
    Student["👤 Student"]
    Guest["👤 Guest"]

    UC01(["UC-01 Register account"])
    UC02(["UC-02 Log in"])
    UC02a(["UC-02a Log in with Google"])
    UC02b(["UC-02b Reset forgotten password"])
    UC03(["UC-03 View & update personal profile"])
    UCAuth(["Authenticate JWT"])

    Guest --> UC01
    Guest --> UC02
    UC02a -.->|«extend»| UC02
    UC02b -.->|«extend»| UC02
    Student --> UC03
    UC03 -.->|«include»| UCAuth
```

## 2. System Administration (FR04–FR08)

```mermaid
flowchart LR
    Admin["👤 System Admin"]

    UC04(["UC-04 Lock / unlock / delete user account"])
    UC05(["UC-05 Manage roles & access control"])
    UC06(["UC-06 View system audit logs"])
    UC06a(["UC-06a Search / filter logs"])
    UC07(["UC-07 View admin dashboard"])
    UC08(["UC-08 Back up & restore data (planned)"])

    Admin --> UC04
    Admin --> UC05
    Admin --> UC06
    UC06a -.->|«extend»| UC06
    Admin --> UC07
    Admin -.-> UC08
```

## 3. Room Booking & Allocation (FR09–FR13)

```mermaid
flowchart LR
    Student["👤 Student"]
    Manager["👤 Manager (Admin / Dorm Manager)"]

    UC09(["UC-09 Manage room catalog (CRUD)"])
    UC10(["UC-10 Manage student records"])
    UC11(["UC-11 Search & book a room"])
    UC12(["UC-12 Approve / reject booking"])
    UC13(["UC-13 Run automatic room assignment"])
    UC14(["UC-14 Request room transfer"])
    UC15(["UC-15 Approve / reject transfer"])
    UCContract(["Create contract"])
    UCNotify(["Send realtime notification"])

    Manager --> UC09
    Manager --> UC10
    Student --> UC11
    Manager --> UC12
    Manager --> UC13
    Student --> UC14
    Manager --> UC15

    UC12 -.->|«include»| UCContract
    UC13 -.->|«include»| UCContract
    UC12 -.->|«include»| UCNotify
    UC13 -.->|«include»| UCNotify
    UC15 -.->|«include»| UCNotify
```

## 4. Contracts & Checkout (FR14–FR21)

```mermaid
flowchart LR
    Student["👤 Student"]
    Manager["👤 Manager"]
    Cron["⏰ System (scheduler)"]

    UC16(["UC-16 View contract"])
    UC17(["UC-17 Extend contract"])
    UC18(["UC-18 Terminate contract"])
    UC19(["UC-19 Export contract to PDF"])
    UC20(["UC-20 Request room checkout"])
    UC21(["UC-21 Inspect assets & record damages"])
    UC22(["UC-22 Complete checkout & refund deposit"])
    UC23(["UC-23 Remind expiring contracts"])
    UCComp(["Calculate compensation"])

    Student --> UC16
    Student --> UC17
    Student --> UC18
    Student --> UC19
    Student --> UC20
    Manager --> UC21
    Manager --> UC22
    Cron --> UC23

    UC22 -.->|«include»| UC21
    UC22 -.->|«include»| UCComp
    UC22 -.->|«include»| UC18
```

## 5. Finance (FR22–FR24)

```mermaid
flowchart LR
    Student["👤 Student"]
    Manager["👤 Manager"]
    Cron["⏰ System (scheduler)"]

    UC24(["UC-24 Create invoice / bulk-generate by meter readings"])
    UC25(["UC-25 View & pay invoice (mock gateway)"])
    UC26(["UC-26 Mark invoice as paid"])
    UC27(["UC-27 View debt summary by room"])
    UC28(["UC-28 Send debt reminders"])
    UC29(["UC-29 View revenue report"])
    UC30(["UC-30 Mark overdue invoices"])

    Manager --> UC24
    Student --> UC25
    Manager --> UC26
    Manager --> UC27
    Manager --> UC28
    Manager --> UC29
    Cron --> UC30

    UC28 -.->|«extend»| UC27
```

## 6. Maintenance (FR26–FR28)

```mermaid
flowchart LR
    Student["👤 Student"]
    Manager["👤 Manager"]
    Staff["👤 Maintenance Staff"]

    UC31(["UC-31 Report incident (photo + priority)"])
    UC32(["UC-32 Assign request to staff"])
    UC33(["UC-33 View assigned jobs"])
    UC34(["UC-34 Update repair progress"])
    UC35(["UC-35 Rate completed repair (1–5 stars)"])

    Student --> UC31
    Manager --> UC32
    Staff --> UC33
    Staff --> UC34
    Manager --> UC34
    Student --> UC35

    UC35 -.->|«extend»| UC34
```

## 7. Residency Rules & Conduct (FR25, FR29–FR30)

```mermaid
flowchart LR
    Student["👤 Student"]
    Manager["👤 Manager"]

    UC36(["UC-36 Declare overnight absence"])
    UC37(["UC-37 Approve / reject absence"])
    UC38(["UC-38 Record rule violation"])
    UC39(["UC-39 Deduct conduct points"])
    UC40(["UC-40 View own violations & conduct score"])

    Student --> UC36
    Manager --> UC37
    Manager --> UC38
    Student --> UC40

    UC38 -.->|«include»| UC39
```

## Traceability: Use Cases ↔ Functional Requirements

| FR | Use case(s) | Status |
| --- | --- | --- |
| FR01 | UC-01 | ✅ |
| FR02 | UC-02, UC-02a, UC-02b | ✅ |
| FR03 | UC-03 | ✅ |
| FR04 | UC-04 | ✅ |
| FR05 | UC-05 | 🔶 fixed roles only |
| FR06 | UC-06, UC-06a | ✅ |
| FR07 | UC-07 | ✅ |
| FR08 | UC-08 | ⬜ planned |
| FR09 | UC-09 | ✅ |
| FR10 | UC-10 | ✅ |
| FR11 | UC-11, UC-12 | ✅ |
| FR12 | UC-13 | ✅ |
| FR13 | UC-14, UC-15 | ✅ |
| FR14–FR17 | UC-16 … UC-19, UC-23 | ✅ |
| FR18–FR21 | UC-20 … UC-22 | ✅ |
| FR22 | UC-24, UC-25, UC-26, UC-30 | ✅ |
| FR23 | UC-27, UC-28 | ✅ |
| FR24 | UC-29 | ✅ |
| FR25 | UC-36, UC-37 | ✅ |
| FR26 | UC-31 | ✅ |
| FR27 | UC-32, UC-33 | ✅ |
| FR28 | UC-34, UC-35 | ✅ |
| FR29 | UC-38 | ✅ |
| FR30 | UC-39, UC-40 | ✅ |
