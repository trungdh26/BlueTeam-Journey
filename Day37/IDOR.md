# IDOR theory (A01)

---

## 1. IDOR là gì?
IDOR = Insecure Direct Object Reference
Là lỗ hổng xảy ra khi ứng dụng cho phép người dùng truy cập trực tiếp vào tài nguyên (dữ liệu, file, thư mục) thông qua tham số do người dùng cung cấp
Nguyên nhân gốc: Hệ thống KHÔNG kiểm tra quyền truy cập trước khi trả dữ liệu về

---

## 2. Ví dụ đơn giản
URL: https://example.com/profile?user_id=123
Sửa thành: https://example.com/profile?user_id=124
Nếu thấy được thông tin của user 124 → Đã có IDOR

---

## 3. Nguyên nhân chính
Không kiểm tra quyền sở hữu trên server
Tin tưởng dữ liệu từ client (URL, form, cookie)
Thiếu ma trận phân quyền rõ ràng
Sử dụng ID tuần tự (1,2,3 → dễ đoán)

---

## 4. Hậu quả
Xem trộm dữ liệu người khác
Sửa thông tin người khác
Xóa dữ liệu người khác
Leo thang quyền từ user lên admin

---

## 5. Các hình thức xuất hiện của IDOR
Vị trí	Ví dụ
Query String	?user_id=123
Path URL	/api/users/123
POST Body	{"user_id": 123}
Cookie	session=user123
Header	X-User-ID: 123
File Upload	Tên file chứa ID

---

## 6. IDOR theo loại API
Method	Hacker làm được gì?
GET	Xem và copy dữ liệu
POST	Tạo dữ liệu giả mạo
PUT/PATCH	Sửa dữ liệu
DELETE	Xóa dữ liệu

---

## 7. Cách khai thác
Thử thủ công:
Tạo 2 tài khoản (A và B)
Lấy request của A, sửa ID thành ID của B
Nếu nhận được dữ liệu của A → có IDOR
Các tham số cần test:
id, user_id, profile_id, account_id, order_id
code, token, role, group
Kỹ thuật nâng cao:
Brute-force ID
Thử giá trị biên: 0, -1, NULL
Gửi mảng ID: ids[]=1&ids[]=2
Kết hợp Path Traversal

---

## 8. Cách phòng chống
Nguyên tắc vàng: Luôn kiểm tra quyền trên SERVER, không tin client.
Hành động	Giải thích
Kiểm tra quyền sở hữu	Trước khi trả data, kiểm tra user hiện tại có sở hữu ID đó không
Dùng UUID	Thay vì ID số 1,2,3 dùng UUID khó đoán
Không đưa ID lên URL	Dùng session để xác định user hiện tại
Áp dụng RBAC	Phân quyền rõ ràng cho từng role
Hash ID	Che giấu ID thật (không thay thế cho kiểm tra quyền)

---

## 9. Mở rộng: Hash - Salt - Pepper (Bảo vệ mật khẩu)
### 9.1. Hash là gì?
Hàm một chiều, không thể giải mã ngược
Ví dụ: MD5, SHA1, SHA256, bcrypt
Hacker không giải mã được, nhưng có thể dò ngược bằng cách:
Brute-force
Dùng Rainbow Table

### 9.2. Salt (Muối)
Là chuỗi ngẫu nhiên, thêm vào mật khẩu trước khi hash
Lưu trong CSDL cùng với hash
2 user cùng pass 123456 → hash khác nhau → chống Rainbow Table

### 9.3. Pepper (Tiêu)
Giống Salt, nhưng KHÔNG lưu trong CSDL
Lưu trong code / biến môi trường
Vô hiệu hóa tấn công ngay cả khi hacker có CSDL

### 9.4. So sánh nhanh
Kỹ thuật	Lưu ở đâu?	Tác dụng
Hash	DB	Không thể giải mã
Salt	DB (cạnh hash)	Chống Rainbow Table
Pepper	Code / Env	Chống tấn công khi lộ DB

---

## 10. IDOR kết hợp với lỗ hổng khác
IDOR + CSRF → Lừa nạn nhân thực hiện hành động trái ý muốn
IDOR + Injection → Lấy ID admin để khai thác tiếp
IDOR + Race Condition → Bypass kiểm tra quyền

---

## 11. Checklist cho Developer / QA
Có dùng ID thô trên URL không?
Có kiểm tra quyền sở hữu trước khi trả data không?
Có kiểm tra role trước khi cho phép hành động không?
Có dùng UUID thay vì ID số không?
Có hash mật khẩu + Salt + Pepper không?

---

Tài liệu tham khảo: OWASP Top 10:2025 - A01 Broken Access Control

