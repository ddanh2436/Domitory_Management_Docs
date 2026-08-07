# Quickstart / Kịch bản demo — Khiếu nại & Thu hồi vi phạm

## Chạy hệ thống

```bash
cd src/backend && npm install && npm run start:dev   # cổng 3001
cd src/frontend && npm install && npm run dev
```

Cần `src/backend/.env` có `MONGO_URI`, `JWT_SECRET`.

## Dữ liệu cần có

- 1 `STUDENT` (đã có điểm hành vi, ví dụ 100).
- 1 `ADMIN` (hoặc `DORMITORY_MANAGER`).
- Admin ghi sẵn cho sinh viên **2–3 vi phạm** qua `/admin/students` (chọn sinh viên → ghi vi phạm).

## Kịch bản 1 — Sinh viên khiếu nại (US1)

1. Đăng nhập STUDENT → `/student/profile` → mục "Điểm hành vi & nề nếp" → danh sách vi phạm.
2. Bấm **Khiếu nại** trên một vi phạm → để trống lý do → bị chặn.
3. Nhập lý do → gửi → vi phạm hiển thị trạng thái **Đang khiếu nại**; admin nhận thông báo.

## Kịch bản 2 — Admin duyệt khiếu nại (US2)

1. Đăng nhập ADMIN → `/admin/violations` → lọc **Đang chờ duyệt**.
2. **Chấp nhận** một khiếu nại → vi phạm chuyển **Đã thu hồi**, điểm hành vi sinh viên **tăng lại** đúng số đã trừ (≤100).
3. Với khiếu nại khác, **Từ chối** kèm ghi chú → chuyển **Khiếu nại bị từ chối**, điểm giữ nguyên.
4. STUDENT mở lại `/student/profile` thấy kết quả + ghi chú của admin.

## Kịch bản 3 — Admin thu hồi trực tiếp (US3)

1. ADMIN ở `/admin/violations` chọn một vi phạm ghi nhầm → bấm **Thu hồi**.
2. Vi phạm chuyển **Đã thu hồi**, điểm được hoàn.
3. Bấm thu hồi lại (nếu còn nút) → không cộng điểm lần hai.

## Kịch bản 4 — Hàng đợi & tổng quan (US4)

1. `/admin/violations` hiển thị tất cả vi phạm kèm trạng thái; bộ lọc theo trạng thái hoạt động đúng.

## Kiểm thử

```bash
cd src/backend && npm run build && npm run test -- violations
cd src/frontend && npm run build
```
