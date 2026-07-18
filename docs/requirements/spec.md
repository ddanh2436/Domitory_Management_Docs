# ĐẶC TẢ KỸ THUẬT: HỆ THỐNG QUẢN LÝ KÝ TÚC XÁ (DORMIFY)

> **Cập nhật lần cuối:** 18/07/2026 — đồng bộ trạng thái với mã nguồn thực tế (backend + frontend).

## 1. Tổng quan dự án (Project Overview)
Hệ thống quản lý Ký túc xá toàn diện hỗ trợ 3 nhóm đối tượng chính:
1. **Khách hàng/Sinh viên:** Đăng ký lưu trú, xem thông tin, thanh toán, báo cáo sự cố.
2. **Quản trị hệ thống (System Admin):** Quản lý tài khoản, phân quyền, bảo mật, sao lưu dữ liệu.
3. **Quản lý KTX (Dormitory Manager):** Vận hành thực tế (phòng ốc, hợp đồng, tài chính, nội quy, bảo trì).
4. **Nhân viên bảo trì (Maintenance Staff):** Tiếp nhận và xử lý các yêu cầu sửa chữa được phân công.

## 2. Kiến trúc công nghệ (Tech Stack)
* **Frontend:** Next.js (App Router), Tailwind CSS, Recharts, React-Icons, Socket.IO Client.
* **Backend:** NestJS, Mongoose, Socket.IO (Real-time notifications), Cloudinary (ảnh bảo trì).
* **Database:** MongoDB (Atlas).

---

## 3. Danh sách Yêu cầu Chức năng (Functional Requirements)

### MODULE 1: QUẢN LÝ TÀI KHOẢN & XÁC THỰC (Sinh viên / Khách hàng)
- [✅] **FR01: Đăng ký hồ sơ lưu trú mới:** Sinh viên/khách nhập thông tin cá nhân sơ bộ, tạo tài khoản qua `POST /api/auth/register`.
- [✅] **FR02: Đăng nhập & Đăng xuất (Login/Logout):** Hỗ trợ Google Authentication (`/api/auth/google`) và Email + Mật khẩu.
  - Quên mật khẩu: gửi link đặt lại mật khẩu (token có hạn) qua Email.
- [✅] **FR03: Quản lý hồ sơ cá nhân sinh viên:** Sinh viên xem/cập nhật thông tin cá nhân và xem thông tin cư trú/hợp đồng.

### MODULE 2: QUẢN TRỊ HỆ THỐNG (System Administrator)
- [✅] **FR04: Quản lý tài khoản người dùng:** Xóa, khóa/mở khóa tài khoản (kèm lý do khóa), tài khoản LOCKED bị chặn ở tầng JWT Guard.
- [🔶] **FR05: Quản lý vai trò & Phân quyền (RBAC):** Đã có 5 vai trò cố định (STUDENT, ADMIN, DORMITORY_MANAGER, FLOOR_MANAGER, MAINTENANCE_STAFF) + trang phân quyền tài khoản. **Chưa có:** tạo Role động và gán quyền chi tiết cho Role.
- [✅] **FR06: Theo dõi nhật ký hệ thống (System Logs):** Interceptor toàn cục ghi lại mọi request thay đổi dữ liệu (ai, hành động gì, lúc nào, kết quả, IP) vào collection `auditlogs` (TTL 180 ngày). Trang xem log dành riêng cho ADMIN (`/admin/audit-logs`) có tìm kiếm/lọc/phân trang.
- [✅] **FR07: Dashboard quản trị:** Bảng điều khiển trực quan hiển thị số liệu tổng quan và biểu đồ phân tích (Recharts).
- [ ] **FR08: Sao lưu và phục hồi dữ liệu:** Backup tự động/thủ công và Restore dữ liệu phòng hờ rủi ro. **(Chưa phát triển)**

### MODULE 3: VẬN HÀNH NGHIỆP VỤ KTX (Dormitory Manager)

#### 3.1. Quản lý Phòng (Room Management)
- [✅] **FR09: Quản lý danh mục phòng:** Thêm, cập nhật thông tin, xóa phòng, đánh dấu phòng đang bảo trì. Phòng có thêm thuộc tính `genderType` (MALE/FEMALE/MIXED) phục vụ phân phòng tự động.

#### 3.2. Quản lý Sinh viên & Xếp phòng
- [✅] **FR10: Quản lý hồ sơ sinh viên:** Xem/chỉnh sửa hồ sơ, theo dõi lịch sử đặt phòng/đổi phòng qua bookings & transfers.
- [✅] **FR11: Duyệt yêu cầu thuê phòng:** Kiểm tra đơn, phê duyệt/từ chối, tự động trừ số giường trống.
- [✅] **FR12: Phân phòng tự động (Auto-assignment):** Trang `/admin/auto-assign` — xếp hàng loạt sinh viên chưa có phòng vào phòng còn chỗ, khớp giới tính (phòng MIXED nhận mọi sinh viên). Mỗi sinh viên được xếp sẽ tự động có booking đã duyệt + hợp đồng + thông báo realtime. Có guard chống vượt sức chứa khi chạy song song.
- [✅] **FR13: Chuyển phòng:** Sinh viên gửi yêu cầu, quản lý duyệt/từ chối; duyệt chạy trong transaction (occupancy 2 phòng + user.room + hợp đồng theo giá phòng mới); lưu lịch sử đầy đủ.

#### 3.3. Quản lý Hợp đồng (Contracts)
- [✅] **FR14: Quản lý chung hợp đồng:** Tự tạo từ booking được duyệt, theo dõi trạng thái ACTIVE/EXPIRED/TERMINATED; cron nhắc hợp đồng sắp hết hạn (≤7 ngày, chống spam).
- [✅] **FR15: Gia hạn hợp đồng:** Sinh viên tự gia hạn 1–12 tháng.
- [✅] **FR16: Thanh lý hợp đồng:** Thanh lý kèm trả chỗ trống cho phòng và gỡ sinh viên khỏi phòng.
- [✅] **FR17: Xuất PDF:** Tải xuống hợp đồng dưới dạng file PDF để in ấn/lưu trữ.

#### 3.4. Quản lý Trả phòng (Checkout)
- [✅] **FR18: Tiếp nhận trả phòng:** Sinh viên gửi yêu cầu trả phòng (lý do + ngày rời dự kiến) tại `/student/checkout`; quản lý nhận thông báo realtime.
- [✅] **FR19: Kiểm tra tài sản:** Quản lý ghi nhận danh mục hư hỏng từng hạng mục (tên tài sản, phí, ghi chú) trong modal "Kiểm tra & hoàn tất".
- [✅] **FR20: Tính phí bồi thường:** Tổng phí các hạng mục hư hỏng tự động trừ vào tiền cọc (quy ước cọc = 1 tháng tiền phòng, quản lý điều chỉnh được).
- [✅] **FR21: Hoàn tiền cọc:** Hoàn cọc = max(0, cọc − bồi thường). Hoàn tất chạy trong transaction: thanh lý hợp đồng + trả chỗ trống + gỡ sinh viên khỏi phòng + thông báo kèm số tiền hoàn.

#### 3.5. Quản lý Tài chính (Finance)
- [✅] **FR22: Theo dõi hóa đơn:** Quản lý hóa đơn Điện, Nước, Phí phòng; sinh hóa đơn hàng loạt theo chỉ số; cổng thanh toán Mock Payment; cron tự đánh dấu quá hạn + thông báo.
- [✅] **FR23: Theo dõi công nợ:** Trang `/admin/debts` — tổng hợp công nợ theo phòng (tổng nợ, số hóa đơn chưa đóng/quá hạn, hạn cũ nhất, danh sách sinh viên đang ở), nhắc nợ từng phòng hoặc nhắc tất cả qua thông báo realtime.
- [✅] **FR24: Báo cáo doanh thu:** Biểu đồ doanh thu thực thu theo thời gian trên Dashboard.

#### 3.6. Quản lý Cư trú & Nội quy
- [✅] **FR25: Theo dõi báo vắng qua đêm:** Sinh viên tự khai báo vắng mặt, quản sinh duyệt/từ chối và theo dõi danh sách tạm trú tạm vắng.
- [✅] **FR29: Quản lý vi phạm:** Ghi nhận và lưu lịch sử vi phạm nội quy của sinh viên.
- [✅] **FR30: Đánh giá điểm sinh viên:** Điểm hành vi khởi tạo 100, tự động trừ theo mức phạt từng vi phạm (không tụt dưới 0), sinh viên nhận thông báo kèm điểm còn lại.

#### 3.7. Quản lý Sự cố (Maintenance)
- [✅] **FR26: Tiếp nhận yêu cầu sửa chữa:** Sinh viên báo sự cố qua form kèm ảnh (Cloudinary) + mức ưu tiên.
- [✅] **FR27: Phân công nhân viên:** Quản lý phân công cho nhân viên bảo trì cụ thể; nhân viên nhận thông báo realtime và xem việc tại khu vực `/staff` (có bộ lọc, tìm kiếm, điểm đánh giá trung bình).
- [✅] **FR28: Theo dõi tiến độ:** Pending → In Progress → Resolved (realtime Socket.IO); sinh viên đánh giá 1–5 sao sau khi hoàn thành.

---

## 4. Tính năng chờ phát triển (Backlog)

| # | Tính năng | Ghi chú |
| --- | --- | --- |
| 1 | **FR08 — Sao lưu & phục hồi dữ liệu** | Mục duy nhất trong spec gốc chưa làm. Hướng làm: endpoint admin gọi `mongodump`/`mongorestore` hoặc export/import JSON theo collection. |
| 2 | **FR05 — RBAC động** | Tạo Role mới + gán quyền chi tiết cho Role (hiện dùng 5 vai trò cố định). |
| 3 | **Ô nhập giới tính** | Thêm trường giới tính vào form hồ sơ sinh viên và form tạo/sửa phòng để phân phòng tự động tách nam/nữ thực sự (backend đã sẵn sàng). |
| 4 | **Cổng thanh toán thật** | Thay Mock Payment bằng VNPay/MoMo/ZaloPay như đề xuất trong proposal. |
| 5 | **Đăng ký khách thăm** (proposal 3.6) | Chưa có trong spec lẫn code. |
| 6 | **Hòm thư góp ý/khiếu nại** (proposal 3.8) | Đã có thông báo 1 chiều (announcements), chưa có kênh phản hồi ngược từ sinh viên. |
| 7 | **Tính năng AI** (proposal mục 4) | NLP phân loại ticket bảo trì + RAG chatbot nội quy. |

---
*Ghi chú: [✅] hoàn thiện · [🔶] hoàn thiện một phần · [ ] chờ phát triển.*
