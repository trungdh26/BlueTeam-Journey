# OWASP (1)
## A01 - A07 - A09: Các lỗ hổng bảo mật web phổ biến

---

## 🎯 MỤC LỤC

1. [A01: Broken Access Control](#1-a01-broken-access-control)
2. [A07: Authentication Failures](#2-a07-authentication-failures)
3. [A09: Logging & Alerting Failures](#3-a09-logging--alerting-failures)
4. [So sánh Horizontal vs Vertical](#4-so-sánh-horizontal-vs-vertical)


---

## 1. A01: BROKEN ACCESS CONTROL

### Định nghĩa

> **Kiểm soát truy cập bị hỏng** xảy ra khi hệ thống **không kiểm tra đúng quyền hạn** của người dùng, cho phép họ truy cập vào dữ liệu hoặc chức năng không thuộc quyền sở hữu.

### Các hình thức tấn công chính

| Hình thức | Mô tả | Ví dụ |
|-----------|-------|-------|
| **Horizontal (Leo thang ngang)** | Truy cập vào tài khoản/dữ liệu của người khác **cùng cấp độ quyền** | User A xem được thông tin của User B |
| **Vertical (Leo thang dọc)** | Từ user thường leo lên **quyền Admin/Root** | User thường xóa được tài khoản người khác |
| **IDOR** | Sửa ID trên URL để xem dữ liệu không thuộc quyền sở hữu | `/order/1001` → `/order/1002` |

### Cách phòng chống

- Kiểm tra quyền trên **server**, không tin tưởng dữ liệu từ client
- Dùng **UUID** thay vì ID tuần tự dễ đoán
- Áp dụng nguyên tắc **"Least Privilege"** (ít quyền nhất có thể)

---

## 2. A07: AUTHENTICATION FAILURES

### Định nghĩa

> **Lỗi xác thực** xảy ra khi hệ thống **không xác minh được chính xác danh tính** của người dùng, cho phép kẻ tấn công giả mạo người khác.

### Các hình thức tấn công chính

| Hình thức | Mô tả | Hậu quả |
|-----------|-------|---------|
| **Username Enumeration** | Hệ thống tiết lộ username đã tồn tại hay chưa | Hacker biết được danh sách user để tấn công |
| **Brute Force** | Thử hàng loạt mật khẩu để tìm ra mật khẩu đúng | Chiếm được tài khoản |
| **Logic Flaws** | Lỗi trong luồng đăng ký/đăng nhập (ví dụ: không đồng bộ xử lý hoa-thường) | Đăng nhập vào tài khoản admin bằng cách đăng ký tên giống |
| **Session/Cookie không an toàn** | Session ID dễ đoán, không dùng HTTPS, cookie thiếu flag Secure/HttpOnly | Đánh cắp phiên làm việc của người khác |

### Cách phòng chống

- Đồng bộ logic giữa đăng ký và đăng nhập
- Áp dụng **MFA** (xác thực đa yếu tố)
- Giới hạn số lần thử đăng nhập (rate limiting)
- Mã hóa mật khẩu bằng thuật toán mạnh (bcrypt, Argon2)
- Sử dụng HTTPS, cookie với flag **Secure**, **HttpOnly**, **SameSite**

---

## 3. A09: LOGGING & ALERTING FAILURES

### Định nghĩa

> **Lỗi ghi nhật ký & cảnh báo** xảy ra khi hệ thống **không ghi lại đầy đủ** các sự kiện bảo mật hoặc **không có cảnh báo** khi có tấn công, khiến người quản trị không thể phát hiện và điều tra.

### Các vấn đề thường gặp

| Vấn đề | Hậu quả |
|--------|---------|
| Không ghi log các sự kiện xác thực | Không biết ai đã đăng nhập |
| Log mơ hồ, thiếu chi tiết | Không thể điều tra |
| Không có cảnh báo khi bị tấn công | Phát hiện quá muộn |
| Log bị xóa sớm (retention ngắn) | Mất bằng chứng điều tra |
| Log bị kẻ tấn công xóa/sửa | Không có bằng chứng pháp lý |

### Log tốt cần ghi những gì?

- **Timestamp** (thời gian)
- **User ID** / IP Address (ai đã làm)
- **Action** (hành động gì)
- **Result** (thành công hay thất bại)
- **Source** (từ đâu)

### Cách phòng chống

- Ghi log tất cả sự kiện bảo mật quan trọng (đăng nhập, thay đổi quyền, tạo user)
- Lưu log ở nơi an toàn, không cho kẻ tấn công xóa/sửa được
- Thiết lập cảnh báo tự động (alert) khi phát hiện hành vi bất thường (brute force, login từ IP lạ)
- Lưu log trong thời gian đủ dài (ít nhất 30-90 ngày)

---

## 4. SO SÁNH HORIZONTAL VS VERTICAL

| Tiêu chí | Horizontal (Ngang) | Vertical (Dọc) |
|----------|--------------------|----------------|
| **Hướng tấn công** | Đi ngang sang tài khoản khác | Đi lên quyền cao hơn (Admin/Root) |
| **Mục tiêu** | User A → User B (cùng cấp) | User thường → Admin |
| **Nguyên nhân** | Code không kiểm tra quyền sở hữu | Lỗ hổng hệ thống / thiếu kiểm tra role |
| **Ví dụ** | Sửa ID trên URL để xem user khác | Khai thác lỗi kernel để thành root |

