# Đặc tả Yêu cầu (Specification)
Dự án: Hệ thống Quản lý Ký túc xá

## Vai trò Người dùng (Roles)
1. Student (Sinh viên / Khách thuê)
2. Admin (Ban quản lý)
3. Maintenance (Nhân viên bảo trì)

## Yêu cầu Chức năng Cốt lõi (Functional Requirements)

### Nhóm 1: Quản lý Phòng & Lưu trú
- FR01: Sinh viên tìm kiếm phòng trống theo bộ lọc (loại phòng, giá tiền, số giường trống).
- FR02: Sinh viên gửi yêu cầu đặt phòng (Submit rental request) kèm thông tin cá nhân.
- FR05: Admin duyệt/từ chối yêu cầu đặt phòng; tự động cập nhật số giường trống.
- FR06: Admin tạo và xuất hợp đồng thuê phòng (PDF).
- FR11: Cung cấp form xử lý quy trình trả phòng cho Sinh viên.
- FR12: Chức năng đăng ký tạm trú, tạm vắng cho Sinh viên.

### Nhóm 2: Quản lý Sự cố Cơ sở vật chất
- FR03: Sinh viên gửi phiếu báo cáo sự cố (kèm hình ảnh).
- FR08: Admin tiếp nhận và phân công sự cố cho Nhân viên bảo trì.
- FR09: Nhân viên bảo trì xem danh sách sự cố được phân công.
- FR10: Nhân viên bảo trì cập nhật trạng thái sự cố (Đang xử lý, Hoàn thành) và ghi chú.

### Nhóm 3: Tài chính & Hóa đơn
- FR07: Admin tạo hóa đơn điện, nước hàng tháng bằng cách nhập chỉ số đầu/cuối.
- FR04: Sinh viên xem hóa đơn tổng và xác nhận thanh toán bằng cách tải lên biên lai.

### Nhóm 4: Xác thực & Người dùng
- FR13: Đăng nhập / Đăng xuất hệ thống (Phân quyền 3 roles).
- FR14: Xem và cập nhật trang hồ sơ cá nhân (Chỉ truy cập khi đã đăng nhập).