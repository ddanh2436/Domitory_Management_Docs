# Feature Specification: Nhật ký xử lý bảo trì & Từ chối có lý do

**Feature Branch**: `001-maintenance-resolution-log`

**Created**: 2026-07-20

**Status**: Draft

**Input**: User description: "Cải thiện khu vực staff/maintenance: nhân viên bảo trì phải nhập lý do khi từ chối yêu cầu, ghi nội dung đã xử lý khi hoàn thành; sinh viên và ban quản lý xem được lý do/kết quả và nhật ký các lần đổi trạng thái."

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Từ chối yêu cầu kèm lý do bắt buộc (Priority: P1)

Là **nhân viên bảo trì**, khi một yêu cầu được giao cho tôi không hợp lệ (báo sai, không
thuộc phạm vi sửa chữa, trùng lặp), tôi muốn **từ chối yêu cầu và bắt buộc nêu lý do**,
để sinh viên và ban quản lý hiểu vì sao yêu cầu không được thực hiện thay vì thấy nó bị
đóng mà không rõ nguyên nhân.

**Why this priority**: Trạng thái `REJECTED` đã tồn tại trong hệ thống nhưng nhân viên
bảo trì hoàn toàn **không có cách từ chối** và không lưu được lý do — đây là lỗ hổng
nghiêm trọng nhất về minh bạch. Chỉ cần hoàn thành story này, tính năng đã có giá trị
sử dụng (MVP).

**Independent Test**: Đăng nhập bằng tài khoản `MAINTENANCE_STAFF` đang được giao một
yêu cầu ở trạng thái `PENDING`/`IN_PROGRESS`, bấm "Từ chối", để trống lý do → hệ thống
chặn; nhập lý do và xác nhận → yêu cầu chuyển sang `REJECTED` và lý do được lưu, hiển thị
lại cho sinh viên.

**Acceptance Scenarios**:

1. **Given** nhân viên đang xem một yêu cầu được giao cho mình, **When** bấm "Từ chối"
   và **để trống** lý do rồi xác nhận, **Then** hệ thống từ chối thao tác và yêu cầu
   nhập lý do (không đổi trạng thái).
2. **Given** nhân viên đã nhập lý do từ chối hợp lệ, **When** xác nhận, **Then** yêu cầu
   chuyển sang trạng thái `REJECTED`, lý do được lưu, và sinh viên nhận được thông báo
   kèm lý do.
3. **Given** một yêu cầu **không được giao** cho nhân viên đang đăng nhập, **When** nhân
   viên đó cố từ chối yêu cầu, **Then** hệ thống từ chối (chỉ xử lý được việc của mình).
4. **Given** yêu cầu đã ở trạng thái `REJECTED`, **When** sinh viên mở màn hình bảo trì,
   **Then** thấy rõ nhãn "Bị từ chối" kèm nội dung lý do.

---

### User Story 2 - Ghi nội dung đã xử lý khi hoàn thành (Priority: P2)

Là **nhân viên bảo trì**, khi hoàn thành sửa chữa, tôi muốn **ghi lại ngắn gọn đã xử lý
gì** (thay linh kiện, nội dung khắc phục), để sinh viên và ban quản lý biết công việc đã
được thực hiện thế nào và làm căn cứ đánh giá.

**Why this priority**: Tăng minh bạch và chất lượng dịch vụ, hỗ trợ sinh viên chấm sao
có cơ sở. Có giá trị cao nhưng không phải điều kiện tối thiểu như việc từ chối.

**Independent Test**: Với một yêu cầu `IN_PROGRESS` được giao cho mình, nhân viên bấm
"Hoàn thành", nhập nội dung đã xử lý (tùy chọn) và xác nhận → yêu cầu chuyển `RESOLVED`,
nội dung xử lý được lưu và hiển thị cho sinh viên.

**Acceptance Scenarios**:

1. **Given** một yêu cầu `IN_PROGRESS` được giao cho nhân viên, **When** nhân viên đánh
   dấu hoàn thành và nhập nội dung đã xử lý, **Then** yêu cầu chuyển `RESOLVED` và nội
   dung xử lý được lưu.
2. **Given** nhân viên hoàn thành nhưng **không** nhập nội dung xử lý, **When** xác nhận,
   **Then** yêu cầu vẫn chuyển `RESOLVED` thành công (nội dung xử lý là tùy chọn).
3. **Given** yêu cầu đã `RESOLVED` có nội dung xử lý, **When** sinh viên mở màn hình bảo
   trì, **Then** thấy nội dung nhân viên đã xử lý bên cạnh ô đánh giá sao.

---

### User Story 3 - Nhật ký các lần đổi trạng thái (Priority: P3)

Là **sinh viên / ban quản lý**, tôi muốn xem **dòng thời gian các lần đổi trạng thái** của
một yêu cầu (ai đổi, sang trạng thái gì, lúc nào, kèm ghi chú nếu có), để theo dõi được
toàn bộ quá trình xử lý.

**Why this priority**: Tính năng bổ trợ, nâng trải nghiệm theo dõi; không bắt buộc cho MVP.

**Independent Test**: Sau khi một yêu cầu trải qua vài lần đổi trạng thái, mở chi tiết
yêu cầu và xác nhận nhật ký liệt kê đúng thứ tự thời gian các mốc chuyển trạng thái.

**Acceptance Scenarios**:

1. **Given** một yêu cầu đã trải qua `PENDING → IN_PROGRESS → RESOLVED`, **When** người
   dùng xem chi tiết, **Then** nhật ký hiển thị 3 mốc kèm thời gian và người thực hiện.
2. **Given** một mốc chuyển trạng thái có ghi chú (lý do từ chối hoặc nội dung xử lý),
   **When** xem nhật ký, **Then** ghi chú hiển thị kèm mốc tương ứng.

---

### Edge Cases

- Nhân viên từ chối với lý do chỉ gồm khoảng trắng → coi như trống, bị chặn.
- Lý do từ chối / nội dung xử lý quá dài → giới hạn độ dài hợp lý (500 ký tự) để tránh
  lạm dụng và vỡ giao diện.
- Ban quản lý (ADMIN) từ chối yêu cầu → cũng phải nêu lý do (đồng nhất quy tắc với nhân
  viên), vì luồng admin hiện có nút từ chối.
- Đổi sang trạng thái không hợp lệ hoặc yêu cầu không tồn tại → báo lỗi rõ ràng.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: Nhân viên bảo trì MUST có thao tác "Từ chối" một yêu cầu được giao cho mình,
  chuyển yêu cầu sang trạng thái `REJECTED`.
- **FR-002**: Hệ thống MUST bắt buộc nhập **lý do từ chối** khác rỗng khi chuyển sang
  `REJECTED`; nếu thiếu, thao tác bị từ chối và trạng thái không đổi.
- **FR-003**: Hệ thống MUST lưu lý do từ chối gắn với yêu cầu và hiển thị lại cho sinh
  viên (chủ yêu cầu) và ban quản lý.
- **FR-004**: Khi chuyển sang `REJECTED`, hệ thống MUST gửi thông báo cho sinh viên kèm
  lý do từ chối.
- **FR-005**: Nhân viên bảo trì MUST có thể nhập **nội dung đã xử lý** (tùy chọn) khi
  chuyển yêu cầu sang `RESOLVED`; hệ thống lưu và hiển thị lại cho sinh viên và ban quản lý.
- **FR-006**: Hệ thống MUST ghi **nhật ký** mỗi lần đổi trạng thái gồm: trạng thái đích,
  người thực hiện (họ tên + vai trò), thời điểm, và ghi chú kèm theo (lý do/nội dung xử lý).
- **FR-007**: Hệ thống MUST chỉ cho nhân viên bảo trì cập nhật (bao gồm từ chối/hoàn thành)
  các yêu cầu **được giao cho chính mình**; ban quản lý cập nhật được mọi yêu cầu.
- **FR-008**: Ban quản lý MUST cũng phải nêu lý do khi từ chối một yêu cầu (đồng nhất với
  nhân viên bảo trì).
- **FR-009**: Lý do từ chối và nội dung xử lý MUST giới hạn tối đa 500 ký tự.

### Key Entities *(include if feature involves data)*

- **Yêu cầu bảo trì (Maintenance)**: Bổ sung các thuộc tính: `rejectionReason` (lý do từ
  chối), `resolutionNote` (nội dung đã xử lý), `statusHistory` (danh sách mốc đổi trạng
  thái). Giữ nguyên các thuộc tính hiện có (user, room, title, status, priority, rating…).
- **Mốc nhật ký (StatusHistory entry)**: Mỗi mốc gồm `status` (trạng thái đích), `note`
  (ghi chú), `changedByName`, `changedByRole`, `at` (thời điểm).

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: 100% các yêu cầu bị từ chối đều có lý do kèm theo (không tồn tại yêu cầu
  `REJECTED` mà thiếu lý do).
- **SC-002**: Sinh viên xem được lý do từ chối hoặc nội dung xử lý ngay trên màn hình bảo
  trì của mình, không cần liên hệ ban quản lý.
- **SC-003**: Nhân viên bảo trì thao tác từ chối/hoàn thành kèm ghi chú trong dưới 20 giây.
- **SC-004**: Giảm số thắc mắc "vì sao yêu cầu bị đóng" gửi tới ban quản lý.

## Assumptions

- Việc phân công yêu cầu cho nhân viên bảo trì đã có sẵn (module maintenance hiện tại);
  tính năng này chỉ bổ sung luồng từ chối/hoàn thành có ghi chú và nhật ký.
- Cơ chế xác thực JWT và phân quyền theo vai trò hiện có được tái sử dụng.
- Nhật ký trạng thái chỉ ghi từ thời điểm tính năng triển khai; các yêu cầu cũ có thể chưa
  có nhật ký đầy đủ (không hồi tố).
- Thông báo realtime dùng hạ tầng `NotificationsService`/Socket.IO sẵn có.
