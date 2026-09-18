# SRS – Module Login
## Hệ thống: Perfex CRM (Anh Tester Demo)

| Thuộc tính | Giá trị |
|---|---|
| URL khảo sát | https://crm.anhtester.com/admin/authentication |
| Ngày khảo sát | 2026-09-18 |
| Người thực hiện | duongnt-17 |
| Phiên bản | 1.0 |

---

## 1. Giới thiệu

### 1.1 Mục đích
Tài liệu này đặc tả yêu cầu phần mềm (SRS) cho **module Login** (Đăng nhập) của hệ thống Perfex CRM, được khảo sát thực tế trên môi trường demo tại `https://crm.anhtester.com/admin/authentication`. Tài liệu làm cơ sở cho việc thiết kế test case, thực hiện kiểm thử hồi quy và kiểm thử chức năng cho module này.

### 1.2 Phạm vi
Module Login bao gồm các luồng:
- Đăng nhập bằng Email/Password (trang `/admin/authentication`)
- Ghi nhớ đăng nhập (Remember me)
- Quên mật khẩu (Forgot Password – `/admin/authentication/forgot_password`)
- Đăng xuất (Logout)
- Điều hướng phiên đăng nhập (session guard) giữa trạng thái đã/chưa xác thực

Phạm vi **không bao gồm**: phân quyền chi tiết theo vai trò sau khi đăng nhập, các module nghiệp vụ khác của CRM (Customers, Projects, Invoices...), chức năng đặt lại mật khẩu qua email (do môi trường demo không có SMTP để xác nhận toàn luồng).

### 1.3 Đối tượng sử dụng tài liệu
Tester, BA, Dev tham gia phát triển/kiểm thử module Login.

### 1.4 Định nghĩa, từ viết tắt
| Từ viết tắt | Ý nghĩa |
|---|---|
| SRS | Software Requirements Specification |
| CRM | Customer Relationship Management |
| FR | Functional Requirement |
| NFR | Non-Functional Requirement |

---

## 2. Mô tả tổng quan

### 2.1 Bối cảnh
Trang đăng nhập là điểm vào (entry point) duy nhất cho khu vực quản trị (`/admin`) của Perfex CRM. Người dùng chưa xác thực khi truy cập bất kỳ URL nào trong khu vực `/admin` sẽ được điều hướng về trang Login.

### 2.2 Người dùng mục tiêu
- **Admin / Staff**: đăng nhập bằng Email và Password được cấp để truy cập hệ thống quản trị CRM.

### 2.3 Giao diện tổng quan
Trang Login gồm:
- Logo hệ thống + slogan ("Automation Testing")
- Tiêu đề "Login"
- Form gồm: ô nhập **Email Address**, ô nhập **Password**, checkbox **Remember me**, nút **Login**, link **Forgot Password?**

---

## 3. Yêu cầu chức năng (Functional Requirements)

### FR-01 – Đăng nhập hệ thống

**Mô tả:** Cho phép người dùng nhập Email và Password hợp lệ để truy cập vào Dashboard quản trị.

**Đầu vào:**
| Trường | Loại | Bắt buộc | Ghi chú |
|---|---|---|---|
| Email Address | text (type=email) | Có | Không giới hạn định dạng hiển thị rõ trên UI ngoài kiểu `email` của input |
| Password | password | Có | Ẩn ký tự khi nhập |
| Remember me | checkbox | Không | Mặc định unchecked |

**Xử lý:**
1. Khi người dùng bấm **Login**, hệ thống kiểm tra các trường bắt buộc.
2. Nếu thiếu trường bắt buộc → hiển thị thông báo lỗi tương ứng, không submit.
3. Nếu đủ trường → hệ thống kiểm tra Email/Password với dữ liệu tài khoản.
4. Nếu sai Email hoặc Password → hiển thị thông báo lỗi chung, không cho biết cụ thể sai ở trường nào (nhằm hạn chế dò tài khoản).
5. Nếu đúng → tạo session đăng nhập, điều hướng người dùng đến trang **Dashboard** (`/admin` hoặc trang trước đó nếu có yêu cầu truy cập bị chặn).

**Đầu ra / Kết quả quan sát thực tế:**
| Trường hợp | Kết quả quan sát |
|---|---|
| Bỏ trống cả 2 trường, bấm Login | Hiển thị 2 thông báo: *"The Password field is required."* và *"The Email Address field is required."* |
| Nhập Email/Password không đúng | Hiển thị thông báo: *"Invalid email or password"* |
| Nhập đúng Email/Password (ví dụ: `admin@example.com` / `123456`) | Đăng nhập thành công, chuyển hướng sang trang **Dashboard**, hiển thị menu điều hướng (Customers, Projects, Tasks, Contracts, Sales, Subscriptions, Expenses, Support, Leads, Estimate Request, Knowledge Base, Utilities, Reports) và khối tổng quan (Invoice overview, Estimate overview, Proposal overview) |

**Quy tắc nghiệp vụ:**
- BR-01: Cả hai trường Email và Password đều bắt buộc.
- BR-02: Thông báo lỗi sai thông tin đăng nhập không được tiết lộ trường nào sai (email hay password) — chỉ hiển thị "Invalid email or password".
- BR-03: Người dùng **đã đăng nhập** truy cập lại trang Login (`/admin/authentication`) sẽ tự động được điều hướng đến Dashboard, không hiển thị lại form Login.

---

### FR-02 – Ghi nhớ đăng nhập (Remember me)

**Mô tả:** Checkbox cho phép hệ thống lưu phiên đăng nhập lâu hơn giữa các lần truy cập.

**Đầu vào:** Checkbox "Remember me" (checked/unchecked).

**Xử lý:** Khi checked và đăng nhập thành công, hệ thống lưu thông tin ghi nhớ đăng nhập (cookie) để duy trì phiên trong thời gian dài hơn so với khi không tick.

**Quy tắc nghiệp vụ:**
- BR-04: Mặc định checkbox ở trạng thái unchecked.
- BR-05: Việc tick/không tick không phải là điều kiện bắt buộc để đăng nhập thành công.

> Ghi chú kiểm thử: cần kiểm tra thời gian sống của session/cookie khi có và không có "Remember me" (không thể xác minh đầy đủ qua giao diện, cần kiểm tra cookie/response header).

---

### FR-03 – Quên mật khẩu (Forgot Password)

**Mô tả:** Cho phép người dùng yêu cầu đặt lại mật khẩu thông qua email đã đăng ký.

**Đường dẫn:** `/admin/authentication/forgot_password`, truy cập qua link **"Forgot Password?"** trên trang Login.

**Đầu vào:**
| Trường | Loại | Bắt buộc |
|---|---|---|
| Email Address | text (type=email) | Có |

**Xử lý & Đầu ra quan sát thực tế:**
| Trường hợp | Kết quả quan sát |
|---|---|
| Bỏ trống Email, bấm Confirm | Hiển thị thông báo: *"Email not found"* (không có validate "required" riêng cho trường trống ở form này) |
| Nhập email hợp lệ đã tồn tại trong hệ thống (`admin@example.com`) | Hệ thống chấp nhận yêu cầu nhưng trả về *"Error setting new password"* trên môi trường demo (do máy chủ demo không cấu hình gửi email/SMTP) |

**Quy tắc nghiệp vụ:**
- BR-06: Trường Email là bắt buộc trên form Forgot Password.
- BR-07: Nếu email không tồn tại trong hệ thống → thông báo "Email not found".
- BR-08: Người dùng **đã đăng nhập** truy cập trang Forgot Password sẽ được điều hướng thẳng về Dashboard, không hiển thị lại form.

> Rủi ro/khuyến nghị bảo mật: thông báo "Email not found" tiết lộ việc một email có tồn tại trong hệ thống hay không (user enumeration). Nên xem xét thống nhất thông báo trung lập (ví dụ: "Nếu email tồn tại, hướng dẫn đặt lại mật khẩu đã được gửi") để hạn chế dò email hợp lệ.

---

### FR-04 – Đăng xuất (Logout)

**Mô tả:** Cho phép người dùng đã đăng nhập chấm dứt phiên làm việc.

**Vị trí:** Menu avatar (góc trên phải) → **Logout**, cùng nhóm với: My Profile, My Timesheets, Edit Profile, Language.

**Xử lý:** Khi bấm Logout, hệ thống hủy session hiện tại và điều hướng người dùng về trang Login.

**Đầu ra quan sát thực tế:** Sau khi bấm Logout, hệ thống chuyển hướng ngay về trang Login (`/admin/authentication`), form trống, không còn truy cập được Dashboard.

**Quy tắc nghiệp vụ:**
- BR-09: Sau khi logout, các trang thuộc khu vực quản trị phải yêu cầu đăng nhập lại (không cho truy cập bằng session cũ).

---

## 4. Yêu cầu phi chức năng (Non-Functional Requirements)

| Mã | Nội dung |
|---|---|
| NFR-01 | **Bảo mật**: Password phải được ẩn ký tự trên input (type=password); không hiển thị rõ trường nào sai khi đăng nhập thất bại. |
| NFR-02 | **Bảo mật**: Cần giới hạn số lần đăng nhập sai liên tiếp (rate limiting / lockout) để chống brute-force — cần kiểm thử bổ sung vì chưa quan sát được cơ chế này qua UI. |
| NFR-03 | **Usability**: Thông báo lỗi hiển thị rõ, có màu sắc phân biệt (nền đỏ nhạt, chữ đỏ) ngay phía trên form. |
| NFR-04 | **Compatibility**: Trang phải hiển thị và hoạt động đúng trên các trình duyệt phổ biến (Chrome, Edge, Firefox) và tối thiểu ở độ phân giải desktop/tablet. |
| NFR-05 | **Performance**: Thời gian phản hồi sau khi bấm Login/Confirm phải hiển thị kết quả (thành công hoặc lỗi) trong thời gian hợp lý (khảo sát thực tế < 2 giây trên môi trường demo). |
| NFR-06 | **Session management**: Người dùng đã xác thực không được phép xem lại các trang Login/Forgot Password (tự động redirect Dashboard). |

---

## 5. Đặc tả màn hình

### 5.1 Màn hình Login

| # | Thành phần | Loại | Bắt buộc | Mô tả |
|---|---|---|---|---|
| 1 | Email Address | Input (type=email) | Có | Nhập địa chỉ email tài khoản |
| 2 | Password | Input (type=password) | Có | Nhập mật khẩu, ký tự bị ẩn |
| 3 | Remember me | Checkbox | Không | Ghi nhớ đăng nhập |
| 4 | Login | Button (submit) | - | Thực hiện đăng nhập |
| 5 | Forgot Password? | Link | - | Điều hướng sang màn Quên mật khẩu |

### 5.2 Màn hình Forgot Password

| # | Thành phần | Loại | Bắt buộc | Mô tả |
|---|---|---|---|---|
| 1 | Email Address | Input (type=email) | Có | Email cần đặt lại mật khẩu |
| 2 | Confirm | Button (submit) | - | Gửi yêu cầu đặt lại mật khẩu |

---

## 6. Danh mục thông báo hệ thống (Message Catalog)

| Mã tình huống | Thông báo hiển thị | Màn hình |
|---|---|---|
| Thiếu Email | "The Email Address field is required." | Login |
| Thiếu Password | "The Password field is required." | Login |
| Sai Email/Password | "Invalid email or password" | Login |
| Email không tồn tại | "Email not found" | Forgot Password |
| Lỗi đặt mật khẩu mới (môi trường demo, không có SMTP) | "Error setting new password" | Forgot Password |

---

## 7. Use Case chính

### UC-01: Đăng nhập thành công
1. Người dùng truy cập `/admin/authentication`.
2. Nhập Email và Password hợp lệ.
3. Bấm **Login**.
4. Hệ thống xác thực thành công → chuyển hướng đến Dashboard.

**Điều kiện tiên quyết:** Tài khoản tồn tại và đang active.
**Kết quả:** Người dùng ở trang Dashboard, có đầy đủ menu điều hướng.

### UC-02: Đăng nhập thất bại do sai thông tin
1–2. Giống UC-01 nhưng Email/Password không đúng.
3. Bấm **Login**.
4. Hệ thống hiển thị "Invalid email or password", ở lại trang Login.

### UC-03: Bỏ trống thông tin đăng nhập
1. Người dùng bấm **Login** khi chưa nhập gì.
2. Hệ thống hiển thị lỗi bắt buộc cho từng trường trống.

### UC-04: Quên mật khẩu
1. Người dùng bấm **Forgot Password?** từ trang Login.
2. Nhập Email đã đăng ký, bấm **Confirm**.
3. Hệ thống xử lý yêu cầu đặt lại mật khẩu (gửi email hướng dẫn trong môi trường production).

### UC-05: Đăng xuất
1. Người dùng đã đăng nhập, mở menu avatar.
2. Bấm **Logout**.
3. Hệ thống kết thúc session, chuyển về trang Login.

---

## 8. Ma trận Test Case gợi ý (tóm tắt để tham chiếu)

| ID | Mô tả | Input | Kết quả mong đợi |
|---|---|---|---|
| TC-01 | Đăng nhập với Email/Password hợp lệ | admin@example.com / 123456 | Vào Dashboard |
| TC-02 | Đăng nhập bỏ trống cả 2 trường | (trống) / (trống) | Hiển thị 2 lỗi required |
| TC-03 | Đăng nhập chỉ bỏ trống Email | (trống) / abc123 | Hiển thị lỗi required cho Email |
| TC-04 | Đăng nhập chỉ bỏ trống Password | a@b.com / (trống) | Hiển thị lỗi required cho Password |
| TC-05 | Đăng nhập sai Password, đúng Email | admin@example.com / saipass | "Invalid email or password" |
| TC-06 | Đăng nhập với Email không tồn tại | random@x.com / 123456 | "Invalid email or password" |
| TC-07 | Đăng nhập với Email sai định dạng | abc / 123456 | Trình duyệt/hệ thống chặn định dạng email không hợp lệ |
| TC-08 | Tick "Remember me" và đăng nhập | admin@example.com / 123456, tick Remember me | Đăng nhập thành công, phiên được ghi nhớ dài hơn |
| TC-09 | Truy cập lại trang Login khi đã đăng nhập | - | Tự động redirect Dashboard |
| TC-10 | Đăng xuất | Bấm Logout | Về lại trang Login, không truy cập được Dashboard bằng session cũ |
| TC-11 | Quên mật khẩu bỏ trống Email | (trống), bấm Confirm | "Email not found" |
| TC-12 | Quên mật khẩu với Email hợp lệ tồn tại | admin@example.com | Nhận thông báo xử lý yêu cầu (thành công/lỗi tùy cấu hình SMTP) |
| TC-13 | Quên mật khẩu với Email không tồn tại | notexist@x.com | "Email not found" |
| TC-14 | Truy cập trang Forgot Password khi đã đăng nhập | - | Tự động redirect Dashboard |

---

## 9. Rủi ro & Khuyến nghị

1. **User enumeration**: Thông báo "Email not found" ở form Forgot Password khác với thông báo chung "Invalid email or password" ở form Login, tạo khả năng dò được email hợp lệ trong hệ thống. Khuyến nghị thống nhất thông báo trung lập.
2. **Chưa xác minh được**: cơ chế khóa tài khoản/giới hạn số lần đăng nhập sai (brute-force protection), thời gian hết hạn session khi có/không có "Remember me", ràng buộc độ mạnh của Password khi tạo/đổi mật khẩu — cần kiểm thử bổ sung ở tầng API/DB hoặc với tài khoản test chuyên biệt.
3. **Case-sensitivity của Email**: chưa kiểm chứng hệ thống có phân biệt hoa/thường với Email đăng nhập hay không — nên bổ sung test case riêng.

---

*Tài liệu được biên soạn dựa trên khảo sát thực tế giao diện tại `https://crm.anhtester.com/admin/authentication` ngày 2026-09-18.*
