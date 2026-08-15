# RDP Brute-Force Detection and Incident Response using Splunk

Phòng lab này mô phỏng một cuộc tấn công brute-force vào giao thức RDP và cách một kỹ sư SOC phát hiện ra nó.

---

## Mục tiêu

Tôi xây dựng dự án này để:
- Thực hành tấn công brute-force RDP bằng Kali Linux.
- Thực hành giám sát và điều tra log bằng Splunk.
- Hiểu quy trình SOC phát hiện và xử lý sự cố.

---

## Cấu hình lab

Có 3 máy trong phòng lab:

- **Máy tấn công:** Kali Linux (IP: 192.168.188.128)
- **Máy nạn nhân:** Windows 10 (IP: 192.168.188.129)
- **Máy SOC:** Windows 11 (IP: 192.168.188.1)

Cả 3 máy đều ở cùng mạng Host-Only.

---

## Diễn biến chính

1. Hacker dùng Nmap quét mạng, phát hiện máy nạn nhân mở cổng RDP.
2. Hacker dùng Crowbar brute-force và đăng nhập thành công.
3. Hacker tạo file ẩn trên máy nạn nhân và rời đi.
4. SOC phát hiện bất thường qua Splunk.
5. SOC điều tra, xác nhận và xóa file.

---

## Công cụ đã dùng

- Kali Linux: Nmap, Crowbar
- Windows 10: RDP Host, Splunk Universal Forwarder
- Windows 11: Splunk Enterprise, Wireshark

## Video về cuộc tấn tấn công 
Link video: https://drive.google.com/file/d/1o4j41geH2Io_cy2zcFfhI9pcJ1b9tkli/view?usp=sharing

---

## Tác giả
Họ tên: Đỗ Hữu Trung
Email: trungdh26.work@gmail.com
SDT: 0913450125
Link Github: https://github.com/trungdh26/