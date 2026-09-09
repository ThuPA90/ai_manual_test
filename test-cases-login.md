# Bộ Test Case Manual — Chức năng Đăng nhập (Module LOGIN)

| Hạng mục | Nội dung |
|---|---|
| Module | LOGIN |
| URL | https://anhtester.com/login |
| Hệ thống | ANHTESTER LMS |
| Ngày tạo | 2026-09-08 |
| Nguồn | [requirements-analysis-login.md](requirements-analysis-login.md) — luồng HP/AP/EP và Test Condition mục 8 |
| Tổng số Test Case | 41 (12 Happy · 19 Negative · 10 Edge) — Negative + Edge chiếm **70,7%** |

## Dữ liệu test dùng chung

| Ký hiệu | Giá trị |
|---|---|
| `EMAIL_HOPLE` | `phamthunb90@gmail.com` |
| `MK_HOPLE` | Mật khẩu hợp lệ của tài khoản test — **không ghi trong tài liệu**, lấy từ nơi lưu trữ bí mật của nhóm test |
| `EMAIL_CHUA_DK` | `khongtontai_20260908@gmail.com` |
| `MK_SAI` | `SaiMatKhau@123` |

**Tiền điều kiện chung (áp dụng cho mọi TC, không lặp lại):** máy test có Internet ổn định; trình duyệt bật JavaScript và cookie; đã xoá cookie/session của `anhtester.com` trước khi bắt đầu; tài khoản test đang ở trạng thái active, không bị khoá.

---

## 1. Happy Path & Alternate Path

| Mã TC | Tiêu đề | Tiền điều kiện | Các bước thực hiện | Dữ liệu test | Kết quả mong đợi | Ưu tiên |
|---|---|---|---|---|---|---|
| TC_LOGIN_001 | Đăng nhập thành công bằng email và mật khẩu hợp lệ | Chưa đăng nhập | 1. Mở `/login`<br>2. Nhập Email<br>3. Nhập Mật khẩu<br>4. Tích ô reCAPTCHA<br>5. Nhấn **Đăng nhập** | `EMAIL_HOPLE` / `MK_HOPLE` | Đăng nhập thành công, hệ thống điều hướng tới trang **Student Dashboard**, hiển thị tên/avatar người dùng đã đăng nhập | High |
| TC_LOGIN_002 | Hiển thị đầy đủ các thành phần trên màn hình đăng nhập | Chưa đăng nhập | 1. Mở `/login`<br>2. Quan sát toàn màn hình | — | Hiển thị đủ: ô Email (*), ô Mật khẩu (*) kèm icon con mắt, checkbox "Ghi nhớ đăng nhập" (mặc định bỏ chọn), link "Quên mật khẩu?", widget reCAPTCHA, nút "Đăng nhập", nút Google, nút GitHub, link "Đăng ký", link "Về trang chủ" | Medium |
| TC_LOGIN_003 | Đăng nhập thành công qua Google | Đã có tài khoản Google liên kết | 1. Mở `/login`<br>2. Nhấn **Google**<br>3. Chọn tài khoản Google và cấp quyền | Tài khoản Google đã liên kết | Chuyển sang màn hình xác thực Google, sau khi cấp quyền quay lại LMS ở trạng thái đã đăng nhập và vào **Student Dashboard** | Medium |
| TC_LOGIN_004 | Đăng nhập thành công qua GitHub | Đã có tài khoản GitHub liên kết | 1. Mở `/login`<br>2. Nhấn **GitHub**<br>3. Đăng nhập GitHub và cấp quyền | Tài khoản GitHub đã liên kết | Quay lại LMS ở trạng thái đã đăng nhập, vào **Student Dashboard** | Medium |
| TC_LOGIN_005 | "Ghi nhớ đăng nhập" giữ phiên sau khi đóng và mở lại trình duyệt | Chưa đăng nhập | 1. Đăng nhập thành công có tích "Ghi nhớ đăng nhập"<br>2. Đóng hoàn toàn trình duyệt<br>3. Mở lại trình duyệt, truy cập `anhtester.com` | `EMAIL_HOPLE` / `MK_HOPLE` | Người dùng vẫn ở trạng thái đã đăng nhập, không bị yêu cầu nhập lại thông tin | High |
| TC_LOGIN_006 | Phiên "ghi nhớ" tồn tại vĩnh viễn theo thời gian | Đã đăng nhập có tích "Ghi nhớ đăng nhập" | 1. Chỉnh đồng hồ hệ thống tiến 1 năm (hoặc kiểm tra `Expires` của cookie phiên)<br>2. Truy cập lại `anhtester.com` | Cookie phiên | Cookie không hết hạn, người dùng vẫn đăng nhập — đúng yêu cầu BR-LOGIN-08 | Medium |
| TC_LOGIN_007 | Đăng xuất làm mất phiên "ghi nhớ đăng nhập" | Đã đăng nhập có tích "Ghi nhớ đăng nhập" | 1. Nhấn **Đăng xuất**<br>2. Đóng và mở lại trình duyệt<br>3. Truy cập trang Student Dashboard | — | Phiên bị xoá, hệ thống điều hướng về `/login`, không tự động đăng nhập lại | High |
| TC_LOGIN_008 | Hiện/ẩn mật khẩu bằng icon con mắt | Đang ở màn hình `/login` | 1. Nhập mật khẩu<br>2. Nhấn icon con mắt<br>3. Nhấn icon con mắt lần nữa | `MK_HOPLE` | Bước 2: mật khẩu hiển thị dạng văn bản thường<br>Bước 3: mật khẩu trở lại dạng ẩn (dấu chấm) | Low |
| TC_LOGIN_009 | Điều hướng tới trang Quên mật khẩu | Đang ở màn hình `/login` | 1. Nhấn link **Quên mật khẩu?** | — | Chuyển tới `/forgot-password`, hiển thị đúng màn hình quên mật khẩu | Medium |
| TC_LOGIN_010 | Điều hướng tới trang Đăng ký | Đang ở màn hình `/login` | 1. Nhấn link **Đăng ký** | — | Chuyển tới `/register`, hiển thị đúng màn hình đăng ký | Medium |
| TC_LOGIN_011 | Điều hướng về trang chủ | Đang ở màn hình `/login` | 1. Nhấn link **Về trang chủ** | — | Chuyển tới `/index`, hiển thị trang chủ | Low |
| TC_LOGIN_012 | Submit form bằng phím Enter | Đang ở màn hình `/login` | 1. Nhập Email và Mật khẩu<br>2. Tích reCAPTCHA<br>3. Đặt con trỏ trong ô Mật khẩu, nhấn phím **Enter** | `EMAIL_HOPLE` / `MK_HOPLE` | Form được submit, đăng nhập thành công như khi nhấn nút "Đăng nhập" | Low |

## 2. Negative Case

| Mã TC | Tiêu đề | Tiền điều kiện | Các bước thực hiện | Dữ liệu test | Kết quả mong đợi | Ưu tiên |
|---|---|---|---|---|---|---|
| TC_LOGIN_013 | Bỏ trống cả Email và Mật khẩu | Đang ở màn hình `/login` | 1. Để trống cả 2 ô<br>2. Tích reCAPTCHA<br>3. Nhấn **Đăng nhập** | (trống) | Không đăng nhập được; hiển thị thông báo **màu đỏ "Bạn không được bỏ trống"** ngay **dưới cả 2 ô** Email và Mật khẩu, vẫn ở lại `/login` | High |
| TC_LOGIN_014 | Bỏ trống Email | Đang ở màn hình `/login` | 1. Để trống Email<br>2. Nhập Mật khẩu<br>3. Tích reCAPTCHA<br>4. Nhấn **Đăng nhập** | Email: trống · `MK_HOPLE` | Không đăng nhập được; thông báo đỏ **"Bạn không được bỏ trống"** hiển thị **dưới ô Email**; ô Mật khẩu **không** hiện thông báo lỗi | High |
| TC_LOGIN_015 | Bỏ trống Mật khẩu | Đang ở màn hình `/login` | 1. Nhập Email<br>2. Để trống Mật khẩu<br>3. Tích reCAPTCHA<br>4. Nhấn **Đăng nhập** | `EMAIL_HOPLE` · Mật khẩu: trống | Không đăng nhập được; thông báo đỏ **"Bạn không được bỏ trống"** hiển thị **dưới ô Mật khẩu**; ô Email **không** hiện thông báo lỗi | High |
| TC_LOGIN_016 | Nhập Email sai định dạng | Đang ở màn hình `/login` | Lặp lại với từng giá trị:<br>1. Nhập Email sai định dạng<br>2. Nhập Mật khẩu<br>3. Tích reCAPTCHA<br>4. Nhấn **Đăng nhập** | `abc` · `abc@` · `abc@gmail` · `@gmail.com` · `abc def@gmail.com` | Không gửi request đăng nhập, hiển thị thông báo lỗi định dạng email, con trỏ tập trung vào ô Email | High |
| TC_LOGIN_017 | Đăng nhập bằng email chưa đăng ký | Đang ở màn hình `/login` | 1. Nhập email chưa đăng ký<br>2. Nhập mật khẩu bất kỳ<br>3. Tích reCAPTCHA<br>4. Nhấn **Đăng nhập** | `EMAIL_CHUA_DK` / `MK_SAI` | Không đăng nhập được, hiển thị **"Email hoặc mật khẩu không đúng"** — không tiết lộ email chưa tồn tại | High |
| TC_LOGIN_018 | Đúng email nhưng sai mật khẩu | Đang ở màn hình `/login` | 1. Nhập email hợp lệ<br>2. Nhập mật khẩu sai<br>3. Tích reCAPTCHA<br>4. Nhấn **Đăng nhập** | `EMAIL_HOPLE` / `MK_SAI` | Không đăng nhập được, hiển thị **"Email hoặc mật khẩu không đúng"** — thông báo giống hệt TC_LOGIN_017 | High |
| TC_LOGIN_019 | Submit khi chưa xác thực reCAPTCHA | Đang ở màn hình `/login` | 1. Nhập Email và Mật khẩu hợp lệ<br>2. **Không** tích reCAPTCHA<br>3. Nhấn **Đăng nhập** | `EMAIL_HOPLE` / `MK_HOPLE` | Không đăng nhập được, hiển thị **"Vui lòng xác nhận bạn không phải là robot."** | High |
| TC_LOGIN_020 | Khoá tài khoản sau khi đăng nhập sai quá 5 lần | Tài khoản đang active, bộ đếm sai = 0 | 1. Đăng nhập sai mật khẩu 5 lần liên tiếp<br>2. Thực hiện lần đăng nhập sai thứ 6 | `EMAIL_HOPLE` / `MK_SAI` | 5 lần đầu: báo "Email hoặc mật khẩu không đúng"<br>Từ lần thứ 6: tài khoản bị **khoá 1 giờ**, hiển thị **"Tài khoản của bạn đang tạm khóa. Hãy quay lại sau 00 giờ 59 phút"** (hoặc thời gian còn lại tương ứng) | High |
| TC_LOGIN_021 | Nhập đúng mật khẩu trong lúc tài khoản đang bị khoá | Tài khoản vừa bị khoá ở TC_LOGIN_020, chưa qua 1 giờ | 1. Nhập email và **đúng** mật khẩu<br>2. Tích reCAPTCHA<br>3. Nhấn **Đăng nhập** | `EMAIL_HOPLE` / `MK_HOPLE` | Vẫn **không** đăng nhập được; hiển thị **"Tài khoản của bạn đang tạm khóa. Hãy quay lại sau XX giờ YY phút"** với XX/YY đúng bằng thời gian còn lại của phiên khoá | High |
| TC_LOGIN_022 | Đăng nhập lại sau khi hết 1 giờ khoá | Tài khoản bị khoá đã quá 1 giờ | 1. Chờ hết 1 giờ kể từ lúc bị khoá<br>2. Nhập email và đúng mật khẩu<br>3. Tích reCAPTCHA và nhấn **Đăng nhập** | `EMAIL_HOPLE` / `MK_HOPLE` | Tài khoản tự mở khoá, đăng nhập thành công, vào **Student Dashboard** | High |
| TC_LOGIN_023 | Chống SQL Injection ở ô Email và Mật khẩu | Đang ở màn hình `/login` | 1. Nhập chuỗi tấn công vào ô Email, sau đó thử ở ô Mật khẩu<br>2. Tích reCAPTCHA<br>3. Nhấn **Đăng nhập** | `' OR '1'='1` · `admin'--` · `'; DROP TABLE users;--` | Không đăng nhập được, hiển thị lỗi xác thực thông thường, **không** lộ thông báo lỗi cơ sở dữ liệu/stack trace, dữ liệu hệ thống nguyên vẹn | High |
| TC_LOGIN_024 | Chống XSS ở ô Email và Mật khẩu | Đang ở màn hình `/login` | 1. Nhập chuỗi script vào ô nhập<br>2. Tích reCAPTCHA<br>3. Nhấn **Đăng nhập** | `<script>alert(1)</script>` · `"><img src=x onerror=alert(1)>` | Script không được thực thi, không hiện hộp thoại alert, chuỗi được hiển thị dạng văn bản thuần (đã escape) | High |
| TC_LOGIN_025 | Đăng nhập khi CSRF token hết hạn | Đang ở màn hình `/login` | 1. Mở `/login` và để nguyên tab trong thời gian dài (vượt thời hạn session)<br>2. Nhập thông tin hợp lệ và submit | `EMAIL_HOPLE` / `MK_HOPLE` | Hệ thống từ chối request (HTTP 419 / lỗi phiên hết hạn), hiển thị thông báo thân thiện yêu cầu tải lại trang, không đăng nhập được bằng token cũ | Medium |
| TC_LOGIN_026 | Submit khi mất kết nối mạng | Đang ở màn hình `/login` | 1. Nhập thông tin hợp lệ và tích reCAPTCHA<br>2. Ngắt kết nối mạng<br>3. Nhấn **Đăng nhập** | `EMAIL_HOPLE` / `MK_HOPLE` | Hiển thị thông báo lỗi kết nối thân thiện, nút "Đăng nhập" không bị treo trạng thái loading vô hạn, người dùng thử lại được sau khi có mạng | Medium |
| TC_LOGIN_027 | Truy cập trang Student Dashboard khi chưa đăng nhập | Chưa đăng nhập, đã xoá cookie | 1. Dán trực tiếp URL trang Student Dashboard vào thanh địa chỉ<br>2. Nhấn Enter | URL dashboard | Hệ thống chặn truy cập và điều hướng về `/login`, không hiển thị bất kỳ dữ liệu học viên nào | High |
| TC_LOGIN_038 | Đăng nhập đúng giữa chừng làm reset bộ đếm số lần sai | Tài khoản active, bộ đếm sai = 0 | 1. Đăng nhập sai 4 lần liên tiếp<br>2. Lần thứ 5 nhập **đúng** mật khẩu → đăng nhập thành công<br>3. Đăng xuất<br>4. Tiếp tục đăng nhập sai 5 lần | `EMAIL_HOPLE` với `MK_SAI` và `MK_HOPLE` | Bước 2 thành công và bộ đếm reset về 0; ở bước 4, tài khoản **chưa** bị khoá trong 5 lần sai đầu — chỉ bị khoá từ lần sai thứ 6 tính từ sau lần đăng nhập đúng | High |
| TC_LOGIN_039 | Khoá tài khoản áp dụng theo tài khoản, không theo IP/trình duyệt | Tài khoản A vừa bị khoá; có sẵn tài khoản B active | 1. Từ trình duyệt/máy khác (IP khác), đăng nhập tài khoản A bằng mật khẩu đúng<br>2. Trên cùng máy đó, đăng nhập tài khoản B bằng mật khẩu đúng | Tài khoản A (đang khoá) · Tài khoản B (active) | Bước 1: tài khoản A vẫn bị chặn, hiện thông báo khoá kèm thời gian còn lại<br>Bước 2: tài khoản B đăng nhập bình thường — chứng tỏ khoá gắn với tài khoản, không gắn với IP | High |
| TC_LOGIN_040 | Thời gian còn lại trong thông báo khoá giảm dần chính xác | Tài khoản vừa bị khoá | 1. Ghi lại thông báo khoá ngay khi bị khoá<br>2. Chờ 10 phút, thử đăng nhập lại và ghi lại thông báo<br>3. Chờ tiếp 20 phút, thử lại | `EMAIL_HOPLE` / `MK_HOPLE` | Thời gian còn lại giảm đúng theo thực tế (≈59 phút → ≈49 phút → ≈29 phút), định dạng "XX giờ YY phút" hiển thị đúng, không hiện số âm hay giá trị rỗng | Medium |
| TC_LOGIN_041 | Đặt lại mật khẩu khi tài khoản đang bị khoá | Tài khoản đang bị khoá, chưa hết 1 giờ | 1. Mở `/login`, nhấn **Quên mật khẩu?**<br>2. Nhập email của tài khoản đang bị khoá<br>3. Gửi yêu cầu đặt lại mật khẩu | `EMAIL_HOPLE` | Hệ thống từ chối, hiển thị **"Tài khoản của bạn đang tạm khóa. Hãy quay lại sau XX giờ YY phút"**; không gửi email đặt lại mật khẩu; tài khoản vẫn bị khoá đủ 1 giờ | High |

## 3. Edge Case

| Mã TC | Tiêu đề | Tiền điều kiện | Các bước thực hiện | Dữ liệu test | Kết quả mong đợi | Ưu tiên |
|---|---|---|---|---|---|---|
| TC_LOGIN_028 | reCAPTCHA hết hạn sau 2 phút rồi mới submit | Đang ở màn hình `/login` | 1. Nhập Email, Mật khẩu hợp lệ<br>2. Tích reCAPTCHA<br>3. Chờ hơn 2 phút không thao tác<br>4. Nhấn **Đăng nhập** | `EMAIL_HOPLE` / `MK_HOPLE` | Hệ thống báo reCAPTCHA đã hết hạn và yêu cầu xác thực lại; dữ liệu Email đã nhập không bị xoá; xác thực lại rồi submit thì đăng nhập thành công | Medium |
| TC_LOGIN_029 | Email có khoảng trắng ở đầu và cuối | Đang ở màn hình `/login` | 1. Nhập email kèm khoảng trắng<br>2. Nhập mật khẩu đúng<br>3. Tích reCAPTCHA và submit | `"  phamthunb90@gmail.com  "` / `MK_HOPLE` | Hệ thống tự cắt khoảng trắng (trim) và đăng nhập thành công — hoặc báo lỗi rõ ràng. **Không** được trả về lỗi hệ thống | Medium |
| TC_LOGIN_030 | Email viết hoa/thường khác nhau | Đang ở màn hình `/login` | 1. Nhập email dạng viết hoa<br>2. Nhập mật khẩu đúng<br>3. Tích reCAPTCHA và submit | `PHAMTHUNB90@GMAIL.COM` / `MK_HOPLE` | Đăng nhập thành công — email không phân biệt hoa/thường (BR-LOGIN-09, cần đối chiếu kết quả thực tế) | Medium |
| TC_LOGIN_031 | Mật khẩu sai hoa/thường | Đang ở màn hình `/login` | 1. Nhập email đúng<br>2. Nhập mật khẩu đúng nhưng đảo hoa/thường<br>3. Tích reCAPTCHA và submit | `EMAIL_HOPLE` / `MK_HOPLE` đã đảo hoa ↔ thường toàn bộ ký tự chữ | Không đăng nhập được, báo "Email hoặc mật khẩu không đúng" — mật khẩu **phân biệt** hoa/thường | Medium |
| TC_LOGIN_032 | Nhập giá trị cực dài vào Email và Mật khẩu | Đang ở màn hình `/login` | 1. Nhập chuỗi 255 và 256 ký tự vào ô Email<br>2. Nhập chuỗi 500 ký tự vào ô Mật khẩu<br>3. Tích reCAPTCHA và submit | Chuỗi 255 / 256 / 500 ký tự | Hệ thống xử lý ổn định: hoặc chặn theo giới hạn độ dài kèm thông báo rõ ràng, hoặc báo lỗi xác thực thông thường. Không vỡ giao diện, không lỗi 500 | Medium |
| TC_LOGIN_033 | Dán (paste) mật khẩu từ clipboard | Đang ở màn hình `/login` | 1. Copy mật khẩu ra clipboard<br>2. Dán vào ô Mật khẩu bằng Ctrl+V<br>3. Tích reCAPTCHA và submit | `MK_HOPLE` | Ô mật khẩu cho phép dán, đăng nhập thành công (không chặn paste — chặn paste là lỗi khả dụng) | Low |
| TC_LOGIN_034 | Nhấn nút Đăng nhập nhiều lần liên tiếp | Đang ở màn hình `/login` | 1. Nhập thông tin hợp lệ, tích reCAPTCHA<br>2. Nhấn **Đăng nhập** liên tiếp 5 lần thật nhanh | `EMAIL_HOPLE` / `MK_HOPLE` | Chỉ 1 request đăng nhập được gửi, nút bị vô hiệu hoá sau lần nhấn đầu, không tạo nhiều phiên, không phát sinh lỗi | Medium |
| TC_LOGIN_035 | Truy cập `/login` khi đã đăng nhập | Đang đăng nhập trên trình duyệt | 1. Dán URL `/login` vào thanh địa chỉ<br>2. Nhấn Enter | — | Hệ thống điều hướng thẳng về **Student Dashboard** thay vì hiển thị lại form đăng nhập (kỳ vọng — cần đối chiếu thực tế, xem Q9) | Medium |
| TC_LOGIN_036 | Mật khẩu không bị lộ trên đường truyền và trong trình duyệt | Đang ở màn hình `/login`, đã mở DevTools | 1. Đăng nhập thành công<br>2. Kiểm tra tab Network: URL, query string, response<br>3. Kiểm tra cookie phiên trong tab Application | `EMAIL_HOPLE` / `MK_HOPLE` | Request đi qua HTTPS bằng phương thức POST; mật khẩu không xuất hiện trong URL/query string/response; cookie phiên có cờ `HttpOnly` và `Secure` | High |
| TC_LOGIN_037 | Hiển thị trên mobile và ở chế độ giao diện tối | Đang ở màn hình `/login` | 1. Mở `/login` trên độ phân giải mobile (375×812)<br>2. Bật chế độ giao diện tối<br>3. Thực hiện đăng nhập | `EMAIL_HOPLE` / `MK_HOPLE` | Bố cục không vỡ, không cuộn ngang; chữ tiếng Việt đủ dấu, đủ độ tương phản ở cả 2 chế độ; widget reCAPTCHA hiển thị đầy đủ; đăng nhập thành công | Low |

---

## 4. Thống kê

| Loại | Số lượng | Tỷ lệ |
|---|---|---|
| Happy Path & Alternate Path | 12 | 29,3% |
| Negative Case | 19 | 46,3% |
| Edge Case | 10 | 24,4% |
| **Tổng** | **41** | **100%** |
| Độ ưu tiên High | 20 | 48,8% |
| Độ ưu tiên Medium | 16 | 39,0% |
| Độ ưu tiên Low | 5 | 12,2% |

## 5. Ghi chú khi thực thi

- **Nhóm TC khoá tài khoản (TC_LOGIN_020, 021, 022, 038, 039, 040, 041)** đều làm khoá tài khoản 1 giờ. Nên gom chạy cuối cùng, dùng **tài khoản test riêng** cho từng ca, hoặc nhờ nhóm phát triển reset bộ đếm giữa các lần chạy để không mất thời gian chờ.
- **Không có cách mở khoá sớm** — cả người dùng lẫn quản trị viên đều không mở khoá được (BR-LOGIN-18). Vì vậy TC_LOGIN_022 bắt buộc phải chờ đủ 1 giờ; hãy bố trí chạy các TC khác trong lúc chờ.
- Khi chạy **TC_LOGIN_020**, kiểm tra thêm hộp thư của tài khoản test: theo BR-LOGIN-19, hệ thống **không** gửi email cảnh báo khi tài khoản bị khoá.
- **TC_LOGIN_039** cần thêm 1 tài khoản test phụ (tài khoản B) và 1 máy/mạng có IP khác (có thể dùng 4G điện thoại).
- **TC_LOGIN_040** kéo dài ~30 phút, nên chạy song song với các TC khác.
- **TC_LOGIN_006** cần quyền chỉnh đồng hồ hệ thống; nếu không, chỉ cần kiểm tra thuộc tính `Expires`/`Max-Age` của cookie phiên.
- Các TC còn phụ thuộc câu hỏi chưa được trả lời: **TC_LOGIN_035** (Q9 — hành vi khi đã đăng nhập mà vào `/login`) và **TC_LOGIN_030** (BR-LOGIN-09 — email có phân biệt hoa/thường không). Cần đối chiếu kết quả thực tế khi thực thi.
- Mật khẩu tài khoản test không được ghi trong tài liệu này; người thực thi lấy từ nơi lưu trữ bí mật của nhóm.
