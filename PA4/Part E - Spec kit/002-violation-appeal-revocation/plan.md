# Implementation Plan: Khiếu nại & Thu hồi vi phạm nề nếp

**Branch**: `002-violation-appeal-revocation` | **Date**: 2026-08-07 | **Spec**: [spec.md](./spec.md)

**Input**: Feature specification from `specs/002-violation-appeal-revocation/spec.md`

## Summary

Mở rộng module `violations` sẵn có để hỗ trợ **khiếu nại** (sinh viên) và **duyệt/thu hồi**
(ban quản lý), kèm luồng **hoàn điểm hành vi** khi vi phạm bị thu hồi. Cách tiếp cận: thêm
`status` + các trường khiếu nại/duyệt vào schema `Violation`; thêm 4 endpoint (appeal,
review, revoke, list-all); cập nhật frontend student (nút khiếu nại trong trang hồ sơ) và
tạo màn hình admin `/admin/violations` để duyệt khiếu nại + thu hồi.

## Technical Context

**Language/Version**: TypeScript strict — NestJS 11 (BE), Next.js 16 App Router (FE).

**Primary Dependencies**: NestJS, Mongoose (MongoDB), class-validator, Socket.IO (Notifications), React 19.

**Storage**: MongoDB — collection `violations` và cập nhật `users.behaviorScore`.

**Testing**: Jest (backend, `violations.service.spec.ts` colocated). Frontend kiểm thử thủ công theo quickstart + script E2E headless.

**Target Platform**: Web (REST API cổng 3001 + WebSocket).

**Project Type**: Web application (backend + frontend tách repo).

**Performance Goals**: Thao tác duyệt/khiếu nại < 300ms p95 ở mức đồ án.

**Constraints**: Tương thích ngược — chỉ **thêm** field/endpoint, không đổi hợp đồng cũ (`POST /violations`, `GET /me`, `GET /student/:id`). Hoàn điểm phải idempotent và chặn trần 100. Không hồi tố dữ liệu cũ.

**Scale/Scope**: Vài trăm vi phạm; 2 vai trò chính (STUDENT, ADMIN/DORMITORY_MANAGER); 1 màn hình admin mới + mở rộng trang hồ sơ sinh viên.

## Constitution Check

- **Module → Controller → Service + DI**: ✅ chỉ mở rộng `ViolationsController`/`ViolationsService`.
- **Mongoose (MongoDB)**: ✅ thêm trường vào `Violation` schema.
- **JWT Auth + Roles**: ✅ dùng `@Roles` — STUDENT cho appeal; ADMIN/DORMITORY_MANAGER cho review/revoke/list.
- **Không mock data, fetch qua API**: ✅ frontend dùng `apiClient`.
- **TS strict, camelCase tiếng Anh; error handling rõ ràng**: ✅ ném `BadRequest`/`Forbidden`/`NotFound` với thông báo tiếng Việt.
- ⚠️ **Styling**: constitution nêu Tailwind; codebase thực tế dùng `<style>` nội tuyến theo design system riêng — tuân theo quy ước codebase (xem Complexity Tracking).

## Project Structure

### Documentation (this feature)

```text
specs/002-violation-appeal-revocation/
├── plan.md
├── research.md
├── data-model.md
├── quickstart.md
├── contracts/
│   └── violations-api.md
└── tasks.md
```

### Source Code (repository root)

```text
src/backend/src/violations/
├── schemas/violation.schema.ts        # + status, appealReason, appealedAt, reviewNote, reviewedBy, reviewedAt
├── violations.enum.ts                 # (mới) VIOLATION_STATUS
├── dto/appeal-violation.dto.ts        # (mới) reason
├── dto/review-appeal.dto.ts           # (mới) decision + reviewNote
├── violations.service.ts              # + appeal, reviewAppeal, revoke, findAll, restoreScore
└── violations.controller.ts           # + POST :id/appeal, PATCH :id/review, DELETE :id, GET /

src/frontend/app/
├── student/profile/page.tsx           # nút Khiếu nại + trạng thái + kết quả duyệt
├── admin/violations/page.tsx          # (mới) hàng đợi khiếu nại + tổng quan + thu hồi
└── admin/layout.tsx                    # thêm NavItem "Vi phạm & khiếu nại"
```

**Structure Decision**: Web app 2 repo. Bám module `violations` sẵn có. Frontend: mở rộng
trang hồ sơ sinh viên (đã hiển thị vi phạm) thay vì tạo trang trùng, và thêm **một** màn
hình admin mới cho việc duyệt khiếu nại (chức năng hoàn toàn mới, không trùng form ghi vi
phạm đang nằm ở `/admin/students`).

## Complexity Tracking

| Violation | Why Needed | Simpler Alternative Rejected Because |
|-----------|------------|-------------------------------------|
| Dùng `<style>` nội tuyến thay vì Tailwind | Toàn bộ FE hiện tại dùng design system nội tuyến; trộn Tailwind gây lệch giao diện | Viết lại toàn bộ sang Tailwind vượt phạm vi cải tiến |
| Thêm trạng thái `APPEAL_REJECTED` riêng thay vì quay về `ACTIVE` | Giữ vết "đã từng khiếu nại và bị từ chối" để hiển thị cho sinh viên | Quay về `ACTIVE` làm mất lịch sử khiếu nại, gây khiếu nại lặp |
