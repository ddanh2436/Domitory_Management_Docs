# Data Model: Nhật ký xử lý bảo trì & Từ chối có lý do

## Entity: Maintenance (mở rộng)

Collection `maintenances`. Các trường **in đậm** là bổ sung mới; còn lại giữ nguyên.

| Trường | Kiểu | Bắt buộc | Ghi chú |
|--------|------|----------|---------|
| user | ObjectId → User | ✅ | Sinh viên tạo yêu cầu |
| room | ObjectId → Room | ✅ | Phòng phát sinh sự cố |
| title | string | ✅ | Tiêu đề sự cố |
| description | string | ✅ | Mô tả chi tiết |
| imageUrl | string | – | Ảnh hiện trường (Cloudinary) |
| priority | enum MaintenancePriority | ✅ | LOW/MEDIUM/HIGH/URGENT |
| status | enum MaintenanceStatus | ✅ | PENDING/IN_PROGRESS/RESOLVED/REJECTED |
| assignedTo | ObjectId → User | – | Nhân viên bảo trì được giao |
| resolvedAt | Date | – | Thời điểm chuyển RESOLVED |
| rating | number (1–5) | – | Sinh viên chấm sao |
| ratedAt | Date | – | Thời điểm chấm sao |
| **rejectionReason** | **string (≤500)** | – | **Lý do từ chối; bắt buộc khi status = REJECTED** |
| **resolutionNote** | **string (≤500)** | – | **Nội dung đã xử lý; nhập tùy chọn khi RESOLVED** |
| **statusHistory** | **StatusHistoryEntry[]** | – | **Nhật ký các lần đổi trạng thái (mặc định [])** |

## Sub-entity: StatusHistoryEntry (embedded)

Mỗi phần tử của `statusHistory`:

| Trường | Kiểu | Bắt buộc | Ghi chú |
|--------|------|----------|---------|
| status | enum MaintenanceStatus | ✅ | Trạng thái đích của lần đổi |
| note | string | – | Ghi chú (lý do từ chối / nội dung xử lý) |
| changedBy | ObjectId → User | – | Người thực hiện |
| changedByName | string | – | Họ tên người thực hiện (denormalized) |
| changedByRole | string | – | Vai trò người thực hiện (ADMIN, MAINTENANCE_STAFF…) |
| at | Date | ✅ | Thời điểm đổi (mặc định now) |

## Ràng buộc & bất biến

- Khi `status` chuyển sang `REJECTED`: `rejectionReason` phải khác rỗng (sau khi trim).
- Khi `status` chuyển sang `RESOLVED`: `resolutionNote` tùy chọn; nếu có thì lưu và
  đồng thời set `resolvedAt` (giữ hành vi cũ).
- Mỗi lần `updateStatus` thành công đẩy đúng **một** phần tử vào `statusHistory`.
- Nhân viên `MAINTENANCE_STAFF` chỉ đổi trạng thái yêu cầu có `assignedTo === chính mình`.
- `rejectionReason`, `resolutionNote` ≤ 500 ký tự.

## Chuyển trạng thái hợp lệ (giữ nguyên + làm rõ)

```
PENDING ──▶ IN_PROGRESS ──▶ RESOLVED
   │                          
   └────────▶ REJECTED (kèm lý do)
IN_PROGRESS ─▶ REJECTED (kèm lý do)
```
