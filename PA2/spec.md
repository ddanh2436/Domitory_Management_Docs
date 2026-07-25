# ĐẶC TẢ KỸ THUẬT: HỆ THỐNG QUẢN LÝ KÝ TÚC XÁ (DORMIFY)

## 1. Tổng quan dự án (Project Overview)
Hệ thống quản lý Ký túc xá toàn diện hỗ trợ 3 nhóm đối tượng chính:
1. **Khách hàng/Sinh viên:** Đăng ký lưu trú, xem thông tin, thanh toán, báo cáo sự cố.
2. **Quản trị hệ thống (System Admin):** Quản lý tài khoản, phân quyền, bảo mật, sao lưu dữ liệu.
3. **Quản lý KTX (Dormitory Manager):** Vận hành thực tế (phòng ốc, hợp đồng, tài chính, nội quy, bảo trì).

## 2. Kiến trúc công nghệ (Tech Stack)
* **Frontend:** Next.js (App Router), Tailwind CSS, Recharts, React-Icons.
* **Backend:** NestJS, Mongoose, Socket.IO (Real-time notifications).
* **Database:** MongoDB.

---

## 3. Danh sách Yêu cầu Chức năng (Functional Requirements)

### MODULE 1: QUẢN LÝ TÀI KHOẢN & XÁC THỰC (Sinh viên / Khách hàng)
- [ ] **FR01: Đăng ký hồ sơ lưu trú mới:** Sinh viên/khách nhập thông tin cá nhân sơ bộ, chọn loại dịch vụ. Hệ thống gửi thông báo xác nhận qua Email/SĐT để liên hệ bước tiếp theo.
- [ ] **FR02: Đăng nhập & Đăng xuất (Login/Logout):** - Hỗ trợ Google Authentication hoặc Email/CCCD và Mật khẩu. 
  - Quên mật khẩu: Gửi mã OTP qua Google Auth/Email.
- [ ] **FR03: Quản lý hồ sơ cá nhân sinh viên:** Sinh viên xem thông tin cá nhân (Họ tên, ngày sinh, CCCD, địa chỉ) và xem thông tin cư trú/hợp đồng (Chỉ Admin mới có quyền chỉnh sửa dữ liệu gốc).

### MODULE 2: QUẢN TRỊ HỆ THỐNG (System Administrator)
- [ ] **FR04: Quản lý tài khoản người dùng:** Xóa, khóa/mở khóa tài khoản hiện hữu.
- [ ] **FR05: Quản lý vai trò & Phân quyền (RBAC):** Tạo Role mới, gán quyền cho Role, gán Role cho User, thu hồi quyền.
- [ ] **FR06: Theo dõi nhật ký hệ thống (System Logs):** Ghi nhận ai làm gì, thời gian nào, dữ liệu nào bị thay đổi.
- [✅] **FR07: Dashboard quản trị:** Bảng điều khiển trực quan hiển thị số liệu tổng quan và biểu đồ phân tích (Recharts).
- [ ] **FR08: Sao lưu và phục hồi dữ liệu:** Backup tự động/thủ công và Restore dữ liệu phòng hờ rủi ro.

### MODULE 3: VẬN HÀNH NGHIỆP VỤ KTX (Dormitory Manager)

#### 3.1. Quản lý Phòng (Room Management)
- [✅] **FR09: Quản lý danh mục phòng:** Thêm, cập nhật thông tin, xóa phòng, đánh dấu phòng đang bảo trì.

#### 3.2. Quản lý Sinh viên & Xếp phòng
- [ ] **FR10: Quản lý hồ sơ sinh viên:** Xem/chỉnh sửa hồ sơ, theo dõi lịch sử thuê phòng.
- [✅] **FR11: Duyệt yêu cầu thuê phòng:** Kiểm tra đơn, phê duyệt/từ chối, tự động trừ số giường trống.
- [ ] **FR12: Phân phòng tự động (Auto-assignment):** Dựa trên nhu cầu, giới tính để xếp chỗ hàng loạt vào đầu năm học.
- [ ] **FR13: Chuyển phòng:** Xử lý yêu cầu đổi phòng và lưu lịch sử.

#### 3.3. Quản lý Hợp đồng (Contracts)
- [ ] **FR14: Quản lý chung hợp đồng:** Thêm vào CSDL, theo dõi trạng thái.
- [ ] **FR15: Gia hạn hợp đồng.**
- [ ] **FR16: Thanh lý hợp đồng.**
- [ ] **FR17: Xuất PDF:** Cho phép tải xuống hợp đồng dưới dạng file PDF để in ấn/lưu trữ.

#### 3.4. Quản lý Trả phòng (Checkout)
- [ ] **FR18: Tiếp nhận trả phòng:** Nhận yêu cầu trả phòng từ sinh viên.
- [ ] **FR19: Kiểm tra tài sản:** Đánh giá mức độ hư hại thiết bị đã bàn giao.
- [ ] **FR20: Tính phí bồi thường:** Trừ tiền cọc dựa trên danh mục hư hỏng (VD: Hỏng bàn -200k).
- [ ] **FR21: Hoàn tiền cọc:** Tính toán số tiền còn lại và hoàn trả.

#### 3.5. Quản lý Tài chính (Finance)
- [✅] **FR22: Theo dõi hóa đơn:** Quản lý hóa đơn Điện, Nước, Phí phòng, Sửa chữa. Tích hợp cổng thanh toán (Mock Payment).
- [ ] **FR23: Theo dõi công nợ:** Thống kê các sinh viên/phòng đang nợ tiền, nhắc nợ.
- [✅] **FR24: Báo cáo doanh thu:** Hiển thị biểu đồ doanh thu theo thời gian (Đã tích hợp cơ bản vào Dashboard).

#### 3.6. Quản lý Cư trú & Nội quy
- [ ] **FR25: Theo dõi báo vắng qua đêm:** Sinh viên tự khai báo vắng mặt trên hệ thống, quản sinh theo dõi danh sách để quản lý tạm trú tạm vắng.
- [ ] **FR29: Quản lý vi phạm:** Ghi nhận và lưu lịch sử vi phạm nội quy của sinh viên.
- [ ] **FR30: Đánh giá điểm sinh viên:** Hệ thống điểm rèn luyện, tự động trừ điểm theo bộ Rules vi phạm đã thiết lập.

#### 3.7. Quản lý Sự cố (Maintenance)
- [✅] **FR26: Tiếp nhận yêu cầu sửa chữa:** Sinh viên báo cáo sự cố qua form.
- [✅] **FR27: Phân công nhân viên:** Quản lý bấm nút tiếp nhận và gọi bên thứ 3 (Status update).
- [✅] **FR28: Theo dõi tiến độ:** Cập nhật trạng thái Pending -> In Progress -> Resolved (Tích hợp thông báo Real-time Socket.IO).

---
*Ghi chú: [✅] là các tính năng đã hoàn thiện. [ ] là các tính năng chờ phát triển.*