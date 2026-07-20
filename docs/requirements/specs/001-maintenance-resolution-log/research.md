# Research: Nhật ký xử lý bảo trì & Từ chối có lý do

## Quyết định 1 — Nơi lưu nhật ký trạng thái

**Quyết định**: Nhúng mảng `statusHistory` ngay trong document `Maintenance` (embedded
subdocuments), không tách collection riêng.

**Lý do**: Nhật ký luôn được đọc/ghi cùng ngữ cảnh một yêu cầu, số mốc nhỏ (vài phần tử),
truy vấn 1 lần không cần join. Phù hợp mô hình document của MongoDB và mức đồ án.

**Phương án loại bỏ**: Collection `maintenance_status_logs` riêng — thêm join/populate,
phức tạp không cần thiết cho quy mô này.

## Quyết định 2 — Bắt buộc lý do từ chối ở tầng nào

**Quyết định**: Validate ở **backend service** (nguồn sự thật) bằng `BadRequestException`
khi `status = REJECTED` mà `rejectionReason` rỗng/khoảng trắng; frontend validate thêm để
UX tốt (chặn nút, hiển thị lỗi tại chỗ).

**Lý do**: Backend là nơi thực thi phân quyền và toàn vẹn dữ liệu; không được tin frontend.
Validate 2 lớp cho trải nghiệm mượt mà nhưng vẫn an toàn.

**Phương án loại bỏ**: Chỉ validate ở frontend — dễ bị bỏ qua qua gọi API trực tiếp.

## Quyết định 3 — Tương thích ngược API

**Quyết định**: Giữ nguyên `PATCH /api/maintenance/:id/status` với body cũ `{ status }`,
**thêm** trường tùy chọn `note` và `rejectionReason`. Không đổi tên/kiểu field cũ.

**Lý do**: Admin và staff đang gọi endpoint này; thêm field tùy chọn không phá vỡ client
cũ. Chỉ ràng buộc mới: `REJECTED` yêu cầu `rejectionReason` (cả admin lẫn staff đều được
cập nhật để gửi kèm).

**Phương án loại bỏ**: Tạo endpoint mới `/reject` và `/resolve` riêng — nhân đôi logic
phân quyền và thông báo đã có trong `updateStatus`.

## Quyết định 4 — Lưu người thực hiện trong nhật ký

**Quyết định**: Lưu `changedByName` + `changedByRole` (denormalized) tại thời điểm ghi,
kèm `changedBy` (ObjectId tham chiếu User) để truy vết.

**Lý do**: Hiển thị nhật ký không phải populate; vẫn giữ ObjectId cho nhu cầu tra cứu sau.

**Phương án loại bỏ**: Chỉ lưu ObjectId — mọi màn hình phải populate lồng, tăng tải và mã.

## Quyết định 5 — Giới hạn độ dài ghi chú

**Quyết định**: Tối đa 500 ký tự cho `rejectionReason` và `resolutionNote`, kiểm ở DTO
(`@MaxLength`) và ở input frontend (`maxLength`).

**Lý do**: Đủ cho mô tả ngắn gọn, tránh lạm dụng và vỡ layout thẻ.
