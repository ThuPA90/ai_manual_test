# Phân tích yêu cầu — Chức năng Đăng nhập (Login)

| Hạng mục | Nội dung |
|---|---|
| Module | LOGIN (Authentication) |
| URL | https://anhtester.com/login |
| Hệ thống | ANHTESTER LMS — Nền tảng học Test Automation |
| Ngày phân tích | 2026-09-08 |
| Nguồn phân tích | Khảo sát trực tiếp giao diện `/login` (cây DOM + ảnh chụp màn hình) — **không có tài liệu SRS đầu vào** |
| Tài khoản test | `phamthunb90@gmail.com` (mật khẩu quản lý riêng, không lưu trong tài liệu) |

> **Giới hạn khảo sát:** phân tích dựa trên quan sát giao diện, **chưa đăng nhập thật**. Lý do: (1) không tự nhập mật khẩu của người dùng vào form, (2) trang có reCAPTCHA v2 mà công cụ tự động không được phép vượt qua.
>
> **Cập nhật 2026-09-08:** người dùng đã xác nhận Q1–Q4 (trang đích, nội dung thông báo lỗi, cơ chế khoá tài khoản, thời hạn ghi nhớ đăng nhập) , Q11–Q14 (cách tính bộ đếm sai, thông báo khoá, đặt lại mật khẩu khi bị khoá, vị trí hiển thị lỗi bỏ trống) và Q15–Q16 (không gửi email cảnh báo, không có cơ chế mở khoá sớm). Các mục này đã chuyển từ giả định sang yêu cầu chính thức. Phần còn lại chưa xác nhận vẫn giữ dấu ❓ — hiện chỉ còn Q5–Q10.

---

## 1. Mục tiêu chức năng

Cho phép người dùng đã có tài khoản xác thực danh tính để truy cập khu vực học viên của LMS, qua 2 phương thức:

- Đăng nhập nội bộ bằng **Email + Mật khẩu** (kèm reCAPTCHA).
- Đăng nhập liên kết (SSO) qua **Google** hoặc **GitHub**.

## 2. Actor và tiền điều kiện

| Actor | Mô tả | Tiền điều kiện |
|---|---|---|
| Học viên đã đăng ký | Người dùng có tài khoản nội bộ | Tài khoản tồn tại, trạng thái active |
| Người dùng SSO | Có tài khoản Google/GitHub | Đã đăng nhập hoặc sẵn sàng đăng nhập tại nhà cung cấp |
| Khách vãng lai | Chưa có tài khoản | Được điều hướng sang `/register` |

Điều kiện chung: có kết nối Internet; trình duyệt bật JavaScript và cookie (bắt buộc cho reCAPTCHA và session).

## 3. Thành phần giao diện (ghi nhận thực tế trên trang)

| # | Thành phần | Kiểu | Ghi chú |
|---|---|---|---|
| 1 | Email | `input[type=email]` | Có dấu `*` — bắt buộc, kích hoạt validation HTML5 |
| 2 | Mật khẩu | `input[type=password]` | Có icon con mắt để hiện/ẩn mật khẩu |
| 3 | Ghi nhớ đăng nhập | `checkbox` | Mặc định bỏ chọn |
| 4 | Quên mật khẩu? | link | → `/forgot-password` |
| 5 | reCAPTCHA v2 | widget | Ô "Tôi không phải là người máy"; trang cảnh báo **hết hạn sau 2 phút** |
| 6 | Đăng nhập | `button[type=submit]` | Submit form |
| 7 | Google | link | → `/auth/google/redirect` |
| 8 | GitHub | link | → `/auth/github/redirect` |
| 9 | Đăng ký | link | → `/register` |
| 10 | Về trang chủ | link | → `/index` |
| 11 | Chuyển giao diện sáng / tối | button | Ảnh hưởng hiển thị, không ảnh hưởng nghiệp vụ |
| 12 | Trường ẩn `_token` | `input[type=hidden]` | CSRF token (đặc trưng Laravel) |
| 13 | Modal "Xác nhận" (Huỷ / Xác nhận) | dialog ẩn | ❓ Chưa rõ kịch bản kích hoạt trên trang login |

---

## 4. Phân tích luồng

### 4.1 Happy Path — HP-LOGIN-01: Đăng nhập nội bộ thành công

1. Người dùng truy cập `https://anhtester.com/login`.
2. Hệ thống hiển thị form đăng nhập kèm reCAPTCHA.
3. Người dùng nhập Email hợp lệ đã đăng ký.
4. Người dùng nhập đúng Mật khẩu.
5. Người dùng tích ô reCAPTCHA và vượt qua thử thách.
6. (Tuỳ chọn) Tích "Ghi nhớ đăng nhập".
7. Người dùng nhấn **Đăng nhập**.
8. Hệ thống xác thực thành công → tạo session → điều hướng tới **trang Student Dashboard** và hiển thị trạng thái đã đăng nhập.

### 4.2 Alternate Path (luồng thay thế — vẫn đạt được mục tiêu)

- **AP-LOGIN-01 — Đăng nhập qua Google:** nhấn **Google** → chuyển sang màn hình xác thực của Google → cấp quyền → quay lại LMS ở trạng thái đã đăng nhập.
- **AP-LOGIN-02 — Đăng nhập qua GitHub:** tương tự AP-LOGIN-01 với GitHub.
- **AP-LOGIN-03 — Ghi nhớ đăng nhập:** tích checkbox → đóng trình duyệt → mở lại vẫn còn phiên. Phiên được giữ **vĩnh viễn trên trình duyệt đó**, chỉ mất khi người dùng đăng xuất hoặc xoá cookie.
- **AP-LOGIN-04 — Hiện/ẩn mật khẩu:** nhấn icon con mắt để kiểm tra mật khẩu đã gõ trước khi submit, sau đó ẩn lại.
- **AP-LOGIN-05 — Quên mật khẩu:** rẽ nhánh sang `/forgot-password`, đặt lại mật khẩu, quay lại đăng nhập bằng mật khẩu mới. Luồng này **chỉ áp dụng khi tài khoản không bị khoá** (xem EP-LOGIN-16).
- **AP-LOGIN-06 — Chưa có tài khoản:** rẽ nhánh sang `/register`, đăng ký xong quay lại đăng nhập.
- **AP-LOGIN-07 — Đã có phiên đăng nhập:** người dùng đang đăng nhập mà truy cập `/login` → ❓ kỳ vọng tự điều hướng về dashboard thay vì hiển thị lại form.
- **AP-LOGIN-08 — Deep link:** truy cập trang yêu cầu đăng nhập → bị đẩy về `/login` → sau khi đăng nhập ❓ kỳ vọng quay lại đúng trang đích ban đầu.

### 4.3 Exception Path (luồng lỗi / bị từ chối)

- **EP-LOGIN-01 — Bỏ trống Email:** chặn submit, hiển thị thông báo **"Bạn không được bỏ trống"**.
- **EP-LOGIN-02 — Bỏ trống Mật khẩu:** chặn submit, hiển thị thông báo **"Bạn không được bỏ trống"**.
- **EP-LOGIN-03 — Email sai định dạng** (`abc`, `abc@`, `a@b`, có khoảng trắng): báo lỗi định dạng, không gửi request.
- **EP-LOGIN-04 — Email chưa đăng ký:** hiển thị thông báo **"Email hoặc mật khẩu không đúng"** — không tiết lộ email có tồn tại hay không.
- **EP-LOGIN-05 — Đúng email, sai mật khẩu:** hiển thị đúng một thông báo giống EP-LOGIN-04: **"Email hoặc mật khẩu không đúng"**.
- **EP-LOGIN-06 — Không tích reCAPTCHA:** chặn đăng nhập, hiển thị thông báo **"Vui lòng xác nhận bạn không phải là robot."**
- **EP-LOGIN-07 — reCAPTCHA hết hạn** (chờ quá 2 phút rồi mới submit): báo lỗi và yêu cầu xác thực lại; ❓ kỳ vọng không mất dữ liệu đã nhập.
- **EP-LOGIN-08 — Sai thông tin đăng nhập quá 5 lần:** bộ đếm tính **theo tài khoản** (không theo IP/trình duyệt); đăng nhập đúng giữa chừng sẽ **reset bộ đếm về 0**. Khi vượt 5 lần sai, tài khoản bị **khoá 1 giờ** và hệ thống hiển thị: **"Tài khoản của bạn đang tạm khóa. Hãy quay lại sau XX giờ YY phút"** (XX/YY là thời gian còn lại). Trong thời gian khoá, nhập đúng mật khẩu vẫn không đăng nhập được.
- **EP-LOGIN-09 — Tài khoản chưa kích hoạt hoặc bị khoá bởi quản trị viên:** ❓ thông báo và hướng xử lý chưa xác định.
- **EP-LOGIN-10 — Huỷ cấp quyền tại Google/GitHub:** quay về `/login`, hiển thị thông báo huỷ, không tạo session.
- **EP-LOGIN-11 — Email SSO trùng email tài khoản nội bộ:** ❓ chưa rõ hệ thống gộp tài khoản (link account) hay báo lỗi.
- **EP-LOGIN-12 — CSRF token hết hạn** (tab mở quá lâu): báo lỗi phiên hết hạn (HTTP 419), yêu cầu tải lại trang.
- **EP-LOGIN-13 — Mất kết nối mạng / server lỗi khi submit:** báo lỗi thân thiện, không treo nút Đăng nhập vô thời hạn.
- **EP-LOGIN-14 — Nhấn Đăng nhập nhiều lần liên tiếp:** vô hiệu hoá nút hoặc chống submit trùng, không tạo nhiều request/nhiều session.
- **EP-LOGIN-15 — Nhập chuỗi tấn công (SQL Injection, XSS) vào Email/Mật khẩu:** xử lý an toàn, không lộ lỗi hệ thống, không thực thi script.
- **EP-LOGIN-16 — Đặt lại mật khẩu khi tài khoản đang bị khoá:** chức năng "Quên mật khẩu" bị chặn, hiển thị đúng thông báo khoá kèm thời gian còn lại như EP-LOGIN-08. Người dùng **không** có cách tự mở khoá sớm, phải chờ hết 1 giờ.

---

## 5. Business Rules

| ID | Quy tắc | Nguồn |
|---|---|---|
| BR-LOGIN-01 | Email và Mật khẩu là bắt buộc | Quan sát UI (dấu `*`) |
| BR-LOGIN-02 | Email phải đúng định dạng chuẩn | `input[type=email]` |
| BR-LOGIN-03 | Phải vượt qua reCAPTCHA mới được submit | Quan sát UI |
| BR-LOGIN-04 | Phiên reCAPTCHA hết hiệu lực sau 2 phút | Cảnh báo hiển thị trên trang |
| BR-LOGIN-05 | Sai email hoặc sai mật khẩu đều trả về đúng một thông báo: "Email hoặc mật khẩu không đúng" | Xác nhận Q2 |
| BR-LOGIN-06 | Mật khẩu mặc định hiển thị dạng ẩn, chỉ hiện khi người dùng chủ động bật | Quan sát UI |
| BR-LOGIN-07 | Mọi request đăng nhập phải kèm CSRF token hợp lệ | Trường ẩn `_token` |
| BR-LOGIN-08 | "Ghi nhớ đăng nhập" giữ phiên **vĩnh viễn** trên trình duyệt đã tích chọn | Xác nhận Q4 |
| BR-LOGIN-09 | Email không phân biệt hoa/thường khi đối chiếu | ❓ Giả định — cần xác nhận |
| BR-LOGIN-10 | Mật khẩu **phân biệt** hoa/thường | Giả định theo chuẩn |
| BR-LOGIN-11 | Đăng nhập sai quá **5 lần** → khoá tài khoản **1 giờ** | Xác nhận Q3 |
| BR-LOGIN-12 | Trường bắt buộc bị bỏ trống → hiển thị thông báo đỏ **"Bạn không được bỏ trống"** ngay **dưới từng ô** đang trống | Xác nhận Q2, Q14 |
| BR-LOGIN-13 | Chưa xác thực reCAPTCHA → thông báo "Vui lòng xác nhận bạn không phải là robot." | Xác nhận Q2 |
| BR-LOGIN-14 | Đăng nhập thành công → điều hướng tới trang Student Dashboard | Xác nhận Q1 |
| BR-LOGIN-15 | Bộ đếm số lần sai tính **theo tài khoản**; đăng nhập đúng sẽ reset bộ đếm về 0 | Xác nhận Q11 |
| BR-LOGIN-16 | Thông báo khoá: "Tài khoản của bạn đang tạm khóa. Hãy quay lại sau XX giờ YY phút" — XX/YY là thời gian còn lại, giảm dần theo thời gian thực | Xác nhận Q12 |
| BR-LOGIN-17 | Tài khoản đang bị khoá thì **không** đặt lại mật khẩu được; hiển thị đúng thông báo khoá của BR-LOGIN-16 | Xác nhận Q13 |
| BR-LOGIN-18 | Không có cơ chế mở khoá sớm (người dùng lẫn quản trị viên); tài khoản chỉ tự mở sau đủ 1 giờ | Xác nhận Q16 |
| BR-LOGIN-19 | Hệ thống **không** gửi email/thông báo cho chủ tài khoản khi tài khoản bị khoá | Xác nhận Q15 |

## 6. Yêu cầu phi chức năng cần kiểm thử

| Nhóm | Nội dung |
|---|---|
| Bảo mật | Toàn trang chạy HTTPS; mật khẩu không xuất hiện trong URL/log/response; chống CSRF; chống brute force; cookie session có `HttpOnly` + `Secure` |
| Hiệu năng | Trang tải < 3s; phản hồi submit < 2s ở điều kiện mạng bình thường |
| Khả dụng | Điều hướng bằng bàn phím (Tab, Enter để submit); label gắn đúng ô nhập; thông báo lỗi đọc được bởi screen reader |
| Tương thích | Chrome / Edge / Firefox / Safari; desktop, tablet, mobile (responsive) |
| Giao diện | Hiển thị đúng ở cả chế độ sáng và tối; tiếng Việt đủ dấu, không vỡ layout |

---

## 7. Điểm mơ hồ / thiếu sót cần làm rõ

### 7.1 Đã được xác nhận (2026-09-08)

| # | Câu hỏi | Câu trả lời |
|---|---|---|
| Q1 | Sau khi đăng nhập thành công, hệ thống điều hướng tới trang nào? | ✅ Trang **Student Dashboard** |
| Q2 | Nội dung chính xác của từng thông báo lỗi? | ✅ Bỏ trống: "Bạn không được bỏ trống" · Sai email/mật khẩu: "Email hoặc mật khẩu không đúng" · Chưa tích reCAPTCHA: "Vui lòng xác nhận bạn không phải là robot." |
| Q3 | Có khoá tài khoản khi đăng nhập sai nhiều lần không? | ✅ Sai quá **5 lần** → khoá **1 giờ** |
| Q4 | "Ghi nhớ đăng nhập" giữ phiên bao lâu? | ✅ **Vĩnh viễn** trên trình duyệt đã tích chọn |
| Q11 | Bộ đếm 5 lần sai tính theo tài khoản hay IP? Có reset không? | ✅ Theo **tài khoản**; đăng nhập đúng giữa chừng **reset bộ đếm về 0** |
| Q12 | Thông báo khi tài khoản đang bị khoá? | ✅ "Tài khoản của bạn đang tạm khóa. Hãy quay lại sau **XX giờ YY phút**" (thời gian còn lại) |
| Q13 | Đang bị khoá có tự mở khoá bằng "Quên mật khẩu" được không? | ✅ **Không**. Cố tình đặt lại mật khẩu sẽ nhận đúng thông báo khoá ở Q12 |
| Q14 | Thông báo "Bạn không được bỏ trống" hiển thị ở đâu? | ✅ Thông báo **màu đỏ dưới từng ô** đang bỏ trống |
| Q15 | Khi tài khoản bị khoá, có gửi email cảnh báo cho chủ tài khoản không? | ✅ **Không** |
| Q16 | Quản trị viên có mở khoá sớm trước 1 giờ được không? | ✅ **Không** |

> ⚠️ **Hai rủi ro cần lưu ý (không chặn việc kiểm thử — vẫn test theo đúng yêu cầu hiện tại):**
>
> 1. **Phiên "ghi nhớ vĩnh viễn"** đồng nghĩa cookie đăng nhập không bao giờ hết hạn. Trên máy dùng chung hoặc máy bị mất, người khác vào thẳng tài khoản. Đề nghị cân nhắc đặt thời hạn tối đa (ví dụ 30–90 ngày) và cho phép đăng xuất khỏi mọi thiết bị.
> 2. **Khoá tài khoản theo email, không có đường mở khoá sớm và không có email cảnh báo** (BR-LOGIN-15, 18, 19): người khác chỉ cần biết email của học viên là có thể cố ý nhập sai 6 lần để khoá tài khoản đó 1 giờ, lặp lại liên tục — nạn nhân không được báo và không ai mở khoá giúp được. Đề nghị cân nhắc bổ sung ngưỡng theo IP, email cảnh báo, hoặc cho quản trị viên mở khoá.

### 7.2 Còn tồn đọng

| # | Câu hỏi | Ảnh hưởng nếu không có câu trả lời |
|---|---|---|
| Q5 | Tài khoản mới đăng ký có phải xác thực email trước khi đăng nhập không? | Ảnh hưởng EP-LOGIN-09 |
| Q6 | Khi email SSO trùng tài khoản nội bộ, hệ thống gộp tài khoản hay báo lỗi? | Ảnh hưởng EP-LOGIN-11 |
| Q7 | Ràng buộc độ dài tối đa/tối thiểu của Email và Mật khẩu? | Không thiết kế được Edge case biên |
| Q8 | Modal "Xác nhận" trên trang login phục vụ kịch bản nào? | Có thành phần UI chưa xác định nghiệp vụ |
| Q9 | Truy cập `/login` khi đã đăng nhập thì hành vi ra sao? | Ảnh hưởng AP-LOGIN-07 |
| Q10 | Có ghi log/audit lần đăng nhập, có cảnh báo đăng nhập bất thường không? | Phạm vi test bảo mật |

## 8. Test Condition đề xuất (đầu vào cho bước sinh Test Case)

| ID | Test Condition | Loại | Ưu tiên |
|---|---|---|---|
| TCD-LOGIN-01 | Đăng nhập thành công với email + mật khẩu hợp lệ | Happy | High |
| TCD-LOGIN-02 | Đăng nhập thành công qua Google / GitHub | Happy | Medium |
| TCD-LOGIN-03 | Kiểm tra trường bắt buộc (bỏ trống email / mật khẩu / cả hai) | Negative | High |
| TCD-LOGIN-04 | Kiểm tra định dạng email không hợp lệ | Negative | High |
| TCD-LOGIN-05 | Sai mật khẩu / email không tồn tại — thông báo lỗi chung | Negative | High |
| TCD-LOGIN-06 | Submit khi chưa xác thực reCAPTCHA | Negative | High |
| TCD-LOGIN-07 | reCAPTCHA hết hạn sau 2 phút | Edge | Medium |
| TCD-LOGIN-08 | Đăng nhập sai liên tiếp nhiều lần (brute force / lockout) | Negative | High |
| TCD-LOGIN-09 | Chức năng "Ghi nhớ đăng nhập" và thời hạn phiên | Edge | Medium |
| TCD-LOGIN-10 | Ẩn/hiện mật khẩu bằng icon con mắt | Happy | Low |
| TCD-LOGIN-11 | Điều hướng các link: Quên mật khẩu, Đăng ký, Về trang chủ | Happy | Medium |
| TCD-LOGIN-12 | Giá trị biên: email/mật khẩu cực dài, khoảng trắng đầu-cuối, ký tự Unicode | Edge | Medium |
| TCD-LOGIN-13 | Phân biệt hoa/thường: email không phân biệt, mật khẩu phân biệt | Edge | Medium |
| TCD-LOGIN-14 | Chuỗi tấn công SQLi / XSS trong ô nhập | Negative | High |
| TCD-LOGIN-15 | CSRF token hết hạn / thao tác trên tab để lâu | Negative | Medium |
| TCD-LOGIN-16 | Mất mạng hoặc server lỗi khi submit | Negative | Medium |
| TCD-LOGIN-17 | Nhấn Đăng nhập nhiều lần (double submit) | Edge | Medium |
| TCD-LOGIN-18 | Truy cập `/login` khi đã có phiên đăng nhập | Edge | Medium |
| TCD-LOGIN-19 | Deep link: đăng nhập xong có quay lại trang đích ban đầu | Edge | Medium |
| TCD-LOGIN-20 | Điều hướng bàn phím, Enter để submit, thứ tự Tab | Usability | Low |
| TCD-LOGIN-21 | Hiển thị responsive trên mobile/tablet, chế độ sáng/tối | UI | Low |
| TCD-LOGIN-22 | Mật khẩu không lộ trong URL/network/log; cookie Secure + HttpOnly | Security | High |
| TCD-LOGIN-23 | Đăng nhập đúng giữa chừng reset bộ đếm số lần sai về 0 | Negative | High |
| TCD-LOGIN-24 | Khoá gắn với tài khoản, không gắn với IP/trình duyệt | Negative | High |
| TCD-LOGIN-25 | Đặt lại mật khẩu bị chặn khi tài khoản đang bị khoá | Negative | High |
| TCD-LOGIN-26 | Thời gian còn lại trong thông báo khoá giảm dần đúng thực tế | Edge | Medium |

**Tỷ lệ:** 5/26 Happy · 12/26 Negative · 9/26 Edge & khác → vượt mức tối thiểu 30% Negative/Edge của bước sinh Test Case.

---

## 9. Bước tiếp theo

Chạy `/skills-testcases-generate` cho module LOGIN — bộ Test Case sẽ dựa trực tiếp trên các luồng HP/AP/EP và danh sách Test Condition ở mục 8 của tài liệu này.
