# Kịch bản Video Demo — Nhật ký xử lý bảo trì & Từ chối có lý do

> Dùng cho PA3 phần E. Tổng thời lượng đề xuất: **6–7 phút**.
> Tính năng thuộc hệ thống Quản lý Ký túc xá. Stack: **Next.js (FE) · NestJS (BE) · MongoDB (DB)**.
> Ngôn ngữ thuyết minh: tiếng Việt (có thể chuyển sang tiếng Anh nếu cần — xem cuối file).

---

## 0. Chuẩn bị trước khi quay (không quay phần này)

**Tài khoản cần có (3 vai trò):**
- 1 `ADMIN` — để giao việc.
- 1 `MAINTENANCE_STAFF` — nhân vật chính xử lý bảo trì.
- 1 `STUDENT` (đã được xếp phòng) — để tạo yêu cầu và nhận phản hồi.

**Dữ liệu mẫu:** đăng nhập STUDENT tạo sẵn **2–3 yêu cầu bảo trì** (một cái để từ chối, một cái để hoàn thành). Để trạng thái ban đầu là "Chờ tiếp nhận".

**Kỹ thuật quay:**
- Chạy: backend `npm run start:dev` (cổng 3001), frontend `npm run dev`.
- Mở sẵn 3 cửa sổ trình duyệt / 3 profile / hoặc 1 cửa sổ thường + 2 cửa sổ ẩn danh để đăng nhập 3 vai trò song song, chuyển nhanh khi quay.
- Mở sẵn: VS Code (thư mục `specs/001-maintenance-resolution-log/`) và MongoDB Compass/Atlas (collection `maintenances`).
- Độ phân giải 1080p, phóng to chữ trình duyệt ~110–125% cho dễ đọc.
- Nên quay từng phân đoạn rồi ghép, hoặc quay một mạch nếu đã tập.

---

## 1. Mở đầu — Giới thiệu (0:00 – 0:40)

**Màn hình:** Trang chủ hệ thống hoặc slide tiêu đề.

**Lời thoại:**
> "Xin chào thầy/cô và các bạn. Nhóm [số nhóm] xin trình bày phần E của PA3 — triển khai một nhóm chức năng end-to-end bằng quy trình Spec-Driven với Spec Kit.
> Hệ thống của nhóm là **Quản lý Ký túc xá**, gồm frontend Next.js, backend NestJS và cơ sở dữ liệu MongoDB.
> Nhóm chức năng chúng em chọn cải tiến là **quy trình xử lý yêu cầu bảo trì của nhân viên kỹ thuật** — cụ thể là *Từ chối yêu cầu kèm lý do* và *Nhật ký xử lý*."

---

## 2. Quy trình Spec Kit — điểm cốt lõi được chấm (0:40 – 2:10)

**Màn hình:** VS Code, mở lần lượt các file trong `specs/001-maintenance-resolution-log/`.

**Lời thoại (vừa nói vừa cuộn file):**
> "Trước khi viết code, chúng em dùng Spec Kit dẫn dắt toàn bộ quy trình.
>
> **Bước 1 — `specify`:** file `spec.md` mô tả tính năng dưới dạng 3 user story theo độ ưu tiên: P1 — từ chối có lý do, P2 — ghi nội dung đã xử lý, P3 — nhật ký trạng thái; kèm 9 yêu cầu chức năng FR-001 đến FR-009 và các tiêu chí đo được.
>
> **Bước 2 — `plan`:** file `plan.md` chốt kiến trúc, cùng `research.md` ghi các quyết định kỹ thuật, `data-model.md` mô tả schema, và `contracts/maintenance-api.md` định nghĩa hợp đồng API.
>
> **Bước 3 — `tasks`:** file `tasks.md` chia nhỏ thành 17 task theo từng user story, có thứ tự phụ thuộc rõ ràng. Các ô đã tick là phần đã hoàn thành.
>
> **Bước 4 — `implement`:** chúng em lập trình đúng theo các task này. Nhờ vậy code bám sát đặc tả thay vì làm tùy hứng."

**Gợi ý hình:** lướt nhanh `spec.md` (phần user stories + FR), `plan.md` (Technical Context), `data-model.md` (bảng field mới **in đậm**), `tasks.md` (các checkbox `[x]`).

---

## 3. Bối cảnh vấn đề (2:10 – 2:40)

**Màn hình:** Đăng nhập STAFF, mở trang `/staff` (danh sách việc được giao).

**Lời thoại:**
> "Đây là màn hình công việc của nhân viên bảo trì đã có sẵn. Vấn đề trước đây: nhân viên chỉ có thể *Tiếp nhận* và *Hoàn thành*. Trạng thái 'Từ chối' đã tồn tại trong hệ thống nhưng nhân viên **không có cách nào từ chối**, và khi một yêu cầu bị đóng, sinh viên **không biết lý do**. Đây chính là khoảng trống mà nhóm khắc phục."

---

## 4. Kịch bản 1 — Từ chối kèm lý do (US1, P1) (2:40 – 4:10)

**4a. Admin giao việc**
- **Màn hình:** cửa sổ ADMIN → `/admin/maintenance`.
- **Hành động:** chọn một yêu cầu, ở ô "Phụ trách" giao cho nhân viên bảo trì.
- **Lời thoại:** "Đầu tiên, ban quản lý giao yêu cầu này cho nhân viên bảo trì."

**4b. Staff từ chối — kiểm chứng validation**
- **Màn hình:** chuyển sang cửa sổ STAFF → `/staff`, danh sách tự cập nhật realtime.
- **Hành động:** bấm **"✕ Từ chối"** → modal hiện ra → **để trống lý do** → bấm xác nhận (nút đang bị khóa / báo lỗi).
- **Lời thoại:** "Nhân viên bấm Từ chối. Lưu ý: nếu để trống lý do, hệ thống **không cho phép** — đây là ràng buộc bắt buộc, được kiểm tra cả ở frontend lẫn backend."
- **Hành động:** nhập lý do, ví dụ *"Báo sai, thiết bị vẫn hoạt động bình thường"* → xác nhận.
- **Lời thoại:** "Bây giờ nhập lý do rồi xác nhận. Yêu cầu chuyển sang trạng thái *Bị từ chối*."

**4c. Sinh viên nhận phản hồi**
- **Màn hình:** cửa sổ STUDENT → chuông thông báo (nếu để realtime) rồi mở `/student/maintenance`.
- **Hành động:** chỉ vào hộp đỏ "Lý do bị từ chối".
- **Lời thoại:** "Phía sinh viên nhận được thông báo realtime, và trên yêu cầu hiển thị rõ **lý do bị từ chối**. Sinh viên không còn phải hỏi ban quản lý nữa."

---

## 5. Kịch bản 2 — Hoàn thành kèm nội dung xử lý (US2, P2) (4:10 – 5:10)

- **Màn hình:** cửa sổ STAFF → `/staff`, chọn một yêu cầu khác.
- **Hành động:** bấm **"🔧 Tiếp nhận sửa chữa"** (chuyển *Đang sửa chữa*) → sau đó bấm **"✓ Hoàn thành"** → modal → nhập nội dung, ví dụ *"Đã thay bóng đèn LED 9W, kiểm tra lại công tắc"* → xác nhận.
- **Lời thoại:** "Với một yêu cầu hợp lệ, nhân viên tiếp nhận rồi đánh dấu hoàn thành. Lần này có thêm ô ghi lại **nội dung đã xử lý** để minh bạch công việc."
- **Màn hình:** cửa sổ STUDENT → `/student/maintenance`, yêu cầu vừa hoàn thành.
- **Hành động:** chỉ vào hộp xanh "Kỹ thuật viên đã xử lý", rồi chấm sao đánh giá.
- **Lời thoại:** "Sinh viên thấy chính xác kỹ thuật viên đã làm gì, và có thể đánh giá chất lượng bằng sao."

---

## 6. Kịch bản 3 — Nhật ký xử lý (US3, P3) (5:10 – 5:45)

- **Màn hình:** trên yêu cầu vừa hoàn thành (student hoặc staff), mở mục **"Nhật ký xử lý"**.
- **Hành động:** bung timeline các mốc.
- **Lời thoại:** "Cuối cùng, mỗi yêu cầu có một **nhật ký** ghi lại toàn bộ các mốc đổi trạng thái — ai thực hiện, lúc nào, kèm ghi chú. Ví dụ ở đây: Đang sửa chữa, rồi Hoàn thành với nội dung xử lý tương ứng."

---

## 7. Lưu trữ dữ liệu — chứng minh persistence (5:45 – 6:15)

**Màn hình:** MongoDB Compass / Atlas → collection `maintenances` → mở document vừa thao tác.

**Lời thoại:**
> "Để chứng minh dữ liệu được lưu bền vững chứ không chỉ ở giao diện, đây là document tương ứng trong MongoDB. Có thể thấy rõ ba trường mới mà nhóm bổ sung: **`rejectionReason`** — lý do từ chối, **`resolutionNote`** — nội dung đã xử lý, và mảng **`statusHistory`** — nhật ký trạng thái. Đây chính là lớp *data persistence* của tính năng."

---

## 8. Kết — Tổng kết end-to-end (6:15 – 6:45)

**Màn hình:** có thể split: terminal hiển thị `npm run build` PASS + `npm run test -- maintenance` PASS.

**Lời thoại:**
> "Tóm lại, nhóm đã triển khai trọn vẹn một nhóm chức năng theo đúng quy trình Spec Kit — từ đặc tả, kế hoạch, chia task đến lập trình — chạy thông suốt cả ba tầng: giao diện Next.js, API NestJS và lưu trữ MongoDB. Backend build và test đều pass. Em xin cảm ơn thầy/cô đã theo dõi."

---

## Bảng tóm tắt shot list

| # | Thời lượng | Màn hình | Nội dung chính |
|---|-----------|----------|----------------|
| 1 | 0:00–0:40 | Slide/Home | Giới thiệu nhóm, tính năng, stack |
| 2 | 0:40–2:10 | VS Code / specs | Quy trình Spec Kit: spec→plan→tasks→implement |
| 3 | 2:10–2:40 | /staff | Bối cảnh vấn đề (thiếu từ chối & lý do) |
| 4 | 2:40–4:10 | admin→staff→student | US1: từ chối có lý do + validation + student thấy lý do |
| 5 | 4:10–5:10 | staff→student | US2: hoàn thành + nội dung xử lý + đánh giá |
| 6 | 5:10–5:45 | student/staff | US3: nhật ký trạng thái |
| 7 | 5:45–6:15 | MongoDB | Chứng minh lưu trữ 3 trường mới |
| 8 | 6:15–6:45 | Terminal | Build/test pass + tổng kết |

---

## Mẹo ghi điểm theo rubric PA3-E

- **"Follows the Spec Kit workflow"** → dành hẳn phân đoạn 2 để lướt các file spec/plan/tasks; nói rõ tên 4 bước.
- **"Functional end-to-end (UI + API/logic + data persistence)"** → phân đoạn 7 mở MongoDB là bằng chứng mạnh cho lớp DB; phân đoạn 4–6 là UI + logic.
- **"Clearly shows working features"** → luôn cho thấy *kết quả* sau mỗi thao tác (trạng thái đổi, hộp lý do hiện ra, thông báo realtime).
- **Nhấn điểm khác biệt so với "code chay"**: quay cảnh validation chặn khi thiếu lý do — cho thấy đặc tả (FR-002) được thực thi đúng.
- Giữ con trỏ chuột chậm, chỉ rõ vùng đang nói; tránh nhảy màn hình quá nhanh.

---

## Phiên bản tiếng Anh (nếu cần)

PA3 yêu cầu *tài liệu* bằng tiếng Anh; phần thuyết minh video không nêu rõ. Nếu muốn chắc điểm "English writing", có thể thuyết minh bằng tiếng Anh — nhóm cứ báo, mình sẽ dịch toàn bộ lời thoại ở trên sang tiếng Anh giữ nguyên mốc thời gian.
