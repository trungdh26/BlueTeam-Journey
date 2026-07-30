# OWASP (2)
## A04 - A05 - A08: Các lỗ hổng bảo mật web phổ biến

---

## 4. A04: CRYPTOGRAPHIC FAILURES

### Định nghĩa

> **Thất bại về mật mã hóa** xảy ra khi dữ liệu nhạy cảm không được bảo vệ đúng cách do **thiếu mã hóa, sử dụng thuật toán yếu, hoặc quản lý khóa kém**, khiến kẻ tấn công có thể đọc hoặc giả mạo dữ liệu.

### Các hình thức tấn công chính

| Hình thức | Mô tả | Hậu quả |
|-----------|-------|---------|
| **Không mã hóa dữ liệu** | Dữ liệu được lưu trữ hoặc truyền tải dạng văn bản rõ | Kẻ tấn công bắt được gói tin sẽ đọc trộm dữ liệu |
| **Thuật toán yếu/lỗi thời** | Sử dụng MD5, SHA1, DES - các thuật toán đã bị phá vỡ | Dễ bị tấn công va chạm (collision) hoặc giải mã ngược |
| **Tự chế mã hóa** | Tự viết thuật toán mã hóa thay vì dùng thư viện chuẩn | Chứa lỗ hổng logic, dễ bị khai thác |
| **Khóa mã hóa ngắn/yếu** | Khóa chỉ dài 4-8 ký tự, dễ đoán | Bị tấn công brute-force tìm ra khóa |
| **Lộ khóa mã hóa** | Nhúng khóa vào source code, file cấu hình | Kẻ tấn công có khóa sẽ giải mã toàn bộ dữ liệu |

### Ví dụ thực tế

Một ứng dụng ghi chú sử dụng mã hóa XOR với khóa 4 ký tự. Vì XOR có tính thuận nghịch (reversible) và khóa quá ngắn, kẻ tấn công có thể brute-force để tìm ra khóa và giải mã toàn bộ ghi chú của tất cả người dùng.

### Cách phòng chống

- Dùng thuật toán mã hóa được kiểm chứng: **AES-256-GCM** cho mã hóa, **bcrypt/Argon2** cho băm mật khẩu
- **Không tự chế** thuật toán mã hóa
- Khóa mã hóa phải dài, phức tạp, lưu trong **KMS** (Key Management System)
- Luôn sử dụng **HTTPS/TLS** để bảo vệ dữ liệu khi truyền tải
- **Không nhúng** khóa bí mật vào source code

---

## 5. A05: INJECTION

### Định nghĩa

> **Tiêm nhiễm** xảy ra khi dữ liệu đầu vào từ người dùng được **truyền thẳng** vào hệ thống có khả năng thực thi lệnh (database, shell, template engine) **mà không qua kiểm tra hoặc lọc**, cho phép kẻ tấn công thực thi mã tùy ý.

### Các hình thức tấn công chính

| Hình thức | Mô tả | Ví dụ |
|-----------|-------|-------|
| **SQL Injection** | Chèn câu lệnh SQL vào ô nhập liệu để truy vấn database | `' OR 1=1 --` bypass đăng nhập |
| **Command Injection** | Chèn lệnh hệ điều hành để điều khiển máy chủ | `; rm -rf /` xóa toàn bộ dữ liệu |
| **SSTI (Server-Side Template Injection)** | Chèn mã vào template engine (Jinja2, Twig) | `{{ config }}` xem cấu hình hệ thống |
| **XSS (Cross-Site Scripting)** | Chèn mã JavaScript vào trang web | `<script>alert(1)</script>` đánh cắp cookie |

### Ví dụ thực tế

Một ứng dụng dùng Jinja2 để render lời chào từ tên người dùng. Khi nhập `{{ self.__class__.__mro__[1].__subclasses__() }}`, ứng dụng chạy luôn câu lệnh này và trả về danh sách tất cả các class đang có trong bộ nhớ, giúp kẻ tấn công tìm cách đọc file hệ thống.

### Cách phòng chống

- Sử dụng **Prepared Statements** và **Parameterised Queries** cho SQL
- **Không ghép chuỗi** để tạo câu truy vấn hoặc lệnh shell
- Kiểm tra và ép kiểu dữ liệu đầu vào (validate & sanitize)
- Sử dụng API an toàn thay vì gọi shell trực tiếp
- Với template engine: **không cho phép người dùng nhập trực tiếp** vào logic render; nếu cần, phải lọc ký tự nguy hiểm

---

## 6. A08: SOFTWARE / DATA INTEGRITY FAILURES

### Định nghĩa

> **Thất bại về tính toàn vẹn** xảy ra khi ứng dụng **tin tưởng mù quáng** vào mã nguồn, bản cập nhật, hoặc dữ liệu từ bên ngoài **mà không xác minh** nguồn gốc hoặc tính toàn vẹn, cho phép kẻ tấn công chèn thành phần độc hại.

### Các hình thức tấn công chính

| Hình thức | Mô tả | Hậu quả |
|-----------|-------|---------|
| **Deserialization Attack** | Ứng dụng giải tuần tự hóa (unpickle) dữ liệu từ người dùng | Thực thi mã tùy ý trên máy chủ |
| **Supply Chain Attack** | Kẻ tấn công chèn mã độc vào bản cập nhật của thư viện/phần mềm | Hàng ngàn ứng dụng cài bản cập nhật đó sẽ bị nhiễm độc |
| **Typosquatting** | Tạo gói giả mạo có tên gần giống gói thật (ví dụ: `rlask` giả mạo `flask`) | Lập trình viên cài nhầm gói độc hại |
| **Tải script/config từ nguồn không tin cậy** | Ứng dụng tải và chạy file từ internet mà không kiểm tra | Máy chủ bị chiếm quyền kiểm soát |

### Ví dụ thực tế

Một ứng dụng Python nhận dữ liệu pickle từ người dùng và giải tuần tự hóa (deserialize) mà không kiểm tra. Kẻ tấn công tạo một payload pickle có chứa lệnh `cat flag.txt` và gửi lên. Ứng dụng giải mã và thực thi lệnh đó, trả về nội dung file flag cho kẻ tấn công.

### Cách phòng chống

- **Không deserialize** dữ liệu từ nguồn không tin cậy
- Xác minh tính toàn vẹn của bản cập nhật bằng **chữ ký số** hoặc **checksum** (SHA-256)
- Chỉ dùng các gói phần mềm từ nguồn chính thống (official repository)
- Kiểm tra kỹ tên gói trước khi cài đặt để tránh typosquatting
- Áp dụng kiểm soát nghiêm ngặt trong quy trình **CI/CD**, chỉ cho phép thành phần từ nguồn đã xác thực

---