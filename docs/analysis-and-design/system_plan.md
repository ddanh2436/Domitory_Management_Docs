### BẢN KẾ HOẠCH HỆ THỐNG (SYSTEM PLAN)

#### 1. Cấu trúc Cơ sở dữ liệu (MongoDB / Mongoose Schemas)

Dựa vào yêu cầu, chúng ta cần 6 Collections (Bảng) chính:

* **Users** (Tài khoản & Thông tin): `_id`, `email`, `password` (hash), `role` (STUDENT, ADMIN, MAINTENANCE), `fullName`, `phone`, `cccd`, `isTempResident` (Tạm trú).
* **Rooms** (Phòng KTX): `_id`, `roomNumber`, `roomType`, `price`, `capacity` (Số giường tối đa), `currentOccupancy` (Số người đang ở).
* **RentalRequests** (Yêu cầu đặt phòng): `_id`, `studentId` (Ref: Users), `roomType`, `status` (PENDING, APPROVED, REJECTED), `createdAt`.
* **Contracts** (Hợp đồng): `_id`, `studentId` (Ref: Users), `roomId` (Ref: Rooms), `startDate`, `endDate`, `pdfUrl`, `isActive`.
* **Bills** (Hóa đơn): `_id`, `roomId` (Ref: Rooms), `month`, `year`, `electricityIndex` (đầu/cuối), `waterIndex` (đầu/cuối), `totalAmount`, `status` (UNPAID, PAID), `receiptUrl`.
* **MaintenanceTasks** (Sự cố): `_id`, `reporterId` (Ref: Users), `roomId` (Ref: Rooms), `description`, `imageUrl`, `assigneeId` (Ref: Users - Role Maintenance), `status` (PENDING, IN_PROGRESS, RESOLVED).

#### 2. Danh sách API Endpoints (Nest.js Controllers)

**Auth & Users (Bảo mật bằng JWT)**

* `POST /api/auth/login`: Đăng nhập, trả về JWT Token.
* `GET /api/users/profile`: (Auth) Xem thông tin cá nhân.
* `PATCH /api/users/profile`: (Auth) Cập nhật hồ sơ / Tạm trú.

**Rooms (Quản lý Phòng)**

* `GET /api/rooms`: Lấy danh sách phòng trống (Có query filter loại phòng, giá).
* `POST /api/rooms`: (Role: Admin) Thêm phòng mới.

**Rentals & Contracts (Đặt phòng & Hợp đồng)**

* `POST /api/rentals`: (Role: Student) Gửi yêu cầu đặt phòng.
* `GET /api/rentals`: (Role: Admin) Xem danh sách yêu cầu.
* `PATCH /api/rentals/:id/approve`: (Role: Admin) Duyệt yêu cầu -> Tự động giảm số giường trống + Tạo dữ liệu qua bảng Contracts.
* `GET /api/contracts/:id/pdf`: Xuất file PDF hợp đồng.
* `POST /api/contracts/:id/checkout`: (Role: Student) Gửi form trả phòng.

**Bills (Tài chính)**

* `POST /api/bills`: (Role: Admin) Nhập chỉ số điện nước, tạo hóa đơn.
* `GET /api/bills/my-bills`: (Role: Student) Xem hóa đơn phòng mình.
* `PATCH /api/bills/:id/pay`: (Role: Student) Nộp biên lai, đổi trạng thái hóa đơn.

**Maintenance (Sự cố)**

* `POST /api/maintenance`: (Role: Student) Gửi phiếu báo cáo sự cố kèm ảnh.
* `GET /api/maintenance`: (Role: Admin/Maintenance) Xem danh sách sự cố.
* `PATCH /api/maintenance/:id/assign`: (Role: Admin) Phân công nhân viên.
* `PATCH /api/maintenance/:id/status`: (Role: Maintenance) Cập nhật tiến độ sửa chữa.

---
