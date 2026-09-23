# Test Strategy – Module Login
## Hệ thống: Perfex CRM (Anh Tester Demo)

| Thuộc tính | Giá trị |
|---|---|
| URL kiểm thử | https://crm.anhtester.com/admin/authentication |
| Tài liệu tham chiếu | [SRS-Login-Module.md](SRS-Login-Module.md) (v1.0) |
| Chuẩn tham chiếu | ISTQB CTFL v4.0, ISTQB CTAL Test Manager, ISO/IEC/IEEE 29119-3 |
| Ngày lập | 2026-09-23 |
| Người thực hiện | duongnt-17 |
| Phiên bản | 1.0 |

---

## 1. Giới thiệu

### 1.1 Mục đích
Tài liệu mô tả **cách tiếp cận kiểm thử** (test strategy) cho module Login của Perfex CRM: kiểm thử những gì, bằng kỹ thuật nào, trên môi trường nào, khi nào bắt đầu/kết thúc và đo lường chất lượng ra sao. Tài liệu là cơ sở để thiết kế test case, lập kế hoạch thực thi và báo cáo kết quả kiểm thử.

### 1.2 Vị trí trong hệ thống tài liệu kiểm thử (theo ISTQB)

| Cấp | Tài liệu | Trạng thái |
|---|---|---|
| Tổ chức | Test Policy – *vì sao* kiểm thử | Không áp dụng (dự án thực hành) |
| Tổ chức / Chương trình | **Test Strategy – kiểm thử *như thế nào*** | **Tài liệu này** |
| Dự án | Test Plan / Test Approach – ai, làm gì, khi nào | Kế thừa từ tài liệu này |
| Thiết kế | Test Case | [Testcase/TC_Login.md](Testcase/TC_Login.md) |

### 1.3 Định nghĩa, từ viết tắt

| Từ viết tắt | Ý nghĩa |
|---|---|
| EP | Equivalence Partitioning – Phân vùng tương đương |
| BVA | Boundary Value Analysis – Phân tích giá trị biên |
| DDP | Defect Detection Percentage – Tỷ lệ phát hiện lỗi |
| SUT | System Under Test – Hệ thống được kiểm thử |
| TC | Test Case |

---

## 2. Phạm vi kiểm thử

### 2.1 Trong phạm vi (In scope)

| Mã | Chức năng | Tham chiếu SRS |
|---|---|---|
| F-01 | Đăng nhập bằng Email/Password | FR-01, BR-01, BR-02 |
| F-02 | Ghi nhớ đăng nhập (Remember me) | FR-02, BR-04, BR-05 |
| F-03 | Quên mật khẩu (Forgot Password) – phần giao diện và thông báo | FR-03, BR-06, BR-07, BR-08 |
| F-04 | Đăng xuất (Logout) | FR-04, BR-09 |
| F-05 | Điều hướng phiên (session guard) | BR-03, NFR-06 |
| F-06 | Bảo mật cơ bản mức UI (ẩn password, thông báo lỗi chung, SQL Injection/XSS ở input) | NFR-01, NFR-02 |
| F-07 | Tương thích trình duyệt | NFR-04 |

### 2.2 Ngoài phạm vi (Out of scope)
- Phân quyền chi tiết theo vai trò sau khi đăng nhập.
- Các module nghiệp vụ khác (Customers, Projects, Invoices...).
- Luồng đặt lại mật khẩu qua email (môi trường demo không có SMTP).
- Performance/Load testing chuyên sâu, Penetration testing (chỉ kiểm thử bảo mật ở mức input cơ bản).
- Unit test, API test, kiểm tra trực tiếp database (không có quyền truy cập mã nguồn/DB).

---

## 3. Loại Test Strategy áp dụng

Theo ISTQB, dự án **kết hợp** nhiều loại strategy thay vì dùng một loại duy nhất:

| Loại strategy (ISTQB) | Mức áp dụng | Cách áp dụng cụ thể |
|---|---|---|
| **Analytical – Risk-based** | Chính | Mức độ và thứ tự kiểm thử dựa trên Risk Register (mục 4). Rủi ro cao → test trước, test sâu hơn, Priority High |
| **Analytical – Requirements-based** | Chính | Mỗi FR/BR/NFR trong SRS phải có ít nhất 1 test case (mục 12 – Traceability) |
| **Methodical** | Bổ trợ | Dùng checklist Login chuẩn (trường trống, định dạng, khoảng trắng, hoa/thường, độ dài, SQL Injection, XSS, back button...) |
| **Reactive** | Bổ trợ | Exploratory testing theo session (time-box 60 phút) sau khi chạy xong test case có kịch bản |
| **Regression-averse** | Bổ trợ | Duy trì bộ regression (các TC Priority High) chạy lại sau mỗi lần sửa lỗi hoặc khi môi trường demo được cập nhật |

---

## 4. Phân tích rủi ro (Risk Register)

Mức rủi ro = Khả năng xảy ra (1–3) × Mức ảnh hưởng (1–3). **≥ 6: Cao**, 3–4: Trung bình, ≤ 2: Thấp.

| Mã | Rủi ro | Loại | Khả năng | Ảnh hưởng | Mức | Cách xử lý trong kiểm thử |
|---|---|---|---|---|---|---|
| R-01 | Người dùng hợp lệ không đăng nhập được | Sản phẩm | 1 | 3 | 3 – TB | Happy path là TC Priority High, chạy đầu tiên và trong mọi vòng regression |
| R-02 | Truy cập được khu vực `/admin` khi chưa đăng nhập hoặc sau khi Logout | Sản phẩm | 2 | 3 | **6 – Cao** | Test session guard, Back button, truy cập URL trực tiếp sau Logout |
| R-03 | SQL Injection / XSS qua form Login | Sản phẩm | 1 | 3 | 3 – TB | Error guessing với bộ payload chuẩn |
| R-04 | Không có cơ chế chống brute-force (lockout/rate limit) | Sản phẩm | 3 | 2 | **6 – Cao** | Test đăng nhập sai liên tiếp; báo cáo phát hiện kể cả khi SRS chưa quy định |
| R-05 | User enumeration qua thông báo "Email not found" ở Forgot Password | Sản phẩm | 3 | 2 | **6 – Cao** | Ghi nhận thành defect/khuyến nghị bảo mật (đã nêu trong SRS mục 9) |
| R-06 | "Remember me" không hoạt động hoặc session tồn tại quá lâu | Sản phẩm | 2 | 2 | 4 – TB | Kiểm tra cookie và hạn dùng qua DevTools |
| R-07 | Thông báo lỗi không đúng nội dung hoặc lộ trường nào sai | Sản phẩm | 1 | 2 | 2 – Thấp | Đối chiếu với Message Catalog (SRS mục 6) |
| R-08 | Môi trường demo dùng chung: người khác đổi mật khẩu `admin@example.com`, hoặc hệ thống được reset giữa chừng | Dự án | 2 | 3 | **6 – Cao** | Kiểm tra tài khoản trước mỗi phiên test (smoke); ghi rõ thời điểm thực thi; test lại khi nghi do môi trường |
| R-09 | SRS chưa quy định một số quy tắc (trim khoảng trắng, hoa/thường của email, lockout) | Dự án | 3 | 1 | 3 – TB | Ghi nhận "Cần BA xác nhận" trong Expected Result; tổng hợp câu hỏi gửi BA |

---

## 5. Test levels và Test types

### 5.1 Test levels

| Test level | Áp dụng | Lý do |
|---|---|---|
| Component testing | Không | Không có quyền truy cập mã nguồn |
| Component integration testing | Không | Không có quyền truy cập mã nguồn/API nội bộ |
| **System testing** | **Có – trọng tâm** | Kiểm thử hộp đen end-to-end qua giao diện web |
| System integration testing | Không | Chức năng gửi email (SMTP) không khả dụng trên môi trường demo |
| Acceptance testing | Một phần | Kiểm tra theo Use Case UC-01 → UC-05 trong SRS từ góc nhìn người dùng |

### 5.2 Test types

| Test type | Áp dụng | Nội dung |
|---|---|---|
| **Functional** | Có | Toàn bộ FR-01 → FR-04, các BR |
| **Non-functional – Security** (mức UI) | Có | Ẩn password, thông báo lỗi chung, SQL Injection, XSS, session sau Logout, brute-force |
| **Non-functional – Usability** | Có | Thông báo lỗi rõ ràng, có màu phân biệt; thao tác bằng bàn phím (Tab, Enter) |
| **Non-functional – Compatibility** | Có | Chrome, Edge, Firefox; desktop và tablet |
| **Non-functional – Performance** | Quan sát | Thời gian phản hồi sau khi bấm Login < 2 giây (đo bằng DevTools > Network), không làm load test |
| **Confirmation testing** (retest) | Có | Chạy lại TC gây ra defect sau khi defect được báo đã sửa |
| **Regression testing** | Có | Chạy lại bộ TC Priority High |

---

## 6. Kỹ thuật kiểm thử (Test Techniques)

| Kỹ thuật (ISTQB) | Nhóm | Áp dụng cho |
|---|---|---|
| **Equivalence Partitioning** | Black-box | Email: hợp lệ-tồn tại / hợp lệ-không tồn tại / sai định dạng / trống. Password: đúng / sai / trống |
| **Boundary Value Analysis** | Black-box | Độ dài Email và Password: 0, 1, 254, 255, 256 ký tự và rất dài (> 1000) |
| **Decision Table** | Black-box | Tổ hợp Email (trống/sai/đúng) × Password (trống/sai/đúng) × Remember me (có/không) |
| **State Transition** | Black-box | Trạng thái: Chưa đăng nhập → Đã đăng nhập → Đã Logout; chuyển trạng thái bằng Login, Logout, Back, truy cập URL trực tiếp, đóng trình duyệt |
| **Use Case Testing** | Black-box | UC-01 → UC-05 trong SRS |
| **Error Guessing** | Experience-based | Khoảng trắng đầu/cuối, chữ hoa, ký tự Unicode, SQL Injection, XSS, double-click nút Login |
| **Checklist-based** | Experience-based | Checklist Login chuẩn (mục 3) |
| **Exploratory Testing** | Experience-based | 1 session 60 phút/vòng, có charter, ghi chép kết quả |

### 6.1 Decision Table – Đăng nhập

| Điều kiện / Hành động | R1 | R2 | R3 | R4 | R5 | R6 |
|---|---|---|---|---|---|---|
| Email | Trống | Trống | Có | Đúng | Sai/không tồn tại | Đúng |
| Password | Trống | Có | Trống | Sai | Bất kỳ | Đúng |
| **Kết quả** | 2 lỗi required | Lỗi required Email | Lỗi required Password | "Invalid email or password" | "Invalid email or password" | Vào Dashboard |

### 6.2 State Transition – Phiên đăng nhập

| Trạng thái hiện tại | Sự kiện | Trạng thái kế tiếp | Kết quả mong đợi |
|---|---|---|---|
| Chưa đăng nhập | Login hợp lệ | Đã đăng nhập | Vào Dashboard |
| Chưa đăng nhập | Login không hợp lệ | Chưa đăng nhập | Hiển thị lỗi, ở lại trang Login |
| Chưa đăng nhập | Truy cập URL `/admin/...` | Chưa đăng nhập | Redirect về trang Login |
| Đã đăng nhập | Truy cập `/admin/authentication` | Đã đăng nhập | Redirect Dashboard (BR-03) |
| Đã đăng nhập | Truy cập Forgot Password | Đã đăng nhập | Redirect Dashboard (BR-08) |
| Đã đăng nhập | Logout | Đã Logout | Về trang Login |
| Đã Logout | Back / F5 / truy cập URL cũ | Đã Logout | Redirect về trang Login (BR-09) |
| Đã đăng nhập (không tick Remember me) | Đóng và mở lại trình duyệt | Chưa đăng nhập | Phải đăng nhập lại |
| Đã đăng nhập (có tick Remember me) | Đóng và mở lại trình duyệt | Đã đăng nhập | Vào thẳng Dashboard |

---

## 7. Tiêu chí bắt đầu và kết thúc (Entry / Exit Criteria)

### 7.1 Entry criteria
- SRS module Login đã được review; các câu hỏi mở đã được ghi nhận.
- Test case đã được thiết kế và review chéo.
- Môi trường demo truy cập được, tài khoản `admin@example.com / 123456` đăng nhập thành công (smoke test đạt).
- Trình duyệt và công cụ ghi nhận lỗi đã sẵn sàng.

### 7.2 Exit criteria
- 100% test case Priority **High** đã được thực thi; ≥ 95% tổng số test case đã được thực thi.
- 100% FR/BR/NFR trong phạm vi có ít nhất 1 test case đã thực thi (requirement coverage).
- Không còn defect **Critical** ở trạng thái Open; defect **High** còn mở phải có phương án xử lý hoặc được chấp nhận rủi ro.
- Pass rate ≥ 90% (không tính các TC bị Blocked do môi trường).
- Đã hoàn thành ít nhất 1 session exploratory testing.
- Test Summary Report đã được lập.

### 7.3 Tiêu chí tạm dừng / tiếp tục (Suspension / Resumption)
- **Tạm dừng** khi: môi trường demo không truy cập được, hoặc tài khoản test không đăng nhập được do nguyên nhân từ môi trường (R-08).
- **Tiếp tục** khi: smoke test (TC đăng nhập hợp lệ) chạy đạt trở lại.

---

## 8. Môi trường và dữ liệu kiểm thử

### 8.1 Test environment

| Hạng mục | Chi tiết |
|---|---|
| SUT | https://crm.anhtester.com/admin/authentication (môi trường demo dùng chung) |
| Hệ điều hành | Windows 11 |
| Trình duyệt | Chrome (bản mới nhất – trình duyệt chính), Edge, Firefox (bản mới nhất) |
| Độ phân giải | Desktop 1920×1080, 1366×768; Tablet 768×1024 (dùng DevTools device mode) |
| Công cụ hỗ trợ | Chrome DevTools (Cookies, Network, Console), công cụ chụp màn hình/quay video |
| Trạng thái đầu | Xóa cookie/cache của domain trước mỗi test case, trừ các TC về Remember me/session |

### 8.2 Test data

| Nhóm | Dữ liệu |
|---|---|
| Tài khoản hợp lệ | `admin@example.com` / `123456` |
| Email không tồn tại | `notexist@example.com` |
| Email sai định dạng | `adminexample.com`, `admin@`, `@example.com`, `admin@@example.com` |
| Khoảng trắng / hoa thường | `  admin@example.com  `, `ADMIN@EXAMPLE.COM`, password ` 123456 ` |
| Giá trị biên độ dài | Chuỗi 255, 256, 1000 ký tự |
| Payload bảo mật | `' OR '1'='1`, `admin@example.com'--`, `<script>alert(1)</script>`, `"><img src=x onerror=alert(1)>` |

> Lưu ý: chỉ dùng payload bảo mật ở mức kiểm tra input trên form. Không thực hiện tấn công brute-force số lượng lớn hoặc gây ảnh hưởng tới môi trường demo dùng chung.

---

## 9. Mức độ độc lập kiểm thử (Independence of Testing)
- Kiểm thử do **tester độc lập** với đội phát triển (không tham gia phát triển Perfex CRM) — mức độc lập cao theo ISTQB.
- Test case được **review chéo** giữa các học viên hoặc giữa tester và AI trước khi thực thi.

---

## 10. Quản lý lỗi (Defect Management)

### 10.1 Vòng đời lỗi
`New → Open/Assigned → Fixed → Retest → Closed`, các nhánh: `Rejected`, `Deferred`, `Reopened` (khi retest không đạt).

### 10.2 Severity

| Mức | Định nghĩa | Ví dụ trong module Login |
|---|---|---|
| Critical | Chặn chức năng chính hoặc lỗ hổng bảo mật nghiêm trọng | Không đăng nhập được với tài khoản hợp lệ; vào được Dashboard khi chưa đăng nhập; SQL Injection thành công |
| High | Chức năng chạy sai, có ảnh hưởng lớn | Vẫn xem được trang quản trị sau Logout; không chống brute-force |
| Medium | Sai một phần, có cách khắc phục tạm | Remember me không hoạt động; thông báo lỗi sai nội dung |
| Low | Lỗi giao diện, chính tả, trải nghiệm | Lệch căn chỉnh, sai màu thông báo |

### 10.3 Priority
High (sửa ngay) / Medium (sửa trong vòng hiện tại) / Low (sửa khi có thời gian).

### 10.4 Nội dung bắt buộc của một báo cáo lỗi
ID, Tiêu đề, Môi trường (trình duyệt, phiên bản, thời điểm), Các bước tái hiện, Test data, Kết quả thực tế, Kết quả mong đợi, Severity, Priority, Ảnh chụp/Video, TC liên quan.

---

## 11. Metrics và báo cáo

### 11.1 Test metrics

| Metric | Công thức | Mục tiêu |
|---|---|---|
| Test execution rate | TC đã chạy / Tổng TC × 100% | ≥ 95% |
| Pass rate | TC Pass / TC đã chạy × 100% | ≥ 90% |
| Requirement coverage | Yêu cầu có TC đã chạy / Tổng yêu cầu trong phạm vi × 100% | 100% |
| Defect theo Severity | Số lỗi mở theo từng mức | 0 Critical mở |
| Defect density | Số lỗi / Số chức năng (F-01 → F-07) | Theo dõi xu hướng |
| Blocked rate | TC Blocked / Tổng TC × 100% | Theo dõi (thường do môi trường) |

### 11.2 Báo cáo

| Báo cáo | Thời điểm | Nội dung |
|---|---|---|
| Test Progress Report | Cuối mỗi ngày thực thi | Số TC đã chạy/Pass/Fail/Blocked, lỗi mới, rủi ro/vướng mắc |
| Test Summary Report | Khi đạt Exit criteria | Tổng kết metrics, lỗi còn mở, đánh giá chất lượng, khuyến nghị (go/no-go) |

---

## 12. Ma trận truy vết (Traceability) Yêu cầu → Test Case

Mã TC tham chiếu bộ test case chi tiết `TC_LOGIN_xxx` / `TC_FP_xxx` trong [Testcase/TC_Login.md](Testcase/TC_Login.md).

| Yêu cầu | Nội dung | Test Case |
|---|---|---|
| FR-01 / UC-01 | Đăng nhập thành công | TC_LOGIN_002, 005, 007 |
| BR-01 / UC-03 | Email, Password bắt buộc | TC_LOGIN_008, 009, 010, 021 |
| BR-02 / UC-02 | Thông báo lỗi chung | TC_LOGIN_011, 012, 013, 016 |
| BR-03, BR-08 / NFR-06 | Đã đăng nhập → redirect Dashboard | TC_LOGIN_006, TC_FP_006 |
| FR-02 / BR-04, BR-05 | Remember me | TC_LOGIN_001, 003, 004 |
| FR-03 / BR-06 → BR-08 | Forgot Password | TC_FP_001 → TC_FP_010 |
| FR-04 / BR-09 | Logout, không dùng lại session cũ | TC_LOGIN_017 |
| NFR-01 | Ẩn password, bảo mật input | TC_LOGIN_023, 024, 025, TC_FP_009 |
| NFR-02 | Chống brute-force | TC_LOGIN_026 |
| NFR-03 | Usability thông báo lỗi | TC_LOGIN_008 → 013 (kiểm tra kèm) |
| NFR-04 | Tương thích trình duyệt | Chạy lại bộ TC High trên Edge, Firefox |
| NFR-05 | Thời gian phản hồi < 2 giây | TC_LOGIN_002 (đo kèm qua DevTools) |
| Định dạng email | Email sai định dạng | TC_LOGIN_014, 015 |
| Edge case | Khoảng trắng, hoa/thường, độ dài | TC_LOGIN_018, 019, 020, 022, 027 |

---

## 13. Regression và Confirmation Testing
- **Confirmation testing**: mỗi lỗi được báo đã sửa phải được retest bằng đúng TC và test data ban đầu, trên cùng trình duyệt.
- **Bộ regression**: gồm toàn bộ TC Priority High (TC_LOGIN_001, 002, 007, 008, 009, 010, 011, 012, 017, 023, 024 và TC_FP_001, 002, 004, 005, 009).
- **Khi chạy regression**: sau mỗi đợt sửa lỗi; khi phát hiện môi trường demo được cập nhật/reset; trước khi lập Test Summary Report.
- **Ứng viên tự động hóa**: bộ regression ở trên phù hợp để tự động hóa (Selenium/Playwright) ở giai đoạn sau, do các bước ổn định và lặp lại nhiều lần.

---

## 14. Quản lý cấu hình testware (Configuration Management)
- Toàn bộ tài liệu (SRS, Test Strategy, Test Case) được quản lý phiên bản bằng **Git** trong repo này.
- Mỗi lần thay đổi test case/strategy phải commit kèm mô tả thay đổi.
- Kết quả thực thi ghi rõ phiên bản test case và thời điểm chạy, vì môi trường demo có thể thay đổi.

---

## 15. Vai trò và trách nhiệm (Roles & Responsibilities)

| Vai trò | Trách nhiệm |
|---|---|
| Test Lead | Lập và duy trì Test Strategy, theo dõi tiến độ, đánh giá Exit criteria, lập Test Summary Report |
| Tester | Thiết kế và thực thi test case, exploratory testing, báo cáo và retest lỗi |
| BA | Trả lời các câu hỏi mở về yêu cầu (mục 16), review test case |
| AI hỗ trợ (Claude) | Gợi ý test case, rà soát độ phủ, soạn tài liệu — kết quả phải được tester review trước khi sử dụng |

---

## 16. Giả định, ràng buộc và câu hỏi mở

### 16.1 Giả định và ràng buộc
- Môi trường demo phản ánh đúng hành vi của hệ thống thật.
- Không có quyền truy cập mã nguồn, database, log server → chỉ kiểm thử hộp đen.
- Môi trường dùng chung, dữ liệu có thể bị người khác thay đổi bất kỳ lúc nào.

### 16.2 Câu hỏi mở cần BA xác nhận

| # | Câu hỏi | Ảnh hưởng tới TC |
|---|---|---|
| Q-01 | Email có được tự động cắt khoảng trắng đầu/cuối không? | TC_LOGIN_018, TC_FP_008 |
| Q-02 | Email đăng nhập có phân biệt hoa/thường không? | TC_LOGIN_019, TC_FP_008 |
| Q-03 | Có chính sách khóa tài khoản/CAPTCHA sau N lần đăng nhập sai không? Nếu có, N bằng bao nhiêu và khóa trong bao lâu? | TC_LOGIN_026 |
| Q-04 | Thời gian sống của session khi có/không có "Remember me" là bao lâu? | TC_LOGIN_003, 004 |
| Q-05 | Độ dài tối đa của Email và Password? | TC_LOGIN_022 |
| Q-06 | Có đổi thông báo "Email not found" thành thông báo trung lập để tránh user enumeration không? | TC_FP_010 |

---

*Tài liệu được biên soạn dựa trên [SRS-Login-Module.md](SRS-Login-Module.md) và các chuẩn ISTQB/ISO 29119, ngày 2026-09-23.*
