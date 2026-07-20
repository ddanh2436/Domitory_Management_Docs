# Implementation Plan: Nhật ký xử lý bảo trì & Từ chối có lý do

**Branch**: `001-maintenance-resolution-log` | **Date**: 2026-07-20 | **Spec**: [spec.md](./spec.md)

**Input**: Feature specification from `specs/001-maintenance-resolution-log/spec.md`

## Summary

Bổ sung vào module `maintenance` hiện có ba khả năng: (1) nhân viên bảo trì từ chối yêu
cầu kèm **lý do bắt buộc**; (2) ghi **nội dung đã xử lý** khi hoàn thành; (3) lưu **nhật ký
đổi trạng thái**. Cách tiếp cận: mở rộng schema Mongoose `Maintenance` với 3 trường mới,
mở rộng endpoint `PATCH /api/maintenance/:id/status` để nhận `note`/`rejectionReason` và
validate, rồi cập nhật 3 màn hình frontend (staff, student, admin) để nhập và hiển thị.

## Technical Context

**Language/Version**: TypeScript (strict) — Node.js / NestJS 11 backend, Next.js 16 (App Router) frontend.

**Primary Dependencies**: NestJS, Mongoose (MongoDB), Socket.IO (NotificationsGateway), Next.js React 19.

**Storage**: MongoDB qua Mongoose — collection `maintenances`.

**Testing**: Jest (backend, `*.spec.ts` colocated). Frontend không có test runner; kiểm thử thủ công theo quickstart.

**Target Platform**: Web (backend REST API cổng 3001 + WebSocket; frontend Next.js dev server).

**Project Type**: Web application (backend + frontend tách repo).

**Performance Goals**: Thao tác cập nhật trạng thái < 300ms p95 ở mức đồ án; không ảnh hưởng danh sách hiện có.

**Constraints**: Không phá vỡ luồng hiện tại (assign, rate, notify). Không hồi tố dữ liệu cũ. Giữ nguyên hợp đồng API cũ (thêm field tùy chọn, không xóa field).

**Scale/Scope**: Vài trăm yêu cầu bảo trì, ~4 vai trò người dùng, 3 màn hình frontend.

## Constitution Check

*GATE: Must pass before Phase 0 research. Re-check after Phase 1 design.*

- **Backend Module → Controller → Service + DI**: ✅ Chỉ mở rộng `MaintenanceController`/`MaintenanceService` sẵn có, giữ đúng mô hình.
- **Mongoose (MongoDB)**: ✅ Thêm trường vào `Maintenance` schema.
- **JWT Auth + Roles**: ✅ Tái dùng `JwtAuthGuard` + `RolesGuard`; giữ ràng buộc nhân viên chỉ xử lý việc của mình.
- **Không mock data, fetch qua API**: ✅ Frontend dùng `apiClient` gọi backend thật.
- **TypeScript strict, tên hàm/biến camelCase tiếng Anh**: ✅.
- **Error handling rõ ràng cho UI**: ✅ Backend ném `BadRequestException`/`ForbiddenException` với thông báo tiếng Việt; frontend hiển thị toast.
- ⚠️ **Styling**: Constitution nêu "bắt buộc Tailwind, không CSS rời". Codebase thực tế dùng khối `<style>` nội tuyến theo design system riêng (Fraunces/DM Sans, biến CSS `--navy/--gold`). Quyết định: **tuân theo quy ước thực tế của codebase** để tính năng đồng nhất giao diện — xem Complexity Tracking.

## Project Structure

### Documentation (this feature)

```text
specs/001-maintenance-resolution-log/
├── plan.md              # This file
├── research.md          # Quyết định kỹ thuật
├── data-model.md        # Schema & entity
├── quickstart.md        # Kịch bản chạy thử / demo
├── contracts/
│   └── maintenance-api.md   # Hợp đồng endpoint
└── tasks.md             # (tạo ở bước /speckit-tasks)
```

### Source Code (repository root)

```text
src/backend/src/maintenance/
├── schemas/maintenance.schema.ts     # + rejectionReason, resolutionNote, statusHistory
├── dto/update-status.dto.ts          # (mới) validate status + note + rejectionReason
├── maintenance.controller.ts         # PATCH :id/status nhận note/rejectionReason
├── maintenance.service.ts            # updateStatus: validate, lưu, ghi nhật ký, notify
└── maintenance.enum.ts               # (giữ nguyên MaintenanceStatus)

src/frontend/app/
├── staff/page.tsx                    # Modal Từ chối (lý do) + Hoàn thành (ghi chú) + nhật ký
├── student/maintenance/page.tsx      # Hiển thị lý do từ chối / nội dung xử lý / nhật ký
└── admin/maintenance/page.tsx        # Reject kèm lý do bắt buộc + hiển thị
```

**Structure Decision**: Web application 2 repo tách rời (`src/backend`, `src/frontend`).
Tính năng bám sát module `maintenance` sẵn có; không tạo module mới. Frontend chạm 3 file
màn hình theo vai trò.

## Complexity Tracking

| Violation | Why Needed | Simpler Alternative Rejected Because |
|-----------|------------|-------------------------------------|
| Dùng khối `<style>` nội tuyến thay vì Tailwind như constitution yêu cầu | Toàn bộ codebase hiện tại (staff/student/admin) dùng design system CSS nội tuyến; trộn Tailwind sẽ lệch giao diện và khó bảo trì | Viết lại toàn bộ sang Tailwind vượt xa phạm vi một cải tiến và gây rủi ro hồi quy giao diện |
| Lưu `changedByName`/`changedByRole` trực tiếp trong nhật ký thay vì chỉ `ObjectId` + populate | Tránh populate lồng nhau khi hiển thị nhật ký; đơn giản và đủ cho mức đồ án | Chỉ lưu ObjectId buộc mọi màn hình phải populate thêm, tăng độ phức tạp truy vấn |
