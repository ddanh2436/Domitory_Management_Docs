# API Contract: Maintenance status update (mở rộng)

Base: `/api/maintenance` — bảo vệ bởi `JwtAuthGuard` + `RolesGuard`.

## PATCH `/api/maintenance/:id/status` (SỬA ĐỔI)

Cập nhật trạng thái một yêu cầu bảo trì, hỗ trợ kèm ghi chú/lý do và ghi nhật ký.

**Roles**: `ADMIN`, `DORMITORY_MANAGER`, `MAINTENANCE_STAFF`
(nhân viên chỉ cập nhật được yêu cầu được giao cho mình).

**Request body**:

```jsonc
{
  "status": "REJECTED",          // bắt buộc; ∈ PENDING|IN_PROGRESS|RESOLVED|REJECTED
  "rejectionReason": "Báo sai, thiết bị vẫn hoạt động", // bắt buộc khi status=REJECTED, ≤500
  "note": "Đã thay bóng đèn LED 9W"  // tùy chọn; nội dung xử lý khi status=RESOLVED, ≤500
}
```

**Quy tắc validate**:

- `status` không hợp lệ → `400 Bad Request` "Trạng thái yêu cầu bảo trì không hợp lệ".
- `status = REJECTED` mà `rejectionReason` rỗng/khoảng trắng → `400 Bad Request`
  "Vui lòng nhập lý do từ chối".
- `rejectionReason`/`note` > 500 ký tự → `400 Bad Request`.
- Nhân viên cập nhật yêu cầu không thuộc mình → `403 Forbidden`.
- Không tìm thấy yêu cầu → `404 Not Found`.

**Response 200**:

```jsonc
{
  "message": "Cập nhật tiến độ thành công",
  "request": { /* Maintenance đã cập nhật, gồm rejectionReason/resolutionNote/statusHistory */ }
}
```

**Side effects**:

- Đẩy một phần tử vào `statusHistory` (status, note, changedBy*, at).
- `REJECTED`: lưu `rejectionReason`; gửi thông báo cho sinh viên kèm lý do.
- `RESOLVED` (lần đầu): lưu `resolutionNote` (nếu có), set `resolvedAt`; gửi thông báo mời đánh giá (hành vi cũ).

## GET `/api/maintenance/me` (KHÔNG ĐỔI HỢP ĐỒNG)

Trả danh sách yêu cầu của sinh viên; nay mỗi phần tử **thêm** `rejectionReason`,
`resolutionNote`, `statusHistory` (tương thích ngược — chỉ thêm field).

## GET `/api/maintenance/assigned/me` và GET `/api/maintenance` (KHÔNG ĐỔI HỢP ĐỒNG)

Tương tự — payload thêm 3 field mới, client cũ bỏ qua được.
