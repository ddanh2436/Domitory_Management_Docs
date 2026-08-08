# Research: Khiếu nại & Thu hồi vi phạm

## Quyết định 1 — Mô hình trạng thái vi phạm

**Quyết định**: Dùng enum `VIOLATION_STATUS` gồm 4 trạng thái:
`ACTIVE` → `APPEAL_PENDING` → (`REVOKED` | `APPEAL_REJECTED`); ngoài ra `ACTIVE`/`APPEAL_PENDING`/`APPEAL_REJECTED` có thể bị admin thu hồi trực tiếp sang `REVOKED`.

**Lý do**: Phản ánh đúng vòng đời khiếu nại và cho phép hiển thị rõ kết quả cho sinh viên;
tách `APPEAL_REJECTED` khỏi `ACTIVE` để giữ lịch sử và tránh khiếu nại lặp vô hạn.

**Loại bỏ**: Dùng cờ boolean `isRevoked`/`isAppealed` rời rạc — dễ sinh tổ hợp trạng thái
mâu thuẫn, khó kiểm soát chuyển tiếp.

## Quyết định 2 — Hoàn điểm hành vi idempotent

**Quyết định**: Chỉ hoàn điểm khi vi phạm **chuyển sang** `REVOKED` từ trạng thái khác; nếu
đã `REVOKED` thì không cộng lại. Điểm hoàn = `violation.points`, kết quả `min(100, current + points)`.

**Lý do**: Tránh cộng điểm nhiều lần (double-restore) khi bấm thu hồi lặp; chặn trần 100
đúng bất biến của `behaviorScore`.

**Loại bỏ**: Tính lại điểm từ đầu bằng cách cộng dồn mọi vi phạm — tốn kém và dễ sai khi dữ
liệu cũ không đầy đủ.

## Quyết định 3 — Không hồi tố dữ liệu cũ

**Quyết định**: Vi phạm cũ không có `status` được coi là `ACTIVE` (default schema).

**Lý do**: Đơn giản, an toàn; không cần migration ở mức đồ án.

## Quyết định 4 — Tương thích ngược API

**Quyết định**: Giữ nguyên `POST /violations`, `GET /violations/me`, `GET /violations/student/:id`.
**Thêm** `POST /violations/:id/appeal`, `PATCH /violations/:id/review`, `DELETE /violations/:id`,
`GET /violations`. Payload danh sách chỉ **thêm** field trạng thái/khiếu nại.

**Lý do**: Không phá vỡ trang hồ sơ sinh viên và form ghi vi phạm hiện có.

## Quyết định 5 — Vị trí UI

**Quyết định**: Nút "Khiếu nại" đặt ngay trong danh sách vi phạm ở trang hồ sơ sinh viên
(đã tồn tại). Việc duyệt khiếu nại đặt ở màn hình admin mới `/admin/violations` (chức năng
mới, không trùng form ghi vi phạm ở `/admin/students`).

**Lý do**: Tránh trùng lặp giao diện; đặt hành động ở nơi người dùng đang xem dữ liệu liên quan.

## Quyết định 6 — Kiểm thử

**Quyết định**: Viết `violations.service.spec.ts` (Jest) phủ: appeal hợp lệ/không hợp lệ,
review ACCEPT (hoàn điểm, chặn 100), review REJECT (giữ điểm), revoke trực tiếp, idempotent.
Bổ sung script E2E headless chạy qua API thật + MongoDB để nghiệm thu 4 kịch bản.

**Lý do**: PA4 yêu cầu kèm test; logic hoàn điểm nhiều nhánh cần được khóa bằng test.
