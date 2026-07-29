# Introduction: OWASP

---

## 1. OWASP là gì?
OWASP ( Open Worldwide Application Security Project): là cộng đồng phi lợi nhuận chuyên về bảo mật ứng dụng web. Họ làm ra tài liệu miễn phí, trong đó nổi tiếng nhất là danh sách Top 10 lỗ hổng nguy hiểm nhất.

## 2. Các khái niệm liên quan
- **MITRE**: Tổ chức quản lý kho lỗ hổng.
- **CWE**: Là loại lỗ hổng (có khoảng 900 loại).
- **CVE**: Là lỗ hổng cụ thể của từng phần mềm.
- **CVSS**: Là thang điểm đánh giá mức độ nghiêm trọng (0-10).

## 3. Danh sách 10 lỗ hổng hàng đầu
1. Broken Access Control - Lỗi phân quyền
2. Security Misconfiguration - Cấu hình sai
3. Software Supply Chain Failures - Lỗi từ thư viện bên thứ 3
4. Cryptographic Failures - Lỗi mã hóa
5. Injection - Tiêm nhiễm (SQL, XSS)
6. Insecure Design - Thiết kế không an toàn
7. Authentication Failures - Lỗi đăng nhập
8. Software or Data Integrity Failures - Lỗi toàn vẹn dữ liệu
9. Security Logging & Alerting Failures - Thiếu giám sát cảnh báo
10. Mishandling of Exceptional Conditions - Xử lý sai lỗi ngoại lệ

## 4. Cách OWASP xếp hạng
Họ dùng **dữ liệu thực tế** từ hàng triệu ứng dụng kết hợp với **khảo sát chuyên gia**. Không chỉ dùng máy tính vì dữ liệu tự động không phản ánh được các lỗ hổng mới nhất.

## 5. Kết luận
OWASP Top 10 là tài liệu nâng cao nhận thức, giúp biết được những lỗ hổng nguy hiểm nhất để tập trung học và kiểm tra. Đối với người mới, cần hiểu bản chất từng nhóm lỗ hổng, không cần học thuộc chi tiết kỹ thuật.

## 6. Tài liệu tham khảo
https://owasp.org/Top10/2025/