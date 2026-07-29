# 🗺️ Roadmap 70 ngày - Blue Team Journey

## Tổng quan
Đây là kế hoạch tổng thể cho hành trình 70 ngày tự học **Blue Team / SOC Analyst** của tôi.  
Lộ trình được chia làm 3 chặng chính, mỗi chặng tập trung vào một nhóm kỹ năng cụ thể.

---

## Chặng 1: Làm quen - Biến lý thuyết thành kỹ năng (Ngày 1 - 14)

| Ngày | Chủ đề | Chi tiết | Trạng thái |
|:----:|:-------|:---------|:-----------|
| 1 | Cài VirtualBox và Ubuntu | Tải và cài VirtualBox, tạo máy ảo Ubuntu. | [x] |
| 2 | Linux Commands (Phần 1) | Học các lệnh: `pwd`, `ls`, `cd`, `mkdir`, `touch`, `cp`, `mv`, `rm`. | [x] |
| 3 | Linux Commands (Phần 2) | Học các lệnh: `cat`, `grep`, `find`, `ps`, `netstat`, `sudo`, `chmod`. | [x] |
| 4 | Cài máy ảo Windows 11 | Tải ISO, tạo máy ảo, cài Guest Additions. | [x] |
| 5 | Review Tổng hợp | Ôn tập các lệnh Linux, kiểm tra máy ảo. | [x] |
| 6 | Nghỉ review | - | [x] |
| 7 | Nghỉ cuối tuần | - | [x] |
| 8 | Cài Wireshark và bắt gói tin | Bắt gói tin HTTP, ping, SSH. | [x] |
| 9 | Bộ lọc Wireshark cơ bản | Học các filter: `http`, `dns`, `tcp`, `ip.addr`, `tcp.port`. | [x] |
| 10 | Log Windows | Event Viewer, Event ID 4624, 4625, 4672, 4688. | [x] |
| 11 | Log Linux | Xem và lọc log trong `/var/log/auth.log` và `/var/log/syslog`. | [x] |
| 12 | Tổng hợp Log | So sánh log Windows và Linux. | [x] |
| 13 | Self-Attack Lab | Tấn công SSH bằng Hydra và quan sát log. | [x] |
| 14 | Nghỉ cuối tuần | Sắp xếp lại ghi chú. | [x] |

---

## Chặng 2: Bơi thử - Bắt đầu với TryHackMe & Pre-Security (Ngày 15 - 35)

| Ngày | Chủ đề | Chi tiết | Trạng thái |
|:----:|:-------|:---------|:-----------|
| 15 | Đăng ký TryHackMe | Làm phòng "Welcome". | [x] |
| 16 | Pre-Security: Network Fundamentals | Ôn lại OSI model, TCP/IP. | [x] |
| 17 | Pre-Security: Network Fundamentals (tiếp) | Subnetting, giao thức cơ bản. | [x] |
| 18 | Pre-Security: How The Web Works | HTTP/HTTPS, DNS, request/response. | [x] |
| 19 | Pre-Security: Intro to LAN | ARP, DHCP, NAT. | [x] |
| 20 | Pre-Security: Windows Fundamentals | UAC, NTFS, Event Viewer. | [x] |
| 21 | Nghỉ cuối tuần | Review Pre-Security. | [x] |
| 22 | Linux Fundamentals (Phần 1) | Ôn lại và bổ sung kiến thức Linux. | [x] |
| 23 | Linux Fundamentals (Phần 2) | File permissions, process management. | [x] |
| 24 | Hoàn thành Pre-Security | Bài kiểm tra cuối path. | [x] |
| 25 | Windows Advanced | - | [x] |
| 26 | Windows Advanced 2 | - | [x] |
| 27 | Windows Advanced 3 | System Configuration. | [x] |
| 28 | Command Line | CMD và PowerShell. | [x] |
| 29 | Networking Concepts | - | [x] |
| 30 | Nmap | Các lệnh quét mạng cơ bản. | [x] |
| 31 | Encryption | Mã hóa, băm. | [x] |
| 32 | Firewall | Khái niệm và cấu hình cơ bản. | [x] |
| 33 | OWASP Top 10 - Tổng quan | Đọc danh sách, xem video giới thiệu. | [ ] |
| 34 | THM: OWASP Top 10 | Bắt đầu Room, làm quen với các khái niệm. | [ ] |
| 35 | THM: SQL Injection | Học và thực hành SQL Injection. | [ ] |

---

## Chặng 3: Tự làm - Dự án và Chuyên sâu (Ngày 36 - 70)

| Ngày | Chủ đề | Chi tiết | Trạng thái |
|:----:|:-------|:---------|:-----------|
| 36 | THM: XSS | Học và thực hành Cross-Site Scripting. | [ ] |
| 37 | THM: IDOR | Học và thực hành Insecure Direct Object Reference. | [ ] |
| 38 | THM: Intro to SIEM | Bắt đầu phòng học về SIEM. | [ ] |
| 39 | PortSwigger Lab (SQLi) | Làm 1 lab về SQL Injection. | [ ] |
| 40 | PortSwigger Lab (XSS) | Làm 1 lab về XSS. | [ ] |
| 41 | Hoàn thành THM: Intro to SIEM | Ghi lại các khái niệm chính. | [ ] |
| 42 | Splunk SPL cơ bản | Xem video, ghi chú các lệnh SPL. | [ ] |
| 43 | Thực hành SPL | Thử viết truy vấn SPL trên môi trường demo. | [ ] |
| 44 | THM: Phishing | Phân tích email phishing. | [ ] |
| 45 | Dự án RDP - Chuẩn bị | Cài Kali, cấu hình Windows RDP. | [ ] |
| 46 | Dự án RDP - Tấn công | Hydra Brute-force RDP. | [ ] |
| 47 | Dự án RDP - Quan sát | Log Windows và Wireshark. | [ ] |
| 48 | Dự án RDP - Viết báo cáo | README.md cho dự án. | [ ] |
| 49 | THM: SOC Level 1 | Bắt đầu path SOC Level 1. | [ ] |
| 50 | Incident Response | Quy trình IR (NIST). | [ ] |
| 51 | MITRE ATT&CK | Framework và ma trận. | [ ] |
| 52 | Threat Hunting | Các kỹ thuật săn lùng mối đe dọa. | [ ] |
| 53 | Chứng chỉ | Nghiên cứu Security+ và (ISC)² CC. | [ ] |
| 54 | Thực hành SOC | Các phòng lab SOC Level 1. | [ ] |
| 55-56 | Dự án tổng hợp | Script phân tích log hoặc Dashboard. | [ ] |
| 57 | Review 70 ngày | Xem lại lịch sử commit. | [ ] |
| 58 | Tổ chức lại GitHub | Cấu trúc thư mục rõ ràng. | [ ] |
| 59 | Viết README.md tổng thể | Giới thiệu hành trình. | [ ] |
| 60 | CV/Portfolio | Chọn điểm nhấn và viết mô tả. | [ ] |
| 61 | Chia sẻ LinkedIn | Bài viết về hành trình 70 ngày. | [ ] |
| 62 | Định hướng tiếp theo | Tìm hiểu mảng yêu thích. | [ ] |
| 63 | Nghỉ cuối tuần | - | [ ] |
| 64 | MITRE ATT&CK - Chi tiết | Đọc sâu về các kỹ thuật. | [ ] |
| 65 | SIEM - Splunk/Elastic | Thực hành nâng cao. | [ ] |
| 66 | Cloud Security | Khái niệm cơ bản. | [ ] |
| 67 | Kết nối cộng đồng | Tham gia group, Discord. | [ ] |
| 68 | Khóa học online | Tìm kiếm các khóa học Blue Team. | [ ] |
| 69 | Tổng kết 10 tuần | Đọc lại tất cả file. | [ ] |
| 70 | Tổng kết cuối cùng | Viết `Farewell-Summary.md`. | [ ] |

---

## Cách sử dụng file này

1. **Đánh dấu tiến độ**: Thay `[ ]` bằng `[x]` khi hoàn thành một ngày.
2. **Cập nhật nội dung**: Điều chỉnh chủ đề hoặc chi tiết nếu lộ trình thay đổi.
3. **Commit thường xuyên**: Push lên GitHub sau mỗi lần cập nhật để theo dõi tiến độ.

---
