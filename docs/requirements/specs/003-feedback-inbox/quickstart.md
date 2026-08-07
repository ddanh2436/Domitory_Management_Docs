# Quickstart / Kịch bản demo

## Chạy hệ thống

```bash
# Backend (cổng 3001)
cd domitory_management_backend
npm install
npm run start:dev

# Frontend (Next.js dev, cổng 3000)
cd domitory_management_frontend
npm install
npm run dev
```

Cần `domitory_management_backend/.env` có `MONGO_URI`, `JWT_SECRET` (đã có sẵn trong repo).

## Dữ liệu cần có

- 1 tài khoản `STUDENT`.
- 1 tài khoản `ADMIN` hoặc `DORMITORY_MANAGER`.

## Kịch bản 1 — Gửi và theo dõi (US1)

1. Đăng nhập STUDENT → menu "Góp ý & Khiếu nại" → chọn loại "Khiếu nại", danh mục "Thái độ
   nhân viên", nhập nội dung, gửi.
2. Kiểm tra: mục vừa gửi xuất hiện ngay trong "Lịch sử của tôi" với trạng thái "Chờ xử lý".
3. Thử để trống nội dung và gửi → hệ thống chặn, báo cần nhập nội dung.

## Kịch bản 2 — Ban quản lý xem, lọc và phản hồi (US2)

1. Đăng nhập ADMIN/DORMITORY_MANAGER → nhận được thông báo chuông (realtime) báo có góp ý
   mới → menu "Góp ý & Khiếu nại".
2. Lọc theo tab "Chờ xử lý" → thấy đúng mục vừa gửi ở kịch bản 1.
3. Mở mục, thử để trống phản hồi và xác nhận → hệ thống chặn.
4. Nhập phản hồi hợp lệ, chọn "Đã xử lý" → mục chuyển trạng thái, biến mất khỏi tab "Chờ xử
   lý".
5. Đăng nhập lại STUDENT → "Lịch sử của tôi": thấy phản hồi hiển thị kèm mục, có thông báo
   chuông realtime báo đã được phản hồi.

## Kiểm tra build

```bash
cd domitory_management_backend && npm run build
cd domitory_management_frontend && npx tsc --noEmit
```
