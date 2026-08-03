# SIEM - Security Information and Event Management

## 1. SIEM là gì?

**SIEM** (Security Information and Event Management) là một giải pháp bảo mật giúp:

- Thu thập log từ nhiều loại nguồn khác nhau (máy chủ, firewall, ứng dụng...)
- Chuẩn hóa định dạng log về một dạng nhất quán
- Tương quan các sự kiện để phát hiện mối đe dọa
- Kích hoạt cảnh báo thời gian thực khi phát hiện hành vi bất thường

**Ví dụ về các giải pháp SIEM phổ biến:** Splunk Enterprise Security, IBM QRadar, Elastic SIEM, Microsoft Sentinel.

---

## 2. Vấn đề khi không có SIEM

Khi phân tích log thủ công trên từng máy, gặp phải các vấn đề sau:

| Vấn đề | Mô tả |
|--------|-------|
| **Quá nhiều nguồn log** | Hàng trăm thiết bị tạo log, mỗi cái một kiểu, phân tán khắp mạng. |
| **Không tập trung** | Phải SSH/RDP vào từng máy để xem log → mất thời gian. |
| **Thiếu ngữ cảnh** | 1 log riêng lẻ không kể được toàn bộ câu chuyện tấn công. |
| **Quá tải dữ liệu** | Hàng nghìn log/giây, con người không thể đọc hết. |
| **Định dạng khác nhau** | Log Windows khác Linux, khác Firewall → khó so sánh. |

---

## 3. Các loại nguồn log

Log được chia thành 2 loại chính:

### 3.1. Host-Centric (Hướng đến máy chủ)
Ghi lại hoạt động xảy ra **bên trong máy đó**.

| Loại thiết bị | Ví dụ log |
|--------------|-----------|
| Windows | User đăng nhập, truy cập file, chạy PowerShell, xóa log (Event ID) |
| Linux | Xác thực SSH, cron jobs, lỗi kernel, web server access |

### 3.2. Network-Centric (Hướng đến mạng)
Ghi lại hoạt động **giao tiếp giữa các máy qua mạng**.

| Loại thiết bị | Ví dụ log |
|--------------|-----------|
| Firewall | Cho phép/chặn kết nối từ IP này đến IP kia |
| Router/Switch | Gói tin đi qua cổng nào, địa chỉ MAC |
| IDS/IPS | Cảnh báo tấn công, bất thường trong lưu lượng |

---

## 4. Các thành phần của SIEM

### 4.1. Log Sources
Các thiết bị/phần mềm tạo ra log. Ví dụ: Windows Event Log, Linux syslog, Firewall logs, Web server logs.

### 4.2. Log Ingestion (Đưa log vào)
Các cách đưa log từ nguồn vào SIEM:

| Phương thức | Mô tả |
|------------|-------|
| **Agent/Forwarder** | Cài phần mềm nhẹ trên máy → tự động gửi log về SIEM |
| **Syslog** | Giao thức gửi log thời gian thực qua mạng |
| **Manual Upload** | Tải file log lên SIEM thủ công |
| **Port-Forwarding** | Cấu hình máy gửi log đến cổng của SIEM |

### 4.3. Normalization (Chuẩn hóa)
- **Parsing:** Chia log thô thành các trường (fields)
- **Normalization:** Đưa log từ nhiều nguồn về cùng 1 định dạng

**Ví dụ:**
- Windows log: `EventID=4624, User=John, IP=192.168.1.10`
- Linux log: `sshd[1234]: Accepted password for John from 192.168.1.10`
- **SIEM (đã chuẩn hóa):** `[Timestamp] [User] [IP] [Action] [Host]`

### 4.4. Correlation (Tương quan)
Kết nối các sự kiện từ nhiều nguồn để phát hiện tấn công phức tạp.

**Ví dụ:** User A đăng nhập VPN từ IP lạ → 5 phút sau chạy PowerShell → 2 phút sau gửi 100MB dữ liệu ra ngoài → SIEM kết luận là **data exfiltration**.

### 4.5. Detection Rules (Quy tắc phát hiện)
Các biểu thức logic để kích hoạt cảnh báo.

| Quy tắc | Ý nghĩa |
|---------|---------|
| 5 failed logins in 10 seconds → Alert | Phát hiện brute-force |
| Login success after 5 failures → Alert | Có thể tài khoản bị chiếm |
| USB inserted → Alert | Phát hiện vi phạm chính sách USB |
| Outbound traffic > 25MB → Alert | Phát hiện đánh cắp dữ liệu |

### 4.6. Alerting (Cảnh báo)
Khi quy tắc được kích hoạt → SIEM gửi cảnh báo đến SOC Analyst.

### 4.7. Dashboards
Hiển thị tóm tắt tình hình bảo mật trực quan:

| Thông tin trên Dashboard |
|--------------------------|
| Alert Highlights |
| System Notifications |
| Failed Login Attempts |
| Events Ingested Count |
| Rules Triggered |
| Top Domains Visited |

---

## 5. Cách tạo Detection Rules (Ví dụ)

### Use-Case 1: Phát hiện xóa log

**Bối cảnh:** Hacker thường xóa log để xóa dấu vết.

**Đặc điểm:** Windows Event ID 104 = "Event Log Cleared"

**Quy tắc:**
> If `Log Source` = `WinEventLog` AND `EventID` = `104` → Alert "Event Log Cleared Detected"

### Use-Case 2: Phát hiện lệnh `whoami`

**Bối cảnh:** Hacker dùng `whoami` sau khi leo thang quyền.

**Đặc điểm:** Windows Event ID 4688 = "Process Execution"

**Quy tắc:**
> If `Log Source` = `WinEventLog` AND `EventCode` = `4688` AND `NewProcessName` contains `whoami` → Alert "WHOAMI Execution Detected"

---

## 6. Quy trình điều tra cảnh báo

```mermaid
flowchart TD
    A[Alert triggered] --> B[Check related events]
    B --> C[Check which rule was triggered]
    C --> D{Analysis}
    D -->|False Positive| E[Tune rule to avoid future FP]
    D -->|True Positive| F[Deep investigation]
    F --> G[Contact asset owner]
    G --> H{Action}
    H --> I[Isolate infected host]
    H --> J[Block suspicious IP]