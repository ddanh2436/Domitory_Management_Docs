---
description: "Task list for Khiếu nại & Thu hồi vi phạm nề nếp"
---

# Tasks: Khiếu nại & Thu hồi vi phạm nề nếp

**Input**: Design documents from `specs/002-violation-appeal-revocation/`

**Prerequisites**: plan.md, spec.md, data-model.md, contracts/violations-api.md, research.md

**Tests**: PA4 yêu cầu kèm test → có task test bắt buộc (T015) + script E2E (T017).

**Organization**: Nhóm theo user story để triển khai/kiểm thử độc lập.

## Format: `[ID] [P?] [Story] Description`

- **[P]**: Chạy song song (khác file, không phụ thuộc)
- **[Story]**: US1 / US2 / US3 / US4

## Path Conventions

- Backend: `src/backend/src/violations/`
- Frontend: `src/frontend/app/`

---

## Phase 1: Setup

- [ ] T001 Xác nhận môi trường chạy được và có sẵn 1 STUDENT, 1 ADMIN + 2–3 vi phạm mẫu (xem quickstart.md).

---

## Phase 2: Foundational (Blocking)

**⚠️ CRITICAL**: mọi story đọc/ghi các trường DB mới → làm trước.

- [x] T002 [Foundation] Tạo `src/backend/src/violations/violations.enum.ts` với `VIOLATION_STATUS` (ACTIVE, APPEAL_PENDING, REVOKED, APPEAL_REJECTED).
- [x] T003 [Foundation] Mở rộng `schemas/violation.schema.ts`: thêm `status` (default ACTIVE), `appealReason`, `appealedAt`, `reviewNote`, `reviewedBy`, `reviewedAt`.
- [x] T004 [P] [Foundation] Tạo `dto/appeal-violation.dto.ts` (reason: IsString, IsNotEmpty, MaxLength 500).
- [x] T005 [P] [Foundation] Tạo `dto/review-appeal.dto.ts` (decision: IsIn ['ACCEPT','REJECT']; reviewNote?: MaxLength 500).
- [x] T006 [Foundation] `violations.service.ts`: thêm helper `restoreScore(violation)` — hoàn `points` cho `behaviorScore` (min 100), chỉ khi chuyển sang REVOKED (idempotent).

**Checkpoint**: schema/enum/DTO/helper sẵn sàng.

---

## Phase 3: User Story 1 — Sinh viên khiếu nại (Priority: P1) 🎯 MVP

**Goal**: Sinh viên gửi khiếu nại kèm lý do; vi phạm chuyển APPEAL_PENDING; admin được báo.

**Independent Test**: khiếu nại thiếu lý do bị chặn; có lý do → APPEAL_PENDING.

- [x] T007 [US1] `violations.service.ts` → `appealViolation(studentId, id, reason)`: kiểm quyền sở hữu + status ACTIVE + reason khác rỗng; set APPEAL_PENDING + appealReason + appealedAt; thông báo admin.
- [x] T008 [US1] `violations.controller.ts` → `POST :id/appeal` (STUDENT) dùng `AppealViolationDto`.
- [x] T009 [P] [US1] Frontend `student/profile/page.tsx`: thêm `status`/`appealReason`/`reviewNote` vào interface Violation; nút **Khiếu nại** (chỉ khi ACTIVE) + modal nhập lý do + hiển thị badge trạng thái.

**Checkpoint**: US1 chạy end-to-end.

---

## Phase 4: User Story 2 — Admin duyệt khiếu nại (Priority: P1)

**Goal**: Admin ACCEPT (thu hồi + hoàn điểm) hoặc REJECT (giữ điểm) khiếu nại đang chờ.

**Independent Test**: ACCEPT hoàn điểm đúng (≤100); REJECT giữ điểm.

- [x] T010 [US2] `violations.service.ts` → `reviewAppeal(id, decision, reviewNote)`: chỉ khi APPEAL_PENDING; ACCEPT → REVOKED + restoreScore + notify; REJECT → APPEAL_REJECTED + reviewNote + notify.
- [x] T011 [US2] `violations.controller.ts` → `PATCH :id/review` (ADMIN, DORMITORY_MANAGER) dùng `ReviewAppealDto`.
- [x] T012 [US2] Frontend `admin/violations/page.tsx` (mới): hàng đợi khiếu nại + nút Chấp nhận/Từ chối (modal ghi chú); hiển thị lý do khiếu nại.

---

## Phase 5: User Story 3 — Thu hồi trực tiếp (Priority: P2)

**Goal**: Admin thu hồi vi phạm ghi nhầm + hoàn điểm; idempotent.

**Independent Test**: revoke lần đầu hoàn điểm; revoke lại không cộng thêm.

- [x] T013 [US3] `violations.service.ts` → `revokeViolation(id)`: chặn nếu đã REVOKED; set REVOKED + restoreScore + reviewedAt + notify. Controller `DELETE :id` (ADMIN, DORMITORY_MANAGER).
- [x] T014 [P] [US3] Frontend `admin/violations/page.tsx`: nút **Thu hồi** trên vi phạm chưa REVOKED.

---

## Phase 6: User Story 4 — Hàng đợi & tổng quan (Priority: P3)

**Goal**: Màn hình admin liệt kê tất cả vi phạm + lọc trạng thái.

- [x] T014b [US4] `violations.service.ts` → `findAll(status?)` + controller `GET /` (ADMIN, DORMITORY_MANAGER), populate student/markedBy, sort mới nhất.
- [x] T014c [P] [US4] Frontend `admin/violations/page.tsx`: bảng danh sách + bộ lọc trạng thái + thẻ thống kê; thêm NavItem trong `admin/layout.tsx`.

---

## Phase 7: Testing & Polish (bắt buộc theo PA4)

- [x] T015 [P] Viết `src/backend/src/violations/violations.service.spec.ts` (Jest): appeal hợp lệ/sai trạng thái/không sở hữu; review ACCEPT hoàn điểm + chặn 100; review REJECT giữ điểm; revoke idempotent.
- [x] T016 Build: `cd src/backend && npm run build`; `cd src/frontend && npm run build`. Sửa lỗi type nếu có.
- [x] T017 Script E2E headless: seed dữ liệu qua Mongoose, mint JWT, gọi API thật cho 4 kịch bản, kiểm DB (điểm hoàn đúng, idempotent), rồi dọn sạch.

---

## Dependencies & Execution Order

- **Setup (T001)** → **Foundational (T002–T006, BLOCKS)** → **US1 (T007–T009)** → **US2 (T010–T012)** → US3 (T013–T014) → US4 (T014b–T014c) → **Test/Polish (T015–T017)**.
- Backend service/controller là tiên quyết cho frontend cùng story.
- `restoreScore` (T006) là tiên quyết cho US2-ACCEPT và US3.

## Implementation Strategy

- **MVP = Phase 1 + 2 + 3 + 4 (US1 + US2)**: đủ luồng khiếu nại→duyệt→hoàn điểm để demo giá trị cốt lõi.
- Giao hàng tăng dần; mỗi story không phá vỡ story trước.
