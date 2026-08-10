# TỔNG HỢP KIẾN THỨC PHISHING (TỪ LÝ THUYẾT ĐẾN THỰC HÀNH)

## Định nghĩa

Phishing là hình thức tấn công kết hợp giữa Kỹ thuật xã hội (Social Engineering) và Kỹ thuật mạng, nhằm đánh lừa nạn nhân cung cấp thông tin nhạy cảm (mật khẩu, thẻ tín dụng) hoặc cài mã độc.

Mục tiêu: Khai thác điểm yếu "con người" - thứ mà tường lửa và công nghệ khó có thể bảo vệ tuyệt đối.

---

## 6 Điểm yếu tâm lý con người bị lợi dụng

1. Sự khan hiếm (Scarcity): Lợi dụng nỗi sợ bỏ lỡ (FOMO) để thúc ép hành động nhanh mà không suy xét. (Ví dụ: "Chỉ còn 5 suất cuối cùng.")

2. Sự khẩn cấp (Urgency): Tạo áp lực thời gian khiến não bộ ưu tiên tốc độ, bỏ qua bước kiểm tra. (Ví dụ: "Tài khoản sẽ bị khóa trong 24h.")

3. Quyền uy (Authority): Lợi dụng tâm lý phục tùng người có chức vụ cao (Sếp, IT, Công an). (Ví dụ: "Yêu cầu từ Giám đốc: Chuyển tiền gấp.")

4. Sự sợ hãi (Fear): Kích hoạt phản xạ tự vệ, khiến nạn nhân làm theo hướng dẫn để tránh tổn thất. (Ví dụ: "Phát hiện đăng nhập bất thường. Xác minh ngay.")

5. Sự tò mò (Curiosity): Kích thích não bộ muốn lấp đầy khoảng trống thông tin. (Ví dụ: "Bí mật: Lộ trình sáp nhập Q3.")

6. Lòng tin (Trust): Lợi dụng mối quan hệ quen thuộc (thương hiệu, đồng nghiệp) để hạ thấp cảnh giác. (Ví dụ: Email giả mạo Microsoft 365 hoặc trưởng phòng gửi.)

---

## Quy trình vòng đời của một chiến dịch Phishing

1. Giai đoạn 1: Lập kế hoạch (Scoping)

    Xác định mục tiêu cụ thể.

    Định nghĩa phạm vi, thời gian và các kỹ thuật được phép.

    Ký kết pháp lý và thỏa thuận với khách hàng.

2. Giai đoạn 2: Trinh sát (Reconnaissance)

    Sử dụng các kỹ thuật tình báo nguồn mở (OSINT) để tìm hiểu thông tin về mục tiêu: LinkedIn, website công ty, thông cáo báo chí, mạng xã hội.

    Thu thập các điểm yếu có thể khai thác: chính sách đổi mật khẩu, tên cấp trên, sự kiện nội bộ.

3. Giai đoạn 3: Xây dựng kịch bản và tải trọng (Scenario & Payload Development)

    Tạo nội dung email giả mạo có tính thuyết phục dựa trên thông tin trinh sát.

    Thiết lập cơ sở hạ tầng: Tên miền giả (typosquatting), trang web clone để thu thập thông tin đăng nhập.

4. Giai đoạn 4: Thực thi (Exploitation)

    Gửi email lừa đảo đến mục tiêu theo kế hoạch.

    Sử dụng các kỹ thuật giả mạo để vượt qua bộ lọc email.

    Thu thập thông tin đăng nhập thông qua các trang web giả (Credential Harvester).

5. Giai đoạn 5: Báo cáo và khắc phục (Reporting)

    Tổng hợp số liệu: Open Rate, Click Rate, Credential Entry Rate, Reporting Rate.

    Phân tích điểm yếu trong nhận thức của nhân viên.

    Đưa ra các khuyến nghị cụ thể để cải thiện bảo mật.

---

## Các kỹ thuật tấn công cốt lõi

1. URL Masking (Ngụy trang liên kết): Sử dụng thẻ HTML <a href="..."> để hiển thị văn bản khác với liên kết thực tế.

    Có thể kiểm tra bằng cách di chuột (hover) vào link trên máy tính hoặc nhấn giữ trên điện thoại.

2. Typosquatting (Chiếm đoạt tên miền gần giống): Đăng ký tên miền với một lỗi chính tả cố ý để đánh lừa người dùng nhập sai.

    Ví dụ: tryacounting.thm (thiếu "c") thay vì tryaccounting.thm.

3. Email Spoofing (Giả mạo người gửi): Sử dụng Alias hoặc chỉnh sửa trường "From" trong tiêu đề email để giả dạng người khác.

    Có thể vượt qua các bộ lọc cơ bản nhưng bị chặn bởi SPF/DKIM/DMARC.

4. Credential Harvesting (Thu thập thông tin đăng nhập): Sử dụng SET (Social Engineering Toolkit) để clone giao diện trang đăng nhập hợp pháp.

    Host trang giả trên máy chủ riêng và cấu hình để ghi lại mọi thông tin được nhập vào.

---

## Ba biện pháp phòng vệ kỹ thuật chính chống giả mạo email

1. SPF (Sender Policy Framework):

    Liệt kê danh sách các máy chủ IP được phép gửi email thay mặt cho tên miền.

    Mục tiêu: Ngăn chặn giả mạo địa chỉ người gửi ở cấp độ máy chủ.

2. DKIM (DomainKeys Identified Mail):

    Sử dụng mã hóa bất đối xứng (Private/Public Key - tương tự HTTPS) để ký số vào email.

    Mục tiêu: Xác minh nội dung email không bị thay đổi trong quá trình vận chuyển.

3. DMARC (Domain-based Message Authentication):

    Hướng dẫn máy chủ nhận xử lý khi SPF và DKIM thất bại.

    Các chính sách: none (chỉ giám sát), quarantine (chuyển vào thư rác), reject (từ chối nhận).

    Mục tiêu: Cung cấp chỉ thị rõ ràng cho máy chủ nhận về cách xử lý email giả mạo.

---

## hực hành Lab: Tấn công Spear Phishing vào Bob (Trưởng phòng Tài chính)

1. Tình huống:

    Mục tiêu: Bob - Trưởng phòng Tài chính tại TryAccounting.

    Phát hiện: Công ty có chính sách đổi mật khẩu 3 tháng/lần và sử dụng bảo mật email cơ bản.

2. Kỹ thuật áp dụng:

    Typosquatting: Tạo tên miền http://tryacounting.thm (thiếu chữ "c") để giả trang đăng nhập.

    Credential Harvesting: Dùng SET để clone giao diện đăng nhập và host trên cổng 80.

    Email Spoofing: Dùng Rainloop Alias support@tryaccounting.thm để giả mạo phòng IT.

3. Nội dung email lừa đảo:

    Tiêu đề: Action Required: Password Expiration Notice

    Nội dung: Thông báo chính sách bắt buộc đổi mật khẩu trước thứ Sáu, đính kèm link giả.

    Các yếu tố tâm lý kết hợp: Quyền uy (Phòng IT), Khẩn cấp (Trước thứ Sáu), Sợ hãi (Mất quyền truy cập).

4. Kết quả:

    Email vượt qua bộ lọc (do dùng Alias) và đến được Bob.

    Bob (trong mô phỏng) nhấp vào link và nhập thông tin đăng nhập.

    Terminal thu về được kết quá: username + password.

