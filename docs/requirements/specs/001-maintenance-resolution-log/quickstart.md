# Quickstart / Kịch bản demo

## Chạy hệ thống

```bash
# Backend (cổng 3001)
cd src/backend
npm install
npm run start:dev

# Frontend (Next.js dev)
cd src/frontend
npm install
npm run dev
```

Cần `src/backend/.env` có `MONGO_URI`, `JWT_SECRET`, và (tùy chọn) Cloudinary cho ảnh.

## Dữ liệu cần có

- 1 tài khoản `STUDENT` đã được xếp phòng.
- 1 tài khoản `MAINTENANCE_STAFF`.
- 1 tài khoản `ADMIN`.
- Ít nhất 1 yêu cầu bảo trì do sinh viên tạo (student → `/student/maintenance` → "Tạo báo cáo mới").

## Kịch bản 1 — Từ chối có lý do (US1)

1. Đăng nhập ADMIN → `/admin/maintenance` → giao yêu cầu cho nhân viên bảo trì.
2. Đăng nhập MAINTENANCE_STAFF → trang `/staff` thấy việc được giao.
3. Bấm **Từ chối** → để trống lý do → xác nhận: hệ thống chặn, báo cần nhập lý do.
4. Nhập lý do (VD "Báo sai, thiết bị vẫn hoạt động") → xác nhận: yêu cầu chuyển **Từ chối**.
5. Đăng nhập lại STUDENT → `/student/maintenance`: thấy nhãn "Bị từ chối" + nội dung lý do,
   và có thông báo realtime kèm lý do.

## Kịch bản 2 — Hoàn thành kèm nội dung xử lý (US2)

1. STAFF chọn một việc, bấm **Tiếp nhận** (PENDING → IN_PROGRESS).
2. Bấm **Hoàn thành** → nhập nội dung đã xử lý (VD "Đã thay bóng đèn LED 9W") → xác nhận.
3. STUDENT xem `/student/maintenance`: thấy nội dung nhân viên đã xử lý cạnh ô chấm sao.

## Kịch bản 3 — Nhật ký trạng thái (US3)

1. Sau khi một yêu cầu trải qua PENDING → IN_PROGRESS → RESOLVED (hoặc REJECTED).
2. Xem chi tiết ở màn student/admin: nhật ký liệt kê từng mốc kèm thời gian, người thực
   hiện và ghi chú.

## Kiểm tra build

```bash
cd src/backend && npm run build && npm run test -- maintenance
cd src/frontend && npm run build
```
