# Kịch bản Video Demo — Hòm thư góp ý & khiếu nại

> Dùng cho PA3 phần E. Tổng thời lượng đề xuất: **5:30–6:00 phút**.
> Tính năng thuộc hệ thống Quản lý Ký túc xá (Dormify). Stack: **Next.js (FE) · NestJS (BE) · MongoDB (DB)**.
> Ngôn ngữ thuyết minh: tiếng Việt (có thể chuyển sang tiếng Anh — xem cuối file).

---

## 0. Chuẩn bị trước khi quay (không quay phần này)

**Tài khoản cần có (2 vai trò):**
- 1 `STUDENT` — người gửi góp ý/khiếu nại.
- 1 `ADMIN` hoặc `DORMITORY_MANAGER` — người xem và phản hồi.

Cách nhanh nhất: chạy `node scripts/e2e-seed.js` trong `domitory_management_backend` — tạo
sẵn `e2e.student@test.local` và `e2e.admin@test.local`, mật khẩu `E2Etest123`. **Lưu ý**:
chạy `node scripts/e2e-cleanup.js` sau khi quay xong để dọn dữ liệu test (script này đã được
cập nhật để dọn cả collection `feedbacks`).

**Dữ liệu mẫu:** không cần tạo trước — phần hay nhất của demo chính là tạo một góp ý mới
ngay trong lúc quay. Có thể tạo sẵn 1 mục cũ (không phản hồi) nếu muốn cảnh "danh sách có sẵn
dữ liệu" ở bước 4, nhưng không bắt buộc.

**Kỹ thuật quay:**
- Chạy: backend `npm run start:dev` (cổng 3001), frontend `npm run dev` (cổng 3000).
- Mở sẵn 2 cửa sổ trình duyệt (1 thường + 1 ẩn danh, hoặc 2 profile khác nhau) để đăng nhập
  song song STUDENT và ADMIN, chuyển nhanh khi quay — quan trọng để thấy **thông báo realtime**
  chạy giữa hai bên.
- Mở sẵn: VS Code (thư mục `specs/002-feedback-inbox/`) và MongoDB Compass/Atlas (collection
  `feedbacks`).
- Độ phân giải 1080p, phóng to chữ trình duyệt ~110–125% cho dễ đọc.

---

## 1. Mở đầu — Giới thiệu (0:00 – 0:35)

**Màn hình:** Trang chủ hệ thống hoặc slide tiêu đề.

**Lời thoại:**
> "Xin chào thầy/cô và các bạn. Nhóm [số nhóm] xin trình bày phần E của PA3 — triển khai một
> nhóm chức năng end-to-end bằng quy trình Spec-Driven với Spec Kit.
> Hệ thống của nhóm là **Dormify — Quản lý Ký túc xá**, gồm frontend Next.js, backend NestJS
> và cơ sở dữ liệu MongoDB.
> Nhóm chức năng chúng em chọn làm mới hoàn toàn là **Hòm thư góp ý & khiếu nại** — kênh để
> sinh viên chủ động phản ánh với ban quản lý, thay vì chỉ nhận thông báo một chiều như
> trước đây."

---

## 2. Quy trình Spec Kit — điểm cốt lõi được chấm (0:35 – 2:00)

**Màn hình:** VS Code, mở lần lượt các file trong `specs/002-feedback-inbox/`.

**Lời thoại (vừa nói vừa cuộn file):**
> "Trước khi viết code, chúng em dùng Spec Kit dẫn dắt toàn bộ quy trình.
>
> **Bước 1 — `specify`:** file `spec.md` mô tả tính năng dưới dạng 2 user story theo độ ưu
> tiên — P1: sinh viên gửi và theo dõi góp ý/khiếu nại; P2: ban quản lý xem, lọc và phản
> hồi — kèm 10 yêu cầu chức năng FR-001 đến FR-010, và cả phần **quyết định đã chốt** để trả
> lời những điểm còn để ngỏ trong use-case gốc, ví dụ: không hỗ trợ ẩn danh, dùng chung một
> schema cho cả góp ý lẫn khiếu nại phân biệt bằng trường `type`.
>
> **Bước 2 — `plan`:** file `plan.md` chốt kiến trúc — module `feedback` hoàn toàn mới, không
> đụng vào module nào đã có — cùng `data-model.md` mô tả schema và bất biến dữ liệu, và
> `contracts/feedback-api.md` định nghĩa hợp đồng 4 API.
>
> **Bước 3 — `tasks`:** file `tasks.md` chia thành 16 task theo từng user story, có thứ tự phụ
> thuộc rõ ràng. Các ô đã tick là phần đã hoàn thành.
>
> **Bước 4 — `implement`:** chúng em lập trình đúng theo các task này, sau đó viết một kịch
> bản kiểm thử tự động chạy qua API thật để xác nhận toàn bộ luồng trước khi quay video —
> phần này em sẽ chỉ lại ở cuối."

**Gợi ý hình:** lướt nhanh `spec.md` (phần user stories + FR + mục "Quyết định đã chốt"),
`data-model.md` (bảng field + vòng đời trạng thái), `contracts/feedback-api.md`, `tasks.md`
(các checkbox `[x]`).

---

## 3. Bối cảnh vấn đề (2:00 – 2:25)

**Màn hình:** Đăng nhập STUDENT, mở `/student` (dashboard), lướt qua sidebar.

**Lời thoại:**
> "Trước tính năng này, hệ thống chỉ có thông báo **một chiều** — ban quản lý gửi thông báo
> chung tới sinh viên qua mục Thông báo. Sinh viên **không có kênh nào** để chủ động phản
> ánh về cơ sở vật chất, thái độ nhân viên, hoá đơn, hay góp ý cải thiện đời sống ký túc xá.
> Đây chính là khoảng trống mà nhóm khắc phục — mục backlog số 6 trong tài liệu đặc tả gốc."

---

## 4. Kịch bản 1 — Sinh viên gửi và theo dõi (US1, P1) (2:25 – 3:40)

**Màn hình:** cửa sổ STUDENT → sidebar → **"Góp ý & Khiếu nại"** (`/student/feedback`).

**4a. Kiểm chứng validation**
- **Hành động:** bấm **"Gửi góp ý / khiếu nại"** → mở form → để trống nội dung → bấm **Gửi**.
- **Lời thoại:** "Nếu để trống nội dung, hệ thống chặn ngay — kể cả nếu chỉ gõ toàn khoảng
  trắng, chặn vẫn hoạt động vì phần backend tự kiểm tra sau khi loại bỏ khoảng trắng."

**4b. Gửi hợp lệ**
- **Hành động:** chọn loại **"Khiếu nại"**, danh mục **"Thái độ nhân viên"**, nhập nội dung ví
  dụ *"Nhân viên bảo vệ nói chuyện thiếu tôn trọng lúc 22h hôm qua."* → bấm **Gửi**.
- **Lời thoại:** "Bây giờ gửi một khiếu nại hợp lệ. Mục vừa gửi xuất hiện **ngay lập tức**
  trong Lịch sử của tôi, với trạng thái *Chờ xử lý* — không cần tải lại trang."
- **Hình:** chỉ vào thẻ vừa xuất hiện: nhãn loại, danh mục, badge "Chờ xử lý".

---

## 5. Kịch bản 2 — Ban quản lý xem, lọc và phản hồi (US2, P2) (3:40 – 5:00)

**5a. Nhận thông báo realtime**
- **Màn hình:** chuyển sang cửa sổ ADMIN → chỉ vào chuông thông báo (số đỏ vừa tăng) và badge
  trên mục sidebar "Góp ý và khiếu nại".
- **Lời thoại:** "Phía ban quản lý nhận thông báo realtime ngay khi sinh viên gửi — không
  cần refresh."

**5b. Xem & lọc**
- **Hành động:** mở **"Góp ý và khiếu nại"** (`/admin/feedback`) → chỉ vào 4 số thống kê →
  tab đang chọn sẵn "Chờ xử lý" đã lọc đúng mục vừa gửi → thử đổi bộ lọc loại (Chỉ khiếu
  nại/Chỉ góp ý).
- **Lời thoại:** "Ban quản lý thấy toàn bộ góp ý/khiếu nại, lọc theo trạng thái và theo loại."

**5c. Kiểm chứng validation + phản hồi**
- **Hành động:** bấm **"Phản hồi"** trên mục vừa gửi → modal hiện nội dung gốc → để trống ô
  phản hồi, bấm xác nhận (bị chặn) → nhập phản hồi, ví dụ *"Ban quản lý đã nhắc nhở nhân viên
  liên quan, cảm ơn phản ánh của bạn."* → chọn **"✓ Đánh dấu đã xử lý"** → xác nhận.
- **Lời thoại:** "Giống bên sinh viên, phản hồi rỗng cũng bị chặn — bắt buộc phải viết nội
  dung trước khi đóng một mục. Sau khi xác nhận, mục chuyển trạng thái *Đã xử lý* và biến mất
  khỏi tab Chờ xử lý."

**5d. Sinh viên thấy kết quả**
- **Màn hình:** chuyển lại cửa sổ STUDENT → chuông thông báo báo có tin mới → mở lại
  `/student/feedback`.
- **Hành động:** chỉ vào hộp xanh "Phản hồi từ ban quản lý" ngay dưới nội dung đã gửi.
- **Lời thoại:** "Sinh viên nhận thông báo realtime và thấy đúng nội dung phản hồi — khép kín
  vòng lặp hai chiều mà trước đây hệ thống chưa có."

---

## 6. Lưu trữ dữ liệu — chứng minh persistence (5:00 – 5:25)

**Màn hình:** MongoDB Compass/Atlas → collection `feedbacks` → mở document vừa thao tác.

**Lời thoại:**
> "Để chứng minh dữ liệu được lưu bền vững chứ không chỉ ở giao diện, đây là document tương
> ứng trong MongoDB — đủ các trường `type`, `category`, `message`, `status: RESOLVED`, và ba
> trường được set cùng lúc khi phản hồi: `response`, `respondedBy`, `respondedAt`. Đây chính
> là lớp *data persistence* của tính năng."

---

## 7. Kết — Tổng kết end-to-end (5:25 – 5:55)

**Màn hình:** terminal hiển thị `npm run build` PASS (backend) — có thể nói thêm phần kiểm
thử tự động.

**Lời thoại:**
> "Tóm lại, nhóm đã triển khai trọn vẹn một nhóm chức năng hoàn toàn mới theo đúng quy trình
> Spec Kit — từ đặc tả, kế hoạch, chia task đến lập trình — chạy thông suốt cả ba tầng: giao
> diện Next.js, API NestJS và lưu trữ MongoDB. Trước khi quay video, nhóm còn viết một kịch
> bản kiểm thử chạy qua API thật để xác nhận toàn bộ luồng — quá trình đó thực tế đã phát
> hiện và giúp nhóm sửa 2 lỗi validate dữ liệu trắng/khoảng trắng trước khi bàn giao. Em xin
> cảm ơn thầy/cô đã theo dõi."

---

## Bảng tóm tắt shot list

| # | Thời lượng | Màn hình | Nội dung chính |
|---|-----------|----------|----------------|
| 1 | 0:00–0:35 | Slide/Home | Giới thiệu nhóm, tính năng, stack |
| 2 | 0:35–2:00 | VS Code / specs | Quy trình Spec Kit: spec→plan→tasks→implement |
| 3 | 2:00–2:25 | /student | Bối cảnh vấn đề (chỉ có thông báo 1 chiều) |
| 4 | 2:25–3:40 | /student/feedback | US1: gửi + validation chặn nội dung rỗng + xuất hiện trong lịch sử |
| 5 | 3:40–5:00 | /admin/feedback → /student/feedback | US2: thông báo realtime, lọc, validation chặn phản hồi rỗng, phản hồi, sinh viên thấy kết quả |
| 6 | 5:00–5:25 | MongoDB | Chứng minh lưu trữ (status/response/respondedBy/respondedAt) |
| 7 | 5:25–5:55 | Terminal | Build pass + tổng kết |

---

## Mẹo ghi điểm theo rubric PA3-E

- **"Follows the Spec Kit workflow"** → dành hẳn phân đoạn 2 để lướt `spec.md`/`plan.md`/
  `data-model.md`/`contracts/`/`tasks.md`; nói rõ tên 4 bước specify→plan→tasks→implement.
- **"Functional end-to-end (UI + API/logic + data persistence)"** → phân đoạn 6 mở MongoDB là
  bằng chứng cho lớp DB; phân đoạn 4–5 là UI + logic, có cả 2 chiều (student → admin →
  student).
- **"Clearly shows working features"** → luôn quay cảnh *kết quả* sau mỗi thao tác: trạng
  thái đổi ngay không cần F5, thông báo chuông tăng số, hộp phản hồi hiện ra.
- **Nhấn điểm khác biệt so với "code chay"**: quay cả 2 cảnh validation bị chặn (nội dung
  rỗng ở bước 4a, phản hồi rỗng ở bước 5c) — chứng minh FR-002 và FR-007 trong `spec.md`
  thực sự được thực thi, không chỉ ghi trên giấy.
- Giữ con trỏ chuột chậm, chỉ rõ vùng đang nói; tránh nhảy màn hình quá nhanh.
- Nếu muốn rút ngắn còn ~4:30, có thể gộp bước 4a+5c (chỉ demo validation một lần thay vì cả
  hai chiều) và bỏ bớt phần lọc ở 5b.

---

## Phiên bản tiếng Anh (nếu cần)

PA3 yêu cầu *tài liệu* bằng tiếng Anh; phần thuyết minh video không nêu rõ. Nếu muốn chắc
điểm "English writing", có thể thuyết minh bằng tiếng Anh — nhóm cứ báo, mình sẽ dịch toàn bộ
lời thoại ở trên sang tiếng Anh giữ nguyên mốc thời gian.
