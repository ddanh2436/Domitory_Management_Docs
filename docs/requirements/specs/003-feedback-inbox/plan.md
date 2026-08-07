# Implementation Plan: Hòm thư góp ý & khiếu nại

**Branch**: `002-feedback-inbox` | **Date**: 2026-08-07 | **Spec**: [spec.md](./spec.md)

**Input**: Feature specification from `specs/002-feedback-inbox/spec.md`

## Summary

Tạo mới hoàn toàn module `feedback` ở backend (NestJS) theo đúng mô hình Module → Controller
→ Service đã dùng cho `violations`/`absences`: sinh viên `POST /api/feedback`, tự xem
`GET /api/feedback/me`; ban quản lý xem tất cả có lọc `GET /api/feedback`, phản hồi
`PATCH /api/feedback/:id/respond`. Frontend thêm 2 trang mới: `student/feedback` (form gửi +
lịch sử, theo mẫu `student/absences`) và `admin/feedback` (danh sách + tab lọc + modal phản
hồi, theo mẫu `admin/violations`). Không đổi bất kỳ module/collection nào đã có.

## Technical Context

**Language/Version**: TypeScript (strict) — Node.js / NestJS 11 backend, Next.js 16 (App
Router) / React 19 frontend.

**Primary Dependencies**: NestJS, Mongoose (MongoDB), class-validator, Socket.IO
(`NotificationsGateway` qua `NotificationsService.createAndSend`), Next.js.

**Storage**: MongoDB qua Mongoose — collection mới `feedbacks`.

**Testing**: Jest có sẵn cho backend (`*.spec.ts`); theo yêu cầu đề bài, **không bắt buộc**
viết/chạy test ở PA này. Kiểm thử thủ công theo `quickstart.md`.

**Target Platform**: Web (backend REST API cổng 3001 + WebSocket; frontend Next.js dev
server cổng 3000).

**Project Type**: Web application — 2 repo tách rời (`domitory_management_backend`,
`domitory_management_frontend`), dùng chung MongoDB Atlas.

**Performance Goals**: Thao tác gửi/phản hồi < 300ms p95 ở mức đồ án; không ảnh hưởng các
module hiện có (module hoàn toàn mới, không sửa file cũ ngoài `app.module.ts` và 2 file
layout để thêm điều hướng).

**Constraints**: Không phá vỡ tính năng nào đã hoàn thành `[✅]`. Không hardcode API URL
(dùng `apiClient`/`NEXT_PUBLIC_API_URL`). Không dùng dữ liệu giả.

**Scale/Scope**: Vài trăm góp ý/khiếu nại, 2 vai trò tham gia trực tiếp (STUDENT gửi;
ADMIN/DORMITORY_MANAGER xử lý), 2 màn hình frontend mới.

## Constitution Check

*GATE: Must pass before Phase 0 research. Re-check after Phase 1 design.*

- **Backend Module → Controller → Service + DI**: ✅ Module mới độc lập, không viết logic DB
  vào controller (theo đúng mẫu `violations`).
- **Mongoose, Schema rõ ràng, có Type cho Document**: ✅ `Feedback`/`FeedbackDocument`.
- **JWT Auth + Roles trên mọi endpoint**: ✅ `JwtAuthGuard` + `RolesGuard` + `@Roles(...)` cho
  từng route (STUDENT cho gửi/xem của mình; ADMIN, DORMITORY_MANAGER cho xem tất cả/phản hồi).
- **Realtime qua NotificationsModule/NotificationsGateway**: ✅ Tái sử dụng
  `NotificationsService.createAndSend`, không tạo cơ chế realtime riêng.
- **Không hardcode API URL ở Frontend**: ✅ Dùng `apiClient` (đã tự chuẩn hoá
  `NEXT_PUBLIC_API_URL`).
- **RoleGuard bọc component ở Frontend**: ✅ 2 trang mới nằm trong `app/student/*` và
  `app/admin/*`, đã được `RoleGuard` bọc sẵn ở layout cha (`student/layout.tsx`,
  `admin/layout.tsx`) — không cần bọc lại.
- **TypeScript strict, không dùng `any` trừ bất khả kháng**: ✅.
- ⚠️ **Styling**: Constitution §2 yêu cầu "chỉ Tailwind, không style nội tuyến". Toàn bộ các
  trang tương đương hiện có (`student/absences`, `admin/violations`, v.v.) đều dùng khối
  `<style>` nội tuyến theo design system riêng (Fraunces/DM Sans, biến `--navy/--gold`).
  Quyết định: **tuân theo quy ước thực tế của codebase** (giống quyết định đã ghi nhận ở
  `specs/001-maintenance-resolution-log/plan.md`) để 2 trang mới đồng nhất giao diện với phần
  còn lại của hệ thống — xem Complexity Tracking.

## Project Structure

### Documentation (this feature)

```text
specs/002-feedback-inbox/
├── plan.md              # This file
├── data-model.md        # Schema & entity
├── quickstart.md        # Kịch bản chạy thử / demo
├── contracts/
│   └── feedback-api.md  # Hợp đồng endpoint
└── tasks.md             # (Phase 2 — /speckit-tasks)
```

### Source Code (repository root — 2 repo tách rời, không có thư mục `src/` bọc ngoài)

```text
domitory_management_backend/src/feedback/
├── feedback.enum.ts                    # (mới) FeedbackType, FeedbackCategory, FeedbackStatus
├── schemas/feedback.schema.ts          # (mới) Mongoose schema
├── dto/create-feedback.dto.ts          # (mới) validate type/category/message
├── dto/respond-feedback.dto.ts         # (mới) validate response/status
├── feedback.controller.ts              # (mới) 4 route: POST /, GET /me, GET /, PATCH /:id/respond
├── feedback.module.ts                  # (mới) import MongooseModule + NotificationsModule
└── feedback.service.ts                 # (mới) logic nghiệp vụ

domitory_management_backend/src/
└── app.module.ts                       # (sửa) import FeedbackModule

domitory_management_frontend/app/
├── student/feedback/page.tsx           # (mới) form gửi + lịch sử của tôi
├── admin/feedback/page.tsx             # (mới) danh sách + tab lọc + modal phản hồi
├── student/layout.tsx                  # (sửa) thêm NavItem "Góp ý & Khiếu nại"
└── admin/layout.tsx                    # (sửa) thêm NavItem "Góp ý & Khiếu nại"
```

**Structure Decision**: Web application 2 repo tách rời, không có `src/` bọc ngoài (khác với
`specs/001-maintenance-resolution-log/plan.md`, vốn ghi nhầm `src/backend`/`src/frontend` —
đường dẫn thật đã được xác nhận lại bằng cách đọc trực tiếp cấu trúc thư mục hiện tại trước
khi viết plan này). Tính năng hoàn toàn mới, không sửa module cũ nào ngoài 3 file đăng ký
(module + 2 layout điều hướng).

## Complexity Tracking

| Violation | Why Needed | Simpler Alternative Rejected Because |
|-----------|------------|-------------------------------------|
| Dùng khối `<style>` nội tuyến thay vì Tailwind thuần như constitution yêu cầu | Toàn bộ trang student/admin hiện tại dùng chung một design system CSS nội tuyến (Fraunces/DM Sans, navy/gold); trộn Tailwind thuần sẽ lệch giao diện 2 trang mới so với phần còn lại | Viết lại toàn bộ theo Tailwind đúng constitution vượt xa phạm vi tính năng và không nhất quán với 20+ trang hiện có |
