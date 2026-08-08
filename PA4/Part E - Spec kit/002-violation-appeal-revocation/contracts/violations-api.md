# API Contract: Violations — Appeal & Revocation

Base: `/api/violations` — bảo vệ bởi `JwtAuthGuard` + `RolesGuard`.

## POST `/api/violations/:id/appeal` (MỚI)

Sinh viên khiếu nại một vi phạm của chính mình.

**Roles**: `STUDENT`

**Body**:
```jsonc
{ "reason": "Em không có mặt ở KTX hôm đó, có minh chứng điểm danh" }  // bắt buộc, ≤500
```

**Quy tắc**:
- Vi phạm không thuộc sinh viên → `403`.
- `status ≠ ACTIVE` → `400` "Chỉ khiếu nại được vi phạm đang hiệu lực".
- `reason` rỗng → `400` "Vui lòng nhập lý do khiếu nại".
- Không tìm thấy → `404`.

**Response 200**: `{ message, violation }` (status = `APPEAL_PENDING`). Side effect: thông báo cho admin.

## PATCH `/api/violations/:id/review` (MỚI)

Ban quản lý duyệt một khiếu nại đang chờ.

**Roles**: `ADMIN`, `DORMITORY_MANAGER`

**Body**:
```jsonc
{ "decision": "ACCEPT", "reviewNote": "Đã kiểm tra, khiếu nại hợp lệ" }  // decision ∈ ACCEPT|REJECT; reviewNote ≤500 tùy chọn
```

**Quy tắc**:
- `status ≠ APPEAL_PENDING` → `400` "Chỉ duyệt được khiếu nại đang chờ".
- `decision` không hợp lệ → `400`.
- Không tìm thấy → `404`.

**Response 200**: `{ message, violation, behaviorScore? }`.
- `ACCEPT` → status `REVOKED`, hoàn điểm (≤100), thông báo sinh viên.
- `REJECT` → status `APPEAL_REJECTED`, lưu `reviewNote`, thông báo sinh viên.

## DELETE `/api/violations/:id` (MỚI — thu hồi trực tiếp)

Ban quản lý thu hồi vi phạm ghi nhầm (không cần khiếu nại).

**Roles**: `ADMIN`, `DORMITORY_MANAGER`

**Quy tắc**:
- `status = REVOKED` → `400` "Vi phạm đã được thu hồi".
- Không tìm thấy → `404`.

**Response 200**: `{ message, violation, behaviorScore }` (status = `REVOKED`, đã hoàn điểm 1 lần).

## GET `/api/violations` (MỚI — danh sách tất cả)

Danh sách vi phạm cho ban quản lý, kèm populate `student` và `markedBy`.

**Roles**: `ADMIN`, `DORMITORY_MANAGER`

**Query (tùy chọn)**: `?status=APPEAL_PENDING` để lọc.

**Response 200**: `Violation[]` (sắp theo `createdAt` giảm dần), mỗi phần tử gồm cả các field trạng thái/khiếu nại mới.

## Các endpoint GIỮ NGUYÊN

- `POST /api/violations` (ADMIN/DORMITORY_MANAGER) — ghi vi phạm + trừ điểm (đã có).
- `GET /api/violations/me` (STUDENT) — vi phạm của mình (payload thêm field trạng thái/khiếu nại).
- `GET /api/violations/student/:id` (ADMIN/DORMITORY_MANAGER/FLOOR_MANAGER) — vi phạm theo sinh viên.
