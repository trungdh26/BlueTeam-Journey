# OWASP(2)
## A02 - A03 - A06 -A10 lỗ hổng bảo mật phổ biến
## 1. A02: SECURITY MISCONFIGURATIONS

### Định nghĩa

> **Cấu hình bảo mật sai** xảy ra khi hệ thống, máy chủ hoặc ứng dụng được triển khai với **các thiết lập không an toàn, cấu hình không đầy đủ, hoặc dịch vụ bị phơi ra ngoài**, tạo ra điểm xâm nhập dễ dàng cho kẻ tấn công.

### Các hình thức tấn công chính

| Hình thức | Mô tả | Hậu quả |
|-----------|-------|---------|
| **Default Credentials** | Để mật khẩu mặc định (admin/admin, root/toor) không đổi | Kẻ tấn công đăng nhập ngay lập tức |
| **Unnecessary Services** | Các cổng/dịch vụ không cần vẫn mở ra internet | Tăng bề mặt tấn công, lộ thông tin hệ thống |
| **Cloud Misconfiguration** | Bucket S3, Azure Blob để công khai (public) | Rò rỉ toàn bộ dữ liệu nhạy cảm |
| **Debug Mode ON** | Để chế độ debug trên production | Lộ stack trace, đường dẫn hệ thống, câu lệnh SQL |
| **Unrestricted API** | API không có xác thực/phân quyền | Hacker gọi API để lấy dữ liệu trái phép |
| **Outdated Software** | Dùng phần mềm cũ có lỗ hổng | Bị khai thác các CVE đã công bố |

### Ví dụ thực tế

**Uber 2017:** Uber để lộ một bucket AWS S3 dự phòng chứa dữ liệu nhạy cảm (thông tin tài xế và hành khách) vì bucket được đặt ở chế độ **công khai**. Kẻ tấn công có thể tải xuống dữ liệu trực tiếp mà không cần xác thực. Đây là hậu quả của **1 lỗi cấu hình đơn giản** nhưng cực kỳ nghiêm trọng.

### Cách phòng chống

- Củng cố cấu hình mặc định, xóa các tính năng/dịch vụ không sử dụng
- Áp dụng xác thực mạnh và nguyên tắc **"Least Privilege"** (ít quyền nhất có thể)
- Hạn chế mức độ phơi nhiễm mạng, phân đoạn tài nguyên nhạy cảm
- Ẩn stack trace và thông tin hệ thống khỏi thông báo lỗi
- Kiểm tra cấu hình cloud và quyền truy cập định kỳ
- Tích hợp kiểm tra cấu hình tự động vào quy trình CI/CD

### Phân biệt A02 với các lỗ hổng khác

| Tiêu chí | A02 | Các lỗ khác (A01, A03, A04, A05...) |
|----------|-----|--------------------------------------|
| **Nguyên nhân** | Lỗi do **cấu hình môi trường** | Lỗi do **code/logic lập trình** |
| **Ai gây ra?** | DevOps, SysAdmin | Developer |
| **Khắc phục** | Sửa file cấu hình, tắt/bật option | Sửa code, viết lại logic |
| **Ví dụ** | Debug mode ON, mật khẩu mặc định | SQL Injection do ghép chuỗi, lỗi phân quyền |

---

## 2. A03: SOFTWARE SUPPLY CHAIN FAILURES

### Định nghĩa

> **Thất bại trong chuỗi cung ứng phần mềm** xảy ra khi ứng dụng phụ thuộc vào các **thành phần, thư viện, dịch vụ, hoặc mô hình AI bị xâm phạm, lỗi thời, hoặc không được xác minh**, cho phép kẻ tấn công chèn mã độc mà không cần chạm vào code của bạn.

### Các hình thức tấn công chính

| Hình thức | Mô tả | Hậu quả |
|-----------|-------|---------|
| **Typosquatting** | Tạo gói giả mạo có tên gần giống gói thật (`rlask` giả mạo `flask`) | Lập trình viên cài nhầm gói độc hại |
| **Dependency Confusion** | Đăng gói trùng tên với gói nội bộ lên kho chung | Hệ thống tải nhầm gói giả mạo |
| **Compromised Updates** | Hack vào server thư viện, chèn mã độc vào bản cập nhật | Hàng ngàn ứng dụng bị nhiễm độc |
| **Insecure CI/CD** | Quy trình build không được bảo vệ | Kẻ tấn công chèn code độc vào pipeline |
| **Unverified AI Models** | Dùng model AI từ bên thứ 3 không kiểm tra | Model chứa cửa hậu hoặc kết quả sai lệch |

### Ví dụ thực tế

**SolarWinds 2021:** Kẻ tấn công chèn mã độc vào bản cập nhật của phần mềm Orion, ảnh hưởng đến hơn 18.000 tổ chức, bao gồm cả các cơ quan chính phủ Mỹ. Đây **không phải lỗi code**, mà là lỗ hổng trong **quy trình xây dựng, xác minh và phân phối bản cập nhật**.

### Cách phòng chống

- Xác minh tất cả thành phần, thư viện và mô hình AI trước khi sử dụng
- Giám sát và vá lỗi các dependency thường xuyên
- Ký, xác minh và kiểm tra bản cập nhật bằng **checksum/chữ ký số**
- Khóa chặt CI/CD pipelines để ngăn chặn giả mạo
- Theo dõi nguồn gốc (provenance) và giấy phép của tất cả dependency
- Giám sát runtime để phát hiện hành vi bất thường từ dependency hoặc AI

### Phân biệt A03 với A08

| Tiêu chí | A03 - Software Supply Chain | A08 - Software/Data Integrity |
|----------|----------------------------|-------------------------------|
| **Phạm vi** | Rộng: cả hệ sinh thái dependency, build pipeline | Hẹp: tập trung vào deserialization và dữ liệu đầu vào |
| **Mục tiêu** | Bản cập nhật, thư viện bên thứ 3, CI/CD | Dữ liệu tuần tự hóa (pickle, JSON) từ người dùng |
| **Ví dụ** | SolarWinds, Log4j | Tấn công pickle deserialization |

---

## 3. A06: INSECURE DESIGN

### Định nghĩa

> **Thiết kế không an toàn** xảy ra khi phần mềm được **thiết kế thiếu các biện pháp bảo mật ngay từ đầu**, không phải lỗi do code sai mà là **lỗi do kiến trúc, quy trình, hoặc thiết kế logic** không tính đến bảo mật.

### Các hình thức tấn công chính

| Hình thức | Mô tả | Hậu quả |
|-----------|-------|---------|
| **Logic Flaws** | Luồng xử lý của ứng dụng có thiếu sót logic | Kẻ tấn công lợi dụng để bypass kiểm tra |
| **Missing Threat Modeling** | Không xác định kịch bản tấn công khi thiết kế | Bỏ qua các vector tấn công tiềm ẩn |
| **Insecure Defaults** | Thiết kế cho phép mặc định không an toàn | Người dùng phải tự cấu hình bảo mật |
| **Over-Engineering** | Hệ thống quá phức tạp, khó kiểm soát | Xuất hiện lỗ hổng không lường trước được |
| **Business Logic Flaws** | Logic nghiệp vụ có lỗ hổng | Tấn công giảm giá, đảo ngược giao dịch |

### Ví dụ thực tế

Một ứng dụng bán hàng thiết kế cho phép người dùng giảm giá bằng cách thay đổi tham số `?discount=10` thành `?discount=100`. Vì **thiết kế ban đầu không giới hạn** giá trị giảm giá, kẻ tấn công có thể mua hàng với giá siêu rẻ. Đây là lỗi ở tầng thiết kế, không phải lỗi code (vì code chạy đúng yêu cầu thiết kế).

### Cách phòng chống

- Áp dụng **Threat Modeling** (mô hình hóa mối đe dọa) ngay từ đầu dự án
- Thiết kế theo nguyên tắc **"Secure by Default"** (mặc định an toàn)
- Xác định các giả định bảo mật và giới hạn ngay từ khâu thiết kế
- Sử dụng **OWASP ASVS** và **OWASP Proactive Controls** làm hướng dẫn
- Thực hiện đánh giá thiết kế (design review) trước khi bắt tay vào code
- Áp dụng **"Zero Trust"** trong thiết kế kiến trúc

### Phân biệt A06 với A05

| Tiêu chí | A06 - Insecure Design | A05 - Injection |
|----------|----------------------|-----------------|
| **Thời điểm** | Xảy ra ở **giai đoạn thiết kế** (trước khi code) | Xảy ra ở **giai đoạn code** (khi viết lập trình) |
| **Nguyên nhân** | Không tính đến bảo mật khi lên ý tưởng | Không lọc input trong code |
| **Khắc phục** | Thay đổi thiết kế, kiến trúc | Sửa code, thêm hàm lọc input |

---

## 4. A10: MISHANDLING OF EXCEPTIONAL CONDITIONS

### Định nghĩa

> **Xử lý sai các tình huống đặc biệt** xảy ra khi ứng dụng **không xử lý đúng cách các lỗi bất thường** như: ngoại lệ (exceptions), lỗi logic, hoặc các tình huống không mong đợi, tạo cơ hội cho kẻ tấn công khai thác.

### Các hình thức tấn công chính

| Hình thức | Mô tả | Hậu quả |
|-----------|-------|---------|
| **Unhandled Exceptions** | Code không bắt (catch) ngoại lệ, hệ thống crash hoặc bộc lộ thông tin | Lộ stack trace, gây mất ổn định |
| **Failing Open** | Khi gặp lỗi, hệ thống cho phép truy cập (fail open) thay vì từ chối | Bypass xác thực, vượt qua kiểm tra |
| **Missing Error Handling** | Không kiểm tra kết quả trả về của hàm quan trọng | Xử lý dữ liệu sai, gây lỗi logic |
| **Logical Edge Cases** | Không xử lý các trường hợp đặc biệt (giá trị 0, null, giá trị âm) | Gây tràn số, sai số, crash |
| **Information Disclosure** | Thông báo lỗi quá chi tiết | Lộ thông tin hệ thống, code, cấu trúc database |

### Ví dụ thực tế

Một ứng dụng kiểm tra quyền admin: nếu hàm `check_admin()` gặp lỗi và không return True/False, nhưng code vẫn coi là "được phép" (failing open), kẻ tấn công có thể khiến hàm bị lỗi và vào được trang admin.

```python
# Code bị failing open
try:
    is_admin = check_admin(user)
except:
    # Bỏ qua lỗi, vẫn cho qua
    is_admin = True

    