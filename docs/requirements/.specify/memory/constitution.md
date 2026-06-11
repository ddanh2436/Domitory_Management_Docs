# Cấu trúc Kiến trúc & Nguyên tắc Lập trình (Constitution)
Dự án: Hệ thống Quản lý Ký túc xá (Dormitory Management System)

## 1. Frontend (Next.js)
- Framework: Bắt buộc sử dụng Next.js (App Router) phiên bản mới nhất.
- Styling: Bắt buộc sử dụng Tailwind CSS cho mọi UI component. Không dùng file CSS rời.
- Ngôn ngữ: TypeScript (Strict mode).
- UI/UX: Giao diện hiện đại, tối giản (màu chủ đạo: Blue/Slate). Bắt buộc Responsive cho cả Mobile và Desktop.
- Quản lý trạng thái: Sử dụng React Hooks (useState, useEffect, useContext).

## 2. Backend (Nest.js)
- Framework: Bắt buộc sử dụng Nest.js.
- Kiến trúc: Tuân thủ nghiêm ngặt mô hình Module -> Controller -> Service. Sử dụng Dependency Injection.
- Cơ sở dữ liệu: Sử dụng Mongoose (MongoDB).
- Ngôn ngữ: TypeScript (Strict mode).
- Bảo mật: Mọi API ngoại trừ `/login` đều phải được bảo vệ bằng JWT Auth Guard. Phân quyền chặt chẽ dựa trên Roles (Admin, Student, Maintenance).

## 3. Quy tắc chung cho AI
- KHÔNG sử dụng dữ liệu giả (mock data) cứng trong component, mọi dữ liệu phải được fetch qua API.
- Tên biến, tên hàm sử dụng tiếng Anh (camelCase).
- Bắt buộc xử lý lỗi (Error Handling) và trả về thông báo rõ ràng cho UI.