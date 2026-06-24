# HIẾN PHÁP DỰ ÁN (PROJECT CONSTITUTION) - DORMIFY

Tài liệu này định nghĩa các nguyên tắc cốt lõi, tiêu chuẩn coding và quy ước kiến trúc cho dự án Dormify. Mọi lập trình viên và AI Assistant (Gemini, Claude, Cursor...) BẮT BUỘC phải tuân thủ các quy tắc này trong mọi dòng code được sinh ra.

## 1. Nguyên tắc cốt lõi (Core Principles)
* **Tuyệt đối không phá vỡ code đang chạy (No breaking changes):** Khi thêm tính năng mới, phải đảm bảo không làm hỏng các tính năng đã được đánh dấu hoàn thành `[✅]` trong `spec.md`.
* **Không dùng dữ liệu giả (No hardcoded mock data) trừ khi được yêu cầu:** Code sinh ra phải sẵn sàng tích hợp API thực tế.
* **Bảo mật là ưu tiên (Security First):** Mọi API endpoint của Backend đều phải có `@UseGuards(JwtAuthGuard)` và `@Roles(...)` phù hợp. Frontend phải bọc component bằng `<RoleGuard>`.

## 2. Tiêu chuẩn Giao diện (Frontend - Next.js App Router)
* **UI Framework:** Chỉ sử dụng Tailwind CSS cho việc styling. Bắt đầu từ phase này, **TUYỆT ĐỐI KHÔNG dùng Inline Styles** (`style={{...}}`) hoặc thẻ `<style>` trong file `.tsx`.
* **Cấu trúc Layout:** Sidebar và Topbar (chứa Notification) phải được đặt trong `app/admin/layout.tsx` và `app/student/layout.tsx`. Các file `page.tsx` chỉ chứa nội dung view.
* **Chuyển trang (Routing):** Bắt buộc sử dụng `useRouter()` từ `next/navigation` hoặc thẻ `<Link>` của Next.js. Tuyệt đối không dùng `window.location.href`.
* **Icons & Biểu đồ:** Chỉ sử dụng `react-icons` và `recharts`.
* **Ngôn ngữ UI:** Mọi văn bản hiển thị cho người dùng BẮT BUỘC phải là Tiếng Việt có dấu, văn phong lịch sự, chuyên nghiệp.

## 3. Tiêu chuẩn Máy chủ (Backend - NestJS)
* **Kiến trúc (Architecture):** Tuân thủ chặt chẽ mô hình Module -> Controller -> Service. Không viết logic database vào Controller.
* **Cơ sở dữ liệu (Database):** Sử dụng Mongoose. Luôn định nghĩa Schema rõ ràng và sử dụng Type cho Document.
* **Real-time:** Mọi tính năng cần thông báo thời gian thực phải gọi qua `NotificationsModule` và `NotificationsGateway` (Socket.IO).
* **Naming Convention:** * Biến, hàm: `camelCase`.
  * Class, Decorator: `PascalCase`.
  * URL API: `kebab-case` (ví dụ: `/api/users/student-list`).

## 4. Quản lý Môi trường & API (Environment Variables)
* Tuyệt đối **KHÔNG HARDCODE** các đường dẫn API như `http://localhost:3001` trong Frontend.
* Bắt buộc sử dụng biến môi trường: `${process.env.NEXT_PUBLIC_API_URL}` để gọi API.

## 5. Quy tắc cho AI (AI Directives)
* Trước khi đưa ra giải pháp, hãy đọc `spec.md` để biết tính năng thuộc Module nào.
* Trả về **Toàn bộ code (Full code)** của file nếu file đó cần thay đổi cấu trúc lớn, để tránh lỗi Copy/Paste bị thiếu ngoặc.
* Luôn sử dụng TypeScript strict mode (không dùng type `any` trừ trường hợp bất khả kháng).