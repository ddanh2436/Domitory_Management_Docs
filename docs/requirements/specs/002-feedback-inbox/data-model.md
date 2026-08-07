# Data Model: Hòm thư góp ý & khiếu nại

## Entity: Feedback (mới)

Collection `feedbacks`.

| Trường | Kiểu | Bắt buộc | Ghi chú |
|--------|------|----------|---------|
| student | ObjectId → User | ✅ | Sinh viên gửi (không hỗ trợ ẩn danh) |
| type | enum FeedbackType | ✅ | `COMPLAINT` \| `SUGGESTION` |
| category | enum FeedbackCategory | ✅ | `FACILITY` \| `STAFF_CONDUCT` \| `BILLING` \| `OTHER`; mặc định `OTHER` |
| message | string (≤1000) | ✅ | Nội dung góp ý/khiếu nại |
| status | enum FeedbackStatus | ✅ | `PENDING` (mặc định) → `RESOLVED` \| `CLOSED` |
| response | string (≤1000) | – | Nội dung phản hồi của ban quản lý; bắt buộc khi chuyển khỏi `PENDING` |
| respondedBy | ObjectId → User | – | Người phản hồi (ADMIN/DORMITORY_MANAGER) |
| respondedAt | Date | – | Thời điểm phản hồi |
| createdAt / updatedAt | Date | ✅ | Tự động (`timestamps: true`) |

## Ràng buộc & bất biến

- `message` phải khác rỗng sau khi trim, tối đa 1000 ký tự.
- Khi tạo mới: `status` luôn khởi tạo `PENDING`; `response`/`respondedBy`/`respondedAt` để
  trống.
- Chỉ được phản hồi (`PATCH /:id/respond`) khi `status === PENDING`. Sau khi phản hồi,
  `status` chuyển sang `RESOLVED` hoặc `CLOSED` — đây là trạng thái cuối, không phản hồi lại
  được nữa (bất biến: một bản ghi tối đa nhận đúng **một** lần phản hồi).
- Khi phản hồi thành công: `response`, `respondedBy`, `respondedAt` MUST đều được set cùng
  lúc (không tồn tại bản ghi `RESOLVED`/`CLOSED` thiếu một trong ba trường này — SC-004).
- `response` bắt buộc khác rỗng khi phản hồi (đã chốt: không hỗ trợ "đóng không phản hồi").

## Vòng đời trạng thái

```
PENDING ──(phản hồi + chọn "Đã xử lý")──▶ RESOLVED
   │
   └──(phản hồi + chọn "Đóng")──▶ CLOSED
```

Không có chuyển tiếp nào khác; `RESOLVED` và `CLOSED` đều là trạng thái cuối.

## Chỉ mục (indexes)

- `{ student: 1, createdAt: -1 }` — phục vụ `GET /api/feedback/me` (danh sách của một sinh
  viên, mới nhất trước).
- `{ status: 1, type: 1, createdAt: -1 }` — phục vụ `GET /api/feedback` có lọc theo trạng
  thái/loại cho ban quản lý.
