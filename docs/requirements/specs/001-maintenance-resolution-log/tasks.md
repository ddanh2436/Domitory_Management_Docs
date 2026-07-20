---
description: "Task list for Nhật ký xử lý bảo trì & Từ chối có lý do"
---

# Tasks: Nhật ký xử lý bảo trì & Từ chối có lý do

**Input**: Design documents from `specs/001-maintenance-resolution-log/`

**Prerequisites**: plan.md, spec.md, data-model.md, contracts/maintenance-api.md, research.md

**Tests**: Không bắt buộc ở PA này (theo yêu cầu). Có 1 task test tùy chọn cho service.

**Organization**: Nhóm theo user story để triển khai/kiểm thử độc lập.

## Format: `[ID] [P?] [Story] Description`

- **[P]**: Có thể chạy song song (khác file, không phụ thuộc)
- **[Story]**: US1 / US2 / US3

## Path Conventions

- Backend: `src/backend/src/maintenance/`
- Frontend: `src/frontend/app/`

---

## Phase 1: Setup (Shared Infrastructure)

- [ ] T001 Xác nhận môi trường chạy được (backend `npm run start:dev`, frontend `npm run dev`) và có 3 tài khoản STUDENT / MAINTENANCE_STAFF / ADMIN + 1 yêu cầu bảo trì mẫu (xem quickstart.md).

---

## Phase 2: Foundational (Blocking Prerequisites)

**⚠️ CRITICAL**: Hoàn tất trước khi làm bất kỳ user story nào — mọi story đều đọc/ghi các trường DB mới.

- [x] T002 [Foundation] Mở rộng `src/backend/src/maintenance/schemas/maintenance.schema.ts`: thêm `rejectionReason?`, `resolutionNote?`, và mảng embedded `statusHistory` (class `StatusHistoryEntry` với status, note, changedBy, changedByName, changedByRole, at).
- [x] T003 [Foundation] Tạo `src/backend/src/maintenance/dto/update-status.dto.ts`: validate `status` (IsEnum), `rejectionReason?`/`note?` (IsString, MaxLength 500).

**Checkpoint**: Schema + DTO sẵn sàng — các story có thể triển khai.

---

## Phase 3: User Story 1 — Từ chối yêu cầu kèm lý do (Priority: P1) 🎯 MVP

**Goal**: Nhân viên/Admin từ chối kèm lý do bắt buộc; sinh viên xem được lý do.

**Independent Test**: STAFF từ chối để trống lý do → bị chặn; nhập lý do → REJECTED + student thấy lý do.

- [x] T004 [US1] `maintenance.service.ts` → `updateStatus`: nhận thêm `note`/`rejectionReason`; nếu `status=REJECTED` mà thiếu `rejectionReason` (trim rỗng) → `BadRequestException`; lưu `rejectionReason`; đẩy 1 phần tử vào `statusHistory`; gửi thông báo cho sinh viên kèm lý do.
- [x] T005 [US1] `maintenance.controller.ts` → `PATCH :id/status`: dùng `UpdateStatusDto`, truyền `note`/`rejectionReason` xuống service; lấy tên người thực hiện để ghi nhật ký.
- [x] T006 [P] [US1] Frontend `staff/page.tsx`: thêm nút **Từ chối** (PENDING/IN_PROGRESS), mở modal nhập lý do bắt buộc, gọi PATCH status=REJECTED kèm `rejectionReason`.
- [x] T007 [P] [US1] Frontend `student/maintenance/page.tsx`: thêm field `rejectionReason` vào interface và hiển thị hộp "Lý do từ chối" khi status=REJECTED.
- [x] T008 [P] [US1] Frontend `admin/maintenance/page.tsx`: khi bấm Từ chối, hiển thị ô nhập lý do trong ConfirmModal (bắt buộc) và gửi kèm `rejectionReason`; hiển thị lý do trên thẻ.

**Checkpoint**: US1 chạy end-to-end độc lập.

---

## Phase 4: User Story 2 — Ghi nội dung đã xử lý khi hoàn thành (Priority: P2)

**Goal**: Nhân viên ghi nội dung xử lý khi RESOLVED; sinh viên/admin xem được.

**Independent Test**: STAFF hoàn thành + nhập nội dung → student thấy nội dung xử lý.

- [x] T009 [US2] `maintenance.service.ts`: khi `status=RESOLVED`, lưu `resolutionNote` (nếu có) cùng lúc set `resolvedAt`; ghi vào `statusHistory` với note tương ứng.
- [x] T010 [P] [US2] Frontend `staff/page.tsx`: khi bấm **Hoàn thành**, mở modal nhập nội dung đã xử lý (tùy chọn) và gửi kèm `note`.
- [x] T011 [P] [US2] Frontend `student/maintenance/page.tsx`: hiển thị `resolutionNote` khi status=RESOLVED (cạnh ô đánh giá sao).
- [x] T012 [P] [US2] Frontend `admin/maintenance/page.tsx`: hiển thị `resolutionNote` trên thẻ yêu cầu đã hoàn thành.

**Checkpoint**: US1 + US2 chạy độc lập.

---

## Phase 5: User Story 3 — Nhật ký các lần đổi trạng thái (Priority: P3)

**Goal**: Hiển thị dòng thời gian statusHistory cho student/admin.

**Independent Test**: Yêu cầu qua vài mốc → nhật ký liệt kê đúng thứ tự + ghi chú.

- [x] T013 [P] [US3] Frontend `student/maintenance/page.tsx`: render `statusHistory` dạng dòng thời gian (trạng thái, thời gian, người thực hiện, ghi chú).
- [x] T014 [P] [US3] Frontend `staff/page.tsx`: hiển thị nhật ký gọn trên thẻ việc để nhân viên tự đối chiếu.

**Checkpoint**: Cả 3 story hoạt động độc lập.

---

## Phase 6: Polish & Cross-Cutting

- [ ] T015 [P] (Tùy chọn) Cập nhật/bổ sung `maintenance.service.spec.ts`: case REJECTED thiếu lý do ném lỗi; case ghi statusHistory.
- [x] T016 Build kiểm tra: `cd src/backend && npm run build`; `cd src/frontend && npm run build`. Sửa lỗi type nếu có.
- [ ] T017 Chạy quickstart.md 3 kịch bản trước khi quay video demo.

---

## Dependencies & Execution Order

- **Setup (T001)** → **Foundational (T002–T003, BLOCKS all)** → **US1 (T004–T008)** → US2 (T009–T012) → US3 (T013–T014) → **Polish (T015–T017)**.
- Backend service/controller (T004–T005, T009) là điều kiện tiên quyết cho các task frontend cùng story.
- Các task `[P]` khác file có thể làm song song trong cùng story.

## Implementation Strategy

- **MVP = Phase 1 + 2 + 3 (US1)**: đủ để demo giá trị cốt lõi (từ chối có lý do). Dừng và kiểm thử trước khi sang US2/US3.
- Giao hàng tăng dần theo từng story; mỗi story không phá vỡ story trước.
