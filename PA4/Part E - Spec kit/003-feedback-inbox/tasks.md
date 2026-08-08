---
description: "Task list for Hòm thư góp ý & khiếu nại"
---

# Tasks: Hòm thư góp ý & khiếu nại

**Input**: Design documents from `specs/002-feedback-inbox/`

**Prerequisites**: plan.md, spec.md, data-model.md, contracts/feedback-api.md

**Tests**: Không bắt buộc ở PA này (theo yêu cầu đề bài). Spec Kit cho phép tạo test case
nhưng không cần kiểm tra/tinh chỉnh — không có task test trong danh sách này.

**Organization**: Nhóm theo user story để triển khai/kiểm thử độc lập.

## Format: `[ID] [P?] [Story] Description`

- **[P]**: Có thể chạy song song (khác file, không phụ thuộc)
- **[Story]**: US1 / US2

## Path Conventions

- Backend: `domitory_management_backend/src/feedback/`
- Frontend: `domitory_management_frontend/app/`

---

## Phase 1: Setup (Shared Infrastructure)

- [x] T001 Xác nhận cấu trúc thư mục thật (không có `src/` bọc ngoài 2 repo) trước khi viết
      plan — đã xác nhận bằng cách đọc trực tiếp `domitory_management_backend/src/*` và
      `domitory_management_frontend/app/*`.

---

## Phase 2: Foundational (Blocking Prerequisites)

**⚠️ CRITICAL**: Hoàn tất trước khi làm bất kỳ user story nào.

- [x] T002 [Foundation] Tạo `domitory_management_backend/src/feedback/feedback.enum.ts`:
      `FeedbackType` (COMPLAINT, SUGGESTION), `FeedbackCategory` (FACILITY, STAFF_CONDUCT,
      BILLING, OTHER), `FeedbackStatus` (PENDING, RESOLVED, CLOSED).
- [x] T003 [Foundation] Tạo `domitory_management_backend/src/feedback/schemas/feedback.schema.ts`
      theo `data-model.md` (student, type, category, message, status, response, respondedBy,
      respondedAt) + 2 index composite.
- [x] T004 [Foundation] Tạo `domitory_management_backend/src/feedback/feedback.module.ts`:
      import `MongooseModule.forFeature([Feedback])` + `NotificationsModule`; đăng ký
      `FeedbackController`/`FeedbackService`.
- [x] T005 [Foundation] Đăng ký `FeedbackModule` vào
      `domitory_management_backend/src/app.module.ts`.

**Checkpoint**: Schema + module khung sẵn sàng — các story có thể triển khai.

---

## Phase 3: User Story 1 — Gửi và theo dõi góp ý/khiếu nại (Priority: P1) 🎯 MVP

**Goal**: Sinh viên gửi được góp ý/khiếu nại và xem lại danh sách + trạng thái của mình.

**Independent Test**: STUDENT gửi 1 góp ý → thấy ngay trong danh sách "Lịch sử của tôi" với
trạng thái "Chờ xử lý".

- [x] T006 [US1] `domitory_management_backend/src/feedback/dto/create-feedback.dto.ts`:
      validate `type` (IsIn), `category` (IsIn, optional, default OTHER), `message`
      (IsNotEmpty, MaxLength 1000).
- [x] T007 [US1] `feedback.service.ts` → `create(studentId, dto)`: tạo bản ghi `PENDING`;
      gửi thông báo cho toàn bộ ADMIN/DORMITORY_MANAGER (`link: /admin/feedback`).
- [x] T007b [US1] `feedback.service.ts` → `findMine(studentId)`: trả danh sách của sinh viên,
      sort `createdAt` giảm dần.
- [x] T008 [US1] `feedback.controller.ts`: `POST /` (Roles STUDENT) và `GET /me` (Roles
      STUDENT), dùng `CreateFeedbackDto`.
- [x] T009 [P] [US1] Frontend `domitory_management_frontend/app/student/feedback/page.tsx`:
      form gửi (loại + danh mục + nội dung, theo mẫu `student/absences/page.tsx`) + bảng lịch
      sử của tôi (loại, danh mục, nội dung, trạng thái, phản hồi nếu có).

**Checkpoint**: US1 chạy end-to-end độc lập (chưa cần màn hình quản lý).

---

## Phase 4: User Story 2 — Ban quản lý xem, lọc và phản hồi (Priority: P2)

**Goal**: Ban quản lý xem toàn bộ, lọc theo loại/trạng thái, phản hồi và khép lại từng mục.

**Independent Test**: ADMIN mở danh sách, lọc "Chờ xử lý", phản hồi 1 mục → mục chuyển
"Đã xử lý"/"Đã đóng", sinh viên liên quan nhận thông báo realtime.

- [x] T010 [US2] `domitory_management_backend/src/feedback/dto/respond-feedback.dto.ts`:
      validate `response` (IsNotEmpty, MaxLength 1000), `status` (IsIn RESOLVED/CLOSED).
- [x] T011 [US2] `feedback.service.ts` → `findAll(filter)`: lọc `type`/`status` nếu có,
      `populate('student', 'fullName mssv email')`, sort mới nhất trước.
- [x] T012 [US2] `feedback.service.ts` → `respond(reviewerId, id, dto)`: chặn nếu không tồn
      tại hoặc `status !== PENDING`; set `response`/`respondedBy`/`respondedAt`/`status`; gửi
      thông báo cho sinh viên (`link: /student/feedback`).
- [x] T013 [US2] `feedback.controller.ts`: `GET /` (Roles ADMIN, DORMITORY_MANAGER, query
      `type`/`status`) và `PATCH /:id/respond` (Roles ADMIN, DORMITORY_MANAGER), dùng
      `RespondFeedbackDto`.
- [x] T014 [P] [US2] Frontend `domitory_management_frontend/app/admin/feedback/page.tsx`:
      danh sách dạng thẻ + tab lọc loại/trạng thái + modal phản hồi (theo mẫu
      `admin/violations/page.tsx`).

**Checkpoint**: US1 + US2 chạy độc lập, khép kín vòng phản hồi hai chiều.

---

## Phase 5: Polish & Cross-Cutting

- [x] T015 [P] Thêm `NavItem` "Góp ý & Khiếu nại" vào
      `domitory_management_frontend/app/student/layout.tsx` (link `/student/feedback`) và
      `domitory_management_frontend/app/admin/layout.tsx` (link `/admin/feedback`).
- [x] T016 Build kiểm tra: `cd domitory_management_backend && npm run build` ✅; frontend
      `npx tsc --noEmit` ✅ (0 lỗi type). Đã xác minh thêm bằng smoke test qua API thật
      (seed `scripts/e2e-seed.js` → kịch bản đầy đủ US1+US2 → cleanup
      `scripts/e2e-cleanup.js`, nay có dọn cả collection `feedbacks`): **12/12 PASS**, gồm cả
      2 trường hợp trước đó **lộ lỗi thật** và đã được sửa trong `feedback.service.ts` —
      `create()`/`respond()` chỉ validate `IsNotEmpty` qua DTO, không trim, nên
      message/response chỉ gồm khoảng trắng lọt qua validation rồi làm Mongoose ném lỗi 500
      (thay vì 400) khi `required: true` gặp chuỗi rỗng; `respond()` còn kịp đổi status sang
      RESOLVED trước khi crash. Đã thêm kiểm tra trim thủ công trong service (đúng pattern
      đã dùng ở `violations.service.ts`) trước khi chạm DB.
- [ ] T017 Chạy `quickstart.md` (2 kịch bản) **thủ công qua giao diện trình duyệt** trước khi
      quay video demo — phần backend/API của 2 kịch bản đã được xác minh tự động ở T016, còn
      lại là xác nhận trải nghiệm UI thực tế (form, modal, badge, thông báo chuông).

---

## Dependencies & Execution Order

- **Setup (T001)** → **Foundational (T002–T005, BLOCKS all)** → **US1 (T006–T009)** → US2
  (T010–T014) → **Polish (T015–T017)**.
- Backend service/controller là điều kiện tiên quyết cho task frontend cùng story.
- T009 và T014 khác file, có thể làm song song với nhau nếu US1 backend đã xong.

## Implementation Strategy

- **MVP = Phase 1 + 2 + 3 (US1)**: sinh viên gửi được và tự theo dõi — đủ để chứng minh giá
  trị cốt lõi trước khi làm US2.
- Giao hàng tăng dần: US1 xong trước, US2 khép kín vòng lặp — mỗi story không phá vỡ story
  trước.
