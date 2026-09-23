# Test Case – Module Login
## Hệ thống: Perfex CRM (Anh Tester Demo)

| Thuộc tính | Giá trị |
|---|---|
| URL | https://crm.anhtester.com/admin/authentication |
| Tài liệu tham chiếu | [SRS-Login-Module.md](../SRS-Login-Module.md), [Test-Strategy-Login-Module.md](../Test-Strategy-Login-Module.md) |
| Ngày lập | 2026-09-23 |
| Người thực hiện | duongnt-17 |
| Phiên bản | 1.0 |

**Pre-condition chung:** Trình duyệt Chrome bản mới nhất; đã xóa cookie/cache của domain `crm.anhtester.com` (trừ khi TC yêu cầu khác); tài khoản `admin@example.com / 123456` đang hoạt động.

**Tổng hợp:**

| Nhóm | Số lượng TC |
|---|---|
| Login – Happy path | 7 |
| Login – Negative case | 10 |
| Login – Edge case | 10 |
| Forgot Password | 10 |
| **Tổng** | **37** |

---

## 1. Login

| Test Case ID | Test Scenario | Steps | Test Data | Expected Result | Priority |
|---|---|---|---|---|---|
| **HAPPY PATH** | | | | | |
| TC_LOGIN_001 | Kiểm tra giao diện trang Login | 1. Mở URL `https://crm.anhtester.com/admin/authentication`<br>2. Quan sát trang | URL như trên | - Tiêu đề "Login" hiển thị<br>- Có ô "Email Address", "Password", checkbox "Remember me" (mặc định unchecked), nút "Login", link "Forgot Password?"<br>- Ô Password ẩn ký tự | High |
| TC_LOGIN_002 | Đăng nhập thành công với Email/Password hợp lệ | 1. Mở URL trang Login<br>2. Nhập Email<br>3. Nhập Password<br>4. Click "Login" | Email: `admin@example.com`<br>Password: `123456` | - Chuyển sang Dashboard (URL `/admin`)<br>- Menu trái hiển thị: Customers, Projects, Tasks, Contracts, Sales…<br>- Hiển thị khối Invoice overview, Estimate overview, Proposal overview | High |
| TC_LOGIN_003 | Đăng nhập thành công khi tick "Remember me" | 1. Mở URL trang Login<br>2. Nhập Email, Password<br>3. Tick "Remember me"<br>4. Click "Login"<br>5. Đóng toàn bộ trình duyệt, mở lại và truy cập `/admin` | Email: `admin@example.com`<br>Password: `123456` | - Bước 4: vào Dashboard thành công<br>- Bước 5: vào thẳng Dashboard, không phải đăng nhập lại<br>- DevTools > Application > Cookies có cookie ghi nhớ đăng nhập với Expires lớn hơn thời điểm hiện tại | Medium |
| TC_LOGIN_004 | Đăng nhập không tick "Remember me" – phiên kết thúc khi đóng trình duyệt | 1. Đăng nhập, không tick "Remember me"<br>2. Đóng toàn bộ trình duyệt<br>3. Mở lại và truy cập `/admin` | Email: `admin@example.com`<br>Password: `123456` | Bị redirect về `/admin/authentication`, phải đăng nhập lại | Medium |
| TC_LOGIN_005 | Đăng nhập bằng phím Enter | 1. Mở URL trang Login<br>2. Nhập Email, Password<br>3. Nhấn Enter khi con trỏ đang ở ô Password | Email: `admin@example.com`<br>Password: `123456` | Đăng nhập thành công, chuyển sang Dashboard như TC_LOGIN_002 | Medium |
| TC_LOGIN_006 | Đã đăng nhập, truy cập lại trang Login | 1. Đăng nhập thành công<br>2. Nhập lại URL `/admin/authentication` trên thanh địa chỉ | Tài khoản hợp lệ | Tự động chuyển về Dashboard, không hiển thị form Login (BR-03) | Medium |
| TC_LOGIN_007 | Truy cập trang nội bộ khi chưa đăng nhập → đăng nhập → quay lại đúng trang | 1. Chưa đăng nhập, truy cập `https://crm.anhtester.com/admin/clients`<br>2. Đăng nhập bằng tài khoản hợp lệ | Tài khoản hợp lệ | - Bước 1: bị redirect về trang Login<br>- Bước 2: vào `/admin/clients` (trang yêu cầu ban đầu) hoặc Dashboard | High |
| **NEGATIVE CASE** | | | | | |
| TC_LOGIN_008 | Bỏ trống cả Email và Password | 1. Mở URL trang Login<br>2. Không nhập gì<br>3. Click "Login" | Email: (trống)<br>Password: (trống) | - Vẫn ở trang Login<br>- Hiển thị 2 lỗi: "The Email Address field is required." và "The Password field is required." | High |
| TC_LOGIN_009 | Bỏ trống Email | 1. Để trống Email<br>2. Nhập Password<br>3. Click "Login" | Email: (trống)<br>Password: `123456` | Vẫn ở trang Login, hiển thị "The Email Address field is required." | High |
| TC_LOGIN_010 | Bỏ trống Password | 1. Nhập Email<br>2. Để trống Password<br>3. Click "Login" | Email: `admin@example.com`<br>Password: (trống) | Vẫn ở trang Login, hiển thị "The Password field is required." | High |
| TC_LOGIN_011 | Email đúng, Password sai | 1. Nhập Email đúng<br>2. Nhập Password sai<br>3. Click "Login" | Email: `admin@example.com`<br>Password: `654321` | - Vẫn ở trang Login<br>- Hiển thị "Invalid email or password"<br>- Không tiết lộ trường nào sai (BR-02) | High |
| TC_LOGIN_012 | Email không tồn tại, Password đúng | 1. Nhập Email chưa đăng ký<br>2. Nhập Password<br>3. Click "Login" | Email: `notexist@example.com`<br>Password: `123456` | Hiển thị "Invalid email or password" – giống hệt thông báo ở TC_LOGIN_011 | High |
| TC_LOGIN_013 | Cả Email và Password đều sai | 1. Nhập Email sai<br>2. Nhập Password sai<br>3. Click "Login" | Email: `wrong@example.com`<br>Password: `abc123` | Vẫn ở trang Login, hiển thị "Invalid email or password" | Medium |
| TC_LOGIN_014 | Email sai định dạng (thiếu @) | 1. Nhập Email sai định dạng<br>2. Nhập Password<br>3. Click "Login" | Email: `adminexample.com`<br>Password: `123456` | Form không được submit; trình duyệt hiển thị cảnh báo HTML5 tại ô Email ("Please include an '@' in the email address…") | Medium |
| TC_LOGIN_015 | Email sai định dạng (thiếu domain) | 1. Nhập Email<br>2. Nhập Password<br>3. Click "Login" | Email: `admin@`<br>Password: `123456` | Form không được submit; trình duyệt hiển thị cảnh báo định dạng email tại ô Email | Medium |
| TC_LOGIN_016 | Password gần đúng (thiếu 1 ký tự) | 1. Nhập Email đúng<br>2. Nhập Password gần đúng<br>3. Click "Login" | Email: `admin@example.com`<br>Password: `12345` | Hiển thị "Invalid email or password", không đăng nhập được | Medium |
| TC_LOGIN_017 | Sau khi Logout, dùng nút Back trình duyệt | 1. Đăng nhập thành công<br>2. Avatar > Logout<br>3. Click Back trên trình duyệt<br>4. Reload trang (F5) | Tài khoản hợp lệ | - Bước 2: về trang Login<br>- Bước 3–4: không xem/thao tác được dữ liệu Dashboard, bị redirect về trang Login (BR-09) | High |
| **EDGE CASE** | | | | | |
| TC_LOGIN_018 | Email có khoảng trắng đầu/cuối | 1. Nhập Email có khoảng trắng 2 đầu<br>2. Nhập Password<br>3. Click "Login" | Email: `  admin@example.com  `<br>Password: `123456` | Đăng nhập thành công (khoảng trắng được trim). *Cần BA xác nhận – Q-01* | Medium |
| TC_LOGIN_019 | Email viết hoa | 1. Nhập Email viết hoa<br>2. Nhập Password<br>3. Click "Login" | Email: `ADMIN@EXAMPLE.COM`<br>Password: `123456` | Đăng nhập thành công (email không phân biệt hoa/thường). *Cần BA xác nhận – Q-02* | Low |
| TC_LOGIN_020 | Password có khoảng trắng đầu/cuối | 1. Nhập Email đúng<br>2. Nhập Password có khoảng trắng<br>3. Click "Login" | Email: `admin@example.com`<br>Password: ` 123456 ` | Hiển thị "Invalid email or password" (password không được trim) | Medium |
| TC_LOGIN_021 | Chỉ nhập khoảng trắng vào cả 2 ô | 1. Nhập toàn dấu cách vào Email và Password<br>2. Click "Login" | Email: `     `<br>Password: `     ` | Không đăng nhập được; hiển thị lỗi required hoặc cảnh báo định dạng email; không xảy ra lỗi hệ thống (HTTP 500) | Medium |
| TC_LOGIN_022 | Email/Password rất dài (> 255 ký tự) | 1. Nhập Email 300 ký tự<br>2. Nhập Password 300 ký tự<br>3. Click "Login" | Email: `aaa…a@example.com` (300 ký tự)<br>Password: 300 ký tự `a` | Hiển thị "Invalid email or password" hoặc lỗi độ dài; không lỗi 500, không vỡ giao diện. *Cần BA xác nhận độ dài tối đa – Q-05* | Low |
| TC_LOGIN_023 | SQL Injection | 1. Nhập chuỗi SQL vào Email và Password<br>2. Click "Login" | Email: `admin@example.com' OR '1'='1`<br>Password: `' OR '1'='1` | Không đăng nhập được; hiển thị lỗi định dạng hoặc "Invalid email or password"; không lộ lỗi SQL/stack trace | High |
| TC_LOGIN_024 | XSS | 1. Nhập script vào ô Email<br>2. Nhập Password<br>3. Click "Login" | Email: `<script>alert(1)</script>`<br>Password: `123456` | Không có popup alert thực thi; không đăng nhập được; dữ liệu được escape khi hiển thị lại | High |
| TC_LOGIN_025 | Bảo mật ô Password | 1. Nhập Password<br>2. Chuột phải vào ô Password > Inspect<br>3. Bôi đen ô Password, nhấn Ctrl+C rồi dán ra Notepad | Password: `123456` | - Ô Password có `type="password"`, hiển thị dấu chấm<br>- Không copy được giá trị password ra ngoài | Medium |
| TC_LOGIN_026 | Đăng nhập sai nhiều lần liên tiếp | 1. Nhập Email đúng, Password sai, click "Login"<br>2. Lặp lại bước 1 tổng cộng 5 lần<br>3. Lần 6 nhập đúng Password | Email: `admin@example.com`<br>Password: `wrong1`…`wrong5`, sau đó `123456` | Hệ thống áp dụng chính sách khóa tạm/CAPTCHA theo quy định; nếu không có chính sách → lần 6 đăng nhập thành công và ghi nhận rủi ro R-04. *Cần BA xác nhận – Q-03* | Medium |
| TC_LOGIN_027 | Reload trang sau khi nhập thông tin, trước khi Login | 1. Nhập Email, Password<br>2. Nhấn F5 | Email: `admin@example.com`<br>Password: `123456` | Ô Password bị xóa trắng; không tự động đăng nhập | Low |

---

## 2. Forgot Password

**Pre-condition riêng:** Chưa đăng nhập (trừ TC_FP_006). Lưu ý: môi trường demo không cấu hình SMTP nên không xác minh được việc gửi email đặt lại mật khẩu (xem SRS FR-03).

| Test Case ID | Test Scenario | Steps | Test Data | Expected Result | Priority |
|---|---|---|---|---|---|
| **HAPPY PATH** | | | | | |
| TC_FP_001 | Điều hướng từ trang Login sang Forgot Password và kiểm tra giao diện | 1. Mở URL trang Login<br>2. Click link "Forgot Password?"<br>3. Quan sát trang | - | - URL chuyển thành `/admin/authentication/forgot_password`<br>- Hiển thị ô "Email Address" và nút "Confirm" | High |
| TC_FP_002 | Gửi yêu cầu với Email hợp lệ đã tồn tại | 1. Mở trang Forgot Password<br>2. Nhập Email<br>3. Click "Confirm" | Email: `admin@example.com` | - Không hiển thị "Email not found"<br>- Trên môi trường demo: hiển thị "Error setting new password" (do không có SMTP)<br>- Trên môi trường có SMTP: hiển thị thông báo đã gửi hướng dẫn và email được gửi tới hộp thư | High |
| TC_FP_003 | Gửi yêu cầu bằng phím Enter | 1. Mở trang Forgot Password<br>2. Nhập Email<br>3. Nhấn Enter | Email: `admin@example.com` | Form được submit, kết quả giống TC_FP_002 | Low |
| **NEGATIVE CASE** | | | | | |
| TC_FP_004 | Bỏ trống Email | 1. Mở trang Forgot Password<br>2. Để trống Email<br>3. Click "Confirm" | Email: (trống) | Vẫn ở trang Forgot Password, hiển thị "Email not found" (theo hành vi quan sát trong SRS; BR-06 quy định trường bắt buộc) | High |
| TC_FP_005 | Email không tồn tại trong hệ thống | 1. Mở trang Forgot Password<br>2. Nhập Email chưa đăng ký<br>3. Click "Confirm" | Email: `notexist@example.com` | Vẫn ở trang Forgot Password, hiển thị "Email not found" (BR-07) | High |
| TC_FP_006 | Đã đăng nhập, truy cập trang Forgot Password | 1. Đăng nhập thành công<br>2. Nhập URL `/admin/authentication/forgot_password` trên thanh địa chỉ | Tài khoản hợp lệ | Tự động chuyển về Dashboard, không hiển thị form Forgot Password (BR-08) | Medium |
| TC_FP_007 | Email sai định dạng | 1. Mở trang Forgot Password<br>2. Nhập Email sai định dạng<br>3. Click "Confirm" | Email: `adminexample.com` | Form không được submit; trình duyệt hiển thị cảnh báo HTML5 tại ô Email | Medium |
| **EDGE CASE** | | | | | |
| TC_FP_008 | Email viết hoa / có khoảng trắng đầu cuối | 1. Mở trang Forgot Password<br>2. Nhập Email<br>3. Click "Confirm" | Email: `  ADMIN@EXAMPLE.COM  ` | Được xử lý như `admin@example.com` (kết quả giống TC_FP_002), không hiển thị "Email not found". *Cần BA xác nhận – Q-01, Q-02* | Low |
| TC_FP_009 | SQL Injection / XSS ở ô Email | 1. Mở trang Forgot Password<br>2. Lần lượt nhập từng payload<br>3. Click "Confirm" sau mỗi lần | Email: `admin@example.com' OR '1'='1`<br>Email: `<script>alert(1)</script>` | Không có popup alert thực thi; hiển thị lỗi định dạng hoặc "Email not found"; không lộ lỗi SQL/stack trace, không lỗi 500 | High |
| TC_FP_010 | User enumeration – so sánh thông báo giữa email tồn tại và không tồn tại | 1. Thực hiện TC_FP_002, ghi lại thông báo<br>2. Thực hiện TC_FP_005, ghi lại thông báo<br>3. So sánh 2 thông báo | Email: `admin@example.com`<br>Email: `notexist@example.com` | Hai thông báo giống nhau (thông báo trung lập, ví dụ: "Nếu email tồn tại, hướng dẫn đặt lại mật khẩu đã được gửi") – không thể suy ra email nào tồn tại. *Theo khuyến nghị bảo mật SRS mục 9; hành vi hiện tại dự kiến Fail – chờ BA xác nhận Q-06* | Medium |
