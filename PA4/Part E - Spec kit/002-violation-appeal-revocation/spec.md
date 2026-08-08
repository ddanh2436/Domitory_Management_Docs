# Feature Specification: Khiếu nại & Thu hồi vi phạm nề nếp

**Feature Branch**: `002-violation-appeal-revocation`

**Created**: 2026-08-07

**Status**: Draft

**Input**: User description: "Mở rộng nhóm vi phạm nề nếp: sinh viên có thể khiếu nại một vi phạm kèm lý do; ban quản lý duyệt (chấp nhận thì thu hồi vi phạm và hoàn lại điểm hành vi, hoặc từ chối kèm lý do); ban quản lý cũng có thể thu hồi trực tiếp vi phạm ghi nhầm. Có hàng đợi duyệt khiếu nại."

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Sinh viên khiếu nại vi phạm (Priority: P1)

Là một **sinh viên** bị ghi nhận vi phạm mà tôi cho là sai/oan, tôi muốn **gửi khiếu nại
kèm lý do**, để ban quản lý xem xét lại thay vì phải chịu trừ điểm hành vi vô căn cứ.

**Why this priority**: Đây là giá trị cốt lõi và điểm khởi đầu của toàn bộ luồng — không
có khiếu nại thì không có gì để duyệt. Hoàn thành story này (kèm US2) là đã có MVP hữu ích.

**Independent Test**: Đăng nhập sinh viên có một vi phạm đang hiệu lực (`ACTIVE`), bấm
"Khiếu nại", để trống lý do → bị chặn; nhập lý do → vi phạm chuyển `APPEAL_PENDING` và ban
quản lý nhận thông báo.

**Acceptance Scenarios**:

1. **Given** sinh viên có vi phạm ở trạng thái `ACTIVE`, **When** gửi khiếu nại với lý do
   hợp lệ, **Then** vi phạm chuyển sang `APPEAL_PENDING`, lưu lý do khiếu nại, ban quản lý
   nhận thông báo.
2. **Given** sinh viên gửi khiếu nại **không** có lý do, **When** xác nhận, **Then** hệ
   thống chặn và yêu cầu nhập lý do (trạng thái không đổi).
3. **Given** một vi phạm đã ở `APPEAL_PENDING`/`REVOKED`/`APPEAL_REJECTED`, **When** sinh
   viên cố khiếu nại lại, **Then** hệ thống từ chối (mỗi vi phạm chỉ khiếu nại khi đang `ACTIVE`).
4. **Given** một vi phạm **không** thuộc về sinh viên đang đăng nhập, **When** sinh viên đó
   cố khiếu nại, **Then** hệ thống từ chối (chỉ khiếu nại vi phạm của chính mình).

---

### User Story 2 - Ban quản lý duyệt khiếu nại (Priority: P1)

Là **ban quản lý**, tôi muốn xem các khiếu nại đang chờ và **chấp nhận hoặc từ chối** từng
cái; nếu chấp nhận thì vi phạm được thu hồi và **điểm hành vi được hoàn lại**.

**Why this priority**: Là nửa còn lại của giá trị cốt lõi — quyết định kết quả khiếu nại và
khôi phục công bằng cho sinh viên.

**Independent Test**: Với một vi phạm `APPEAL_PENDING`, admin chọn "Chấp nhận" → vi phạm
chuyển `REVOKED`, điểm hành vi của sinh viên tăng lại đúng số điểm đã trừ (không vượt 100);
chọn "Từ chối" → chuyển `APPEAL_REJECTED`, điểm giữ nguyên.

**Acceptance Scenarios**:

1. **Given** vi phạm `APPEAL_PENDING` trừ 10 điểm (điểm hiện tại 80), **When** admin chấp
   nhận khiếu nại, **Then** vi phạm chuyển `REVOKED` và điểm hành vi trở lại 90.
2. **Given** vi phạm `APPEAL_PENDING` mà sinh viên đang có điểm 95, đã trừ 10, **When**
   admin chấp nhận, **Then** điểm hoàn lại nhưng **bị chặn ở 100** (không vượt trần).
3. **Given** vi phạm `APPEAL_PENDING`, **When** admin từ chối kèm ghi chú, **Then** vi phạm
   chuyển `APPEAL_REJECTED`, lưu ghi chú, điểm hành vi **không đổi**, sinh viên nhận thông báo.
4. **Given** một vi phạm **không** ở `APPEAL_PENDING`, **When** admin cố duyệt, **Then** hệ
   thống từ chối (chỉ duyệt được khiếu nại đang chờ).

---

### User Story 3 - Ban quản lý thu hồi trực tiếp vi phạm ghi nhầm (Priority: P2)

Là **ban quản lý**, khi phát hiện mình **ghi nhầm** một vi phạm, tôi muốn **thu hồi trực
tiếp** (không cần chờ sinh viên khiếu nại) và hệ thống **hoàn lại điểm** đã trừ.

**Why this priority**: Xử lý lỗi nhập liệu chủ động; hữu ích nhưng không phải luồng chính.

**Independent Test**: Admin bấm "Thu hồi" trên một vi phạm chưa bị thu hồi → vi phạm chuyển
`REVOKED`, điểm hành vi được hoàn (chặn 100); thu hồi lần nữa không cộng điểm thêm.

**Acceptance Scenarios**:

1. **Given** vi phạm `ACTIVE` (hoặc `APPEAL_PENDING`/`APPEAL_REJECTED`), **When** admin thu
   hồi, **Then** vi phạm chuyển `REVOKED` và điểm hành vi được hoàn lại (≤100).
2. **Given** vi phạm đã `REVOKED`, **When** admin thu hồi lại, **Then** hệ thống không hoàn
   điểm lần hai (bảo đảm idempotent).

---

### User Story 4 - Hàng đợi duyệt & tổng quan vi phạm (Priority: P3)

Là **ban quản lý**, tôi muốn một màn hình **liệt kê tất cả vi phạm** kèm trạng thái và lọc
nhanh các **khiếu nại đang chờ**, để xử lý tập trung.

**Why this priority**: Tăng hiệu quả vận hành; các story trên vẫn dùng được mà không cần nó.

**Independent Test**: Mở màn hình quản lý vi phạm → thấy danh sách kèm trạng thái; lọc
"Đang chờ duyệt" → chỉ còn các vi phạm `APPEAL_PENDING`.

**Acceptance Scenarios**:

1. **Given** có nhiều vi phạm ở các trạng thái khác nhau, **When** admin mở màn hình,
   **Then** danh sách hiển thị kèm sinh viên, người ghi nhận, trạng thái và (nếu có) lý do
   khiếu nại.
2. **Given** đang ở màn hình, **When** admin chọn bộ lọc "Đang chờ duyệt", **Then** chỉ các
   vi phạm `APPEAL_PENDING` hiển thị.

---

### Edge Cases

- Khiếu nại chỉ gồm khoảng trắng → coi như rỗng, bị chặn.
- Sinh viên bị khóa tài khoản (`LOCKED`) → không truy cập được API (guard chặn sẵn).
- Hoàn điểm khi điểm hiện tại + điểm hoàn > 100 → chặn ở trần 100.
- Vi phạm của sinh viên đã bị xóa/không tồn tại → báo lỗi rõ ràng.
- Quyết định duyệt không phải `ACCEPT`/`REJECT` → báo lỗi 400.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: Sinh viên MUST gửi được khiếu nại cho một vi phạm của **chính mình** đang ở
  trạng thái `ACTIVE`, kèm **lý do bắt buộc** (≤500 ký tự).
- **FR-002**: Mỗi vi phạm MUST chỉ khiếu nại được khi đang `ACTIVE`; các trạng thái khác bị từ chối.
- **FR-003**: Khi khiếu nại, hệ thống MUST đổi trạng thái sang `APPEAL_PENDING`, lưu lý do +
  thời điểm, và thông báo cho ban quản lý.
- **FR-004**: Ban quản lý MUST duyệt một khiếu nại `APPEAL_PENDING` với quyết định
  `ACCEPT` hoặc `REJECT` (kèm ghi chú tùy chọn ≤500 ký tự).
- **FR-005**: `ACCEPT` MUST chuyển vi phạm sang `REVOKED` và **hoàn lại đúng số điểm đã trừ**
  vào điểm hành vi của sinh viên, **không vượt quá 100**; thông báo cho sinh viên.
- **FR-006**: `REJECT` MUST chuyển vi phạm sang `APPEAL_REJECTED`, lưu ghi chú; điểm hành vi
  **giữ nguyên**; thông báo cho sinh viên.
- **FR-007**: Ban quản lý MUST thu hồi trực tiếp một vi phạm **chưa** `REVOKED`, chuyển sang
  `REVOKED` và hoàn điểm (≤100); thông báo cho sinh viên.
- **FR-008**: Việc hoàn điểm MUST **idempotent** — vi phạm đã `REVOKED` không hoàn điểm lần nữa.
- **FR-009**: Ban quản lý MUST xem được danh sách tất cả vi phạm (kèm sinh viên, người ghi
  nhận, trạng thái) và lọc theo trạng thái (đặc biệt `APPEAL_PENDING`).
- **FR-010**: Chỉ `STUDENT` mới khiếu nại; chỉ `ADMIN`/`DORMITORY_MANAGER` mới duyệt/thu hồi/xem tất cả.

### Key Entities *(include if feature involves data)*

- **Vi phạm (Violation)**: Bổ sung `status` (ACTIVE/APPEAL_PENDING/REVOKED/APPEAL_REJECTED),
  `appealReason`, `appealedAt`, `reviewNote`, `reviewedBy`, `reviewedAt`. Giữ nguyên
  `student`, `reason`, `points`, `markedBy`, `scoreAfter`.
- **Điểm hành vi (User.behaviorScore)**: Bị trừ khi ghi vi phạm (đã có), được **hoàn lại**
  khi vi phạm bị thu hồi (mới), trần 100.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: 100% khiếu nại được chấp nhận đều hoàn đúng số điểm đã trừ (không vượt 100).
- **SC-002**: Không tồn tại vi phạm `REVOKED` mà điểm bị hoàn hai lần (idempotent đúng).
- **SC-003**: Sinh viên gửi khiếu nại và thấy trạng thái khiếu nại của mình trong dưới 20 giây.
- **SC-004**: Ban quản lý lọc và xử lý toàn bộ khiếu nại đang chờ trên một màn hình duy nhất.

## Assumptions

- Cơ chế ghi vi phạm + trừ điểm hành vi đã có sẵn (module violations hiện tại); tính năng
  này bổ sung khiếu nại/duyệt/thu hồi và luồng hoàn điểm.
- Xác thực JWT + phân quyền theo vai trò được tái sử dụng.
- Thông báo dùng `NotificationsService` sẵn có.
- Không hồi tố: các vi phạm cũ mặc định coi là `ACTIVE`.
