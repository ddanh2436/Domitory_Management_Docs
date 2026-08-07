# API Contract: Feedback (Góp ý & Khiếu nại)

Base: `/api/feedback` — mọi route bảo vệ bởi `JwtAuthGuard` + `RolesGuard`.

## POST `/api/feedback`

Sinh viên gửi một góp ý/khiếu nại mới.

**Roles**: `STUDENT`

**Request body**:

```jsonc
{
  "type": "COMPLAINT",          // bắt buộc; "COMPLAINT" | "SUGGESTION"
  "category": "STAFF_CONDUCT",  // tùy chọn; mặc định "OTHER" nếu bỏ trống
  "message": "Nhân viên bảo vệ nói chuyện thiếu tôn trọng lúc 22h..." // bắt buộc, ≤1000 ký tự
}
```

**Quy tắc validate**:

- `type` không thuộc `COMPLAINT`/`SUGGESTION` → `400 Bad Request`.
- `category` không thuộc 4 giá trị hợp lệ (nếu có gửi) → `400 Bad Request`.
- `message` rỗng/khoảng trắng → `400 Bad Request` "Vui lòng nhập nội dung".
- `message` > 1000 ký tự → `400 Bad Request`.

**Response 201**:

```jsonc
{
  "message": "Đã gửi góp ý/khiếu nại, ban quản lý sẽ phản hồi sớm nhất.",
  "feedback": { "_id": "...", "type": "COMPLAINT", "category": "STAFF_CONDUCT", "message": "...", "status": "PENDING", "createdAt": "..." }
}
```

**Side effects**: Gửi thông báo realtime (`NotificationsService.createAndSend`) cho mọi tài
khoản `ADMIN` và `DORMITORY_MANAGER`, `link: "/admin/feedback"`.

---

## GET `/api/feedback/me`

Sinh viên xem danh sách góp ý/khiếu nại của chính mình.

**Roles**: `STUDENT`

**Response 200**: mảng `Feedback`, sắp xếp `createdAt` giảm dần, không phân trang (quy mô đồ
án).

---

## GET `/api/feedback`

Ban quản lý xem toàn bộ góp ý/khiếu nại, có lọc.

**Roles**: `ADMIN`, `DORMITORY_MANAGER`

**Query params** (tất cả tùy chọn):

- `type`: `COMPLAINT` | `SUGGESTION`
- `status`: `PENDING` | `RESOLVED` | `CLOSED`

**Quy tắc validate**: giá trị `type`/`status` không hợp lệ → `400 Bad Request`.

**Response 200**: mảng `Feedback`, `populate('student', 'fullName mssv email')`, sắp xếp
`createdAt` giảm dần.

---

## PATCH `/api/feedback/:id/respond`

Ban quản lý phản hồi và khép lại một góp ý/khiếu nại đang chờ xử lý.

**Roles**: `ADMIN`, `DORMITORY_MANAGER`

**Request body**:

```jsonc
{
  "response": "Ban quản lý đã nhắc nhở nhân viên bảo vệ liên quan, cảm ơn phản ánh của bạn.", // bắt buộc, ≤1000 ký tự
  "status": "RESOLVED" // bắt buộc; "RESOLVED" | "CLOSED"
}
```

**Quy tắc validate**:

- `id` không phải ObjectId hợp lệ → `400 Bad Request`.
- Không tìm thấy góp ý/khiếu nại → `404 Not Found`.
- `status` hiện tại của bản ghi khác `PENDING` → `400 Bad Request` "Mục này đã được xử lý
  trước đó".
- `response` rỗng/khoảng trắng → `400 Bad Request` "Vui lòng nhập nội dung phản hồi".
- `response` > 1000 ký tự → `400 Bad Request`.
- `status` gửi lên không thuộc `RESOLVED`/`CLOSED` → `400 Bad Request`.

**Response 200**:

```jsonc
{
  "message": "Đã phản hồi và cập nhật trạng thái",
  "feedback": { "_id": "...", "status": "RESOLVED", "response": "...", "respondedBy": "...", "respondedAt": "..." }
}
```

**Side effects**: Gửi thông báo realtime cho sinh viên đã gửi mục này (`recipient:
feedback.student`), kèm nội dung phản hồi, `link: "/student/feedback"`.
