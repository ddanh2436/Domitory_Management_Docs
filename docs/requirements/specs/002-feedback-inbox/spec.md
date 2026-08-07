# Feature Specification: Hòm thư góp ý & khiếu nại

**Feature Branch**: `002-feedback-inbox`

**Created**: 2026-08-07

**Status**: Draft

**Input**: User description: "Triển khai backlog #6 (proposal 3.8) từ spec.md gốc: hệ thống hiện chỉ có thông báo một chiều (announcements: quản lý → sinh viên). Bổ sung kênh phản hồi ngược — sinh viên gửi góp ý/khiếu nại, ban quản lý xem và phản hồi — dựa trên use-case UC-FBK-01/02/03 đã đề xuất tại `use-case-specs/09-feedback-suggestions.md`."

## Quyết định đã chốt (trả lời các mục "⚠ decision needed" trong use-case doc)

- **Ẩn danh**: KHÔNG hỗ trợ. Mọi góp ý/khiếu nại luôn gắn với sinh viên gửi, thống nhất với cách hệ thống xử lý mọi request khác (luôn có `req.user`).
- **Schema góp ý & khiếu nại**: Dùng CHUNG một collection/endpoint, phân biệt bằng trường `type` (`COMPLAINT` | `SUGGESTION`), như phương án đề xuất trong use-case doc.
- **Phạm vi MVP luồng phản hồi**: Một chiều — sinh viên gửi, ban quản lý đọc, viết **một** phản hồi và đánh dấu kết quả cuối (`RESOLVED` hoặc `CLOSED`) trong cùng một thao tác. KHÔNG làm hội thoại nhiều lượt (thread) ở PA này — xem mục Assumptions.

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Gửi góp ý/khiếu nại và theo dõi trạng thái (Priority: P1) 🎯 MVP

Là **sinh viên**, tôi muốn **gửi một góp ý hoặc khiếu nại** về cơ sở vật chất, thái độ
nhân viên, hoá đơn, hoặc vấn đề khác, và **xem lại được** danh sách những gì mình đã gửi
kèm trạng thái xử lý, để tôi có một kênh chính thức phản ánh lên ban quản lý thay vì chỉ
nhận thông báo một chiều như hiện nay.

**Why this priority**: Đây là lỗ hổng được nêu rõ trong `spec.md` gốc (backlog #6) — hệ
thống hoàn toàn không có kênh để sinh viên chủ động liên hệ ban quản lý. Chỉ cần hoàn
thành story này, tính năng đã có giá trị sử dụng độc lập (sinh viên gửi được và tự theo
dõi, kể cả trước khi có màn hình xử lý phía quản lý).

**Independent Test**: Đăng nhập STUDENT, vào "Góp ý & Khiếu nại", chọn loại (Khiếu nại/Góp
ý) + danh mục, nhập nội dung, gửi → mục vừa gửi xuất hiện ngay trong "Lịch sử của tôi" với
trạng thái "Chờ xử lý".

**Acceptance Scenarios**:

1. **Given** sinh viên đang ở form gửi góp ý, **When** để trống nội dung và bấm gửi, **Then**
   hệ thống chặn và yêu cầu nhập nội dung.
2. **Given** sinh viên chọn loại "Khiếu nại", danh mục "Thái độ nhân viên", nhập nội dung hợp
   lệ, **When** bấm gửi, **Then** hệ thống tạo bản ghi trạng thái `PENDING`, hiển thị ngay
   trong danh sách của sinh viên, và ban quản lý (ADMIN, DORMITORY_MANAGER) nhận được thông
   báo realtime có mục mới cần xử lý.
3. **Given** sinh viên đã gửi vài góp ý/khiếu nại, **When** mở "Lịch sử của tôi", **Then** thấy
   danh sách sắp xếp mới nhất trước, mỗi mục hiển thị loại, danh mục, nội dung, trạng thái.
4. **Given** một góp ý đã được ban quản lý phản hồi, **When** sinh viên mở lại mục đó, **Then**
   thấy nội dung phản hồi và trạng thái cuối (Đã xử lý/Đã đóng).

---

### User Story 2 - Ban quản lý xem, phản hồi và xử lý (Priority: P2)

Là **ban quản lý (Dormitory Manager/Admin)**, tôi muốn **xem toàn bộ góp ý/khiếu nại**,
lọc theo loại và trạng thái, và **viết phản hồi rồi đánh dấu kết quả**, để khép lại vòng
phản hồi hai chiều với sinh viên và không bỏ sót phản ánh nào.

**Why this priority**: Không có story này thì US1 chỉ là một hòm thư một chiều nữa —
đúng vấn đề mà tính năng này được tạo ra để giải quyết. Ưu tiên P2 vì phụ thuộc dữ liệu do
US1 tạo ra.

**Independent Test**: Đăng nhập ADMIN/DORMITORY_MANAGER, vào trang quản lý góp ý, lọc theo
trạng thái "Chờ xử lý", mở một mục, nhập phản hồi, chọn "Đánh dấu đã xử lý" → mục chuyển
`RESOLVED`, sinh viên liên quan nhận thông báo realtime kèm nội dung phản hồi.

**Acceptance Scenarios**:

1. **Given** danh sách có góp ý/khiếu nại ở nhiều trạng thái, **When** ban quản lý chọn tab lọc
   theo loại (`COMPLAINT`/`SUGGESTION`) hoặc trạng thái, **Then** danh sách chỉ hiển thị đúng
   các mục khớp bộ lọc.
2. **Given** một mục đang `PENDING`, **When** ban quản lý bỏ trống nội dung phản hồi và bấm xác
   nhận, **Then** hệ thống chặn (phản hồi là bắt buộc trong luồng MVP đã chốt).
3. **Given** một mục đang `PENDING`, **When** ban quản lý nhập phản hồi hợp lệ và chọn "Đã xử
   lý" (hoặc "Đóng"), **Then** mục chuyển đúng trạng thái tương ứng, lưu người phản hồi + thời
   điểm, và sinh viên nhận thông báo.
4. **Given** một mục đã ở trạng thái `RESOLVED`/`CLOSED`, **When** ban quản lý mở lại, **Then**
   không còn thao tác phản hồi nữa (trạng thái cuối, không sửa lại được ở PA này).

---

### Edge Cases

- Nội dung góp ý/khiếu nại hoặc phản hồi chỉ gồm khoảng trắng → coi như trống, bị chặn.
- Nội dung quá dài → giới hạn 1000 ký tự để tránh lạm dụng và vỡ giao diện.
- Phản hồi một mục không tồn tại hoặc đã ở trạng thái cuối (`RESOLVED`/`CLOSED`) → báo lỗi rõ
  ràng, không đổi trạng thái.
- Sinh viên bị khoá tài khoản (`accessStatus = LOCKED`) → vẫn áp dụng theo cơ chế xác thực
  chung hiện có (JwtAuthGuard), không cần xử lý riêng trong tính năng này.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: Sinh viên MUST gửi được một góp ý/khiếu nại gồm loại (`COMPLAINT` hoặc
  `SUGGESTION`), danh mục (cơ sở vật chất/thái độ nhân viên/hoá đơn/khác), và nội dung.
- **FR-002**: Hệ thống MUST bắt buộc nội dung khác rỗng (sau khi trim) và tối đa 1000 ký tự.
- **FR-003**: Hệ thống MUST gắn mỗi góp ý/khiếu nại với sinh viên gửi (không hỗ trợ ẩn danh).
- **FR-004**: Khi có góp ý/khiếu nại mới, hệ thống MUST thông báo realtime cho toàn bộ tài
  khoản `ADMIN` và `DORMITORY_MANAGER`.
- **FR-005**: Sinh viên MUST xem được danh sách toàn bộ góp ý/khiếu nại của chính mình, sắp
  xếp mới nhất trước, kèm trạng thái và (nếu có) phản hồi.
- **FR-006**: Ban quản lý (`ADMIN`, `DORMITORY_MANAGER`) MUST xem được toàn bộ góp ý/khiếu nại
  của mọi sinh viên, lọc được theo `type` và `status`.
- **FR-007**: Ban quản lý MUST phản hồi một mục đang `PENDING` bằng cách nhập nội dung phản hồi
  (bắt buộc, tối đa 1000 ký tự) và chọn trạng thái cuối là `RESOLVED` hoặc `CLOSED`.
- **FR-008**: Hệ thống MUST chỉ cho phản hồi các mục đang ở trạng thái `PENDING`; mục đã
  `RESOLVED`/`CLOSED` là trạng thái cuối, không phản hồi lại được.
- **FR-009**: Khi một mục được phản hồi, hệ thống MUST lưu người phản hồi + thời điểm, và gửi
  thông báo realtime kèm nội dung phản hồi cho sinh viên đã gửi mục đó.
- **FR-010**: Mọi endpoint MUST được bảo vệ bởi `JwtAuthGuard` + `RolesGuard` theo đúng vai trò
  ở trên (Constitution §1 — Security First).

### Key Entities *(include if feature involves data)*

- **Góp ý/Khiếu nại (Feedback)**: `student` (người gửi), `type` (COMPLAINT/SUGGESTION),
  `category` (FACILITY/STAFF_CONDUCT/BILLING/OTHER), `message`, `status`
  (PENDING/RESOLVED/CLOSED), `response`, `respondedBy`, `respondedAt`, `createdAt`.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: 100% góp ý/khiếu nại được gửi đều xuất hiện ngay trong danh sách của sinh viên
  gửi mà không cần tải lại trang thủ công nhiều lần (dữ liệu lấy lại sau khi gửi thành công).
- **SC-002**: Ban quản lý xử lý xong một mục (đọc → phản hồi → đổi trạng thái) trong dưới 30
  giây thao tác trên giao diện.
- **SC-003**: Sinh viên nhận được thông báo realtime trong vòng vài giây sau khi ban quản lý
  phản hồi, không cần refresh trang (tái sử dụng hạ tầng Socket.IO hiện có).
- **SC-004**: Không tồn tại bản ghi `RESOLVED`/`CLOSED` mà thiếu `response`, `respondedBy`, hoặc
  `respondedAt` (bất biến dữ liệu, kiểm tra được bằng truy vấn DB).

## Assumptions

- Đây là tính năng **hoàn toàn mới** (0% code có sẵn) — không có màn hình/endpoint nào trước
  đó để tương thích ngược.
- Cơ chế xác thực JWT, phân quyền theo vai trò, và hạ tầng `NotificationsService`/Socket.IO
  hiện có được tái sử dụng nguyên trạng, không sửa đổi.
- Không hỗ trợ ẩn danh, không hỗ trợ hội thoại nhiều lượt (thread), không hỗ trợ đính kèm ảnh —
  các mở rộng này (nếu cần) là backlog riêng cho vòng sau, ngoài phạm vi tính năng này.
- "Đóng không phản hồi" (AF1 trong use-case gốc, ví dụ trường hợp spam/trùng lặp) nằm ngoài
  phạm vi MVP đã chốt với người dùng; ban quản lý vẫn phải nhập phản hồi kể cả khi chọn "Đóng".
- Quyền quản lý (xem tất cả + phản hồi) giới hạn ở `ADMIN` và `DORMITORY_MANAGER`, không cấp
  cho `FLOOR_MANAGER`/`MAINTENANCE_STAFF` — nhất quán với cách module `violations` hiện tại
  phân quyền cho các thao tác xử lý toàn hệ thống tương tự.
