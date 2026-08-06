# Splunk (1)

---

## 1. Splunk là gì?

Splunk là một công cụ chuyên dùng để thu thập, tìm kiếm, phân tích và trực quan hóa dữ liệu log. Trong một SOC (Security Operations Center), Splunk là công cụ quan trọng nhất để một nhà phân tích có thể phát hiện và điều tra các cuộc tấn công mạng.

Splunk gồm ba thành phần chính:

+ Forwarder (Bộ chuyển tiếp): Được cài trên các máy cần lấy log. Nó đọc log và gửi về máy chủ trung tâm.

+ Indexer (Bộ đánh chỉ mục): Nhận log từ Forwarder, phân tích cú pháp, tách thành các trường (field), đánh chỉ mục và lưu trữ.

+ Search Head (Giao diện tìm kiếm): Là giao diện web cho người dùng, nơi bạn nhập truy vấn và xem kết quả.

---

## 2. Các khái niệm cơ bản

Index là "kho chứa" dữ liệu. Dùng index để phân loại dữ liệu. Ví dụ: index=main là index mặc định, index=windows chứa log Windows, index=firewall chứa log tường lửa. Khi tìm kiếm, luôn phải chỉ định index để Splunk biết tìm ở đâu.

Sourcetype là loại nguồn dữ liệu. Nó giúp Splunk biết log đó có định dạng gì để phân tích cú pháp. Ví dụ: sourcetype=web_traffic, sourcetype=WinEventLog:Security.

Host là tên máy tính hoặc thiết bị đã tạo ra log. Ví dụ: host=webserver01.

Field là các trường dữ liệu đã được Splunk trích xuất từ log thô. Ví dụ: client_ip (IP máy khách), user_agent (trình duyệt hoặc công cụ), path (đường dẫn được yêu cầu), status (mã HTTP). Mỗi field đều có thể được dùng để lọc và thống kê.

_raw là dòng log thô, chưa qua xử lý. Bạn thường không cần xem _raw, mà chỉ cần xem các field đã được tách ra.

_time là thời gian xảy ra sự kiện. Bạn có thể dùng _time để lọc theo khoảng thời gian hoặc vẽ biểu đồ.

---

## 3. Các lệnh SPL cốt lõi

### Lọc dữ liệu

Để lọc dữ liệu bằng index và sourcetype. Ví dụ: index=main sourcetype=web_traffic.

Các điều kiện lọc. Để lọc chính xác một giá trị: field=value. Ví dụ: status=404.

Để lọc loại trừ (bỏ qua một giá trị): field!=value. Ví dụ: user_agent!=*Mozilla* có nghĩa là loại bỏ tất cả user_agent chứa từ Mozilla.

Để lọc một lúc nhiều giá trị dùng field IN (value1, value2). Ví dụ: path IN ("/.env", "/.git") sẽ tìm các log có path là /.env hoặc /.git.

Để lọc theo một phần của giá trị (ký tự đại diện), bạn dùng dấu *. Ví dụ: path="*..\/..\/*" sẽ tìm các path có chứa chuỗi ../../.


### Thống kê và nhóm

Lệnh stats là lệnh quan trọng nhất để thống kê. Dùng "stats count by field" để đếm số lượng sự kiện theo từng giá trị của field đó. 

 Ví dụ: stats count by client_ip sẽ đếm số lượng request theo từng IP.


Để tính tổng một trường số, bạn dùng stats sum(field) as new_name. 

 Ví dụ: stats sum(bytes_transferred) as total_bytes.

Để sắp xếp kết quả, dùng sort -field (dấu - có nghĩa là giảm dần, từ cao xuống thấp).

 Ví dụ: sort -count sẽ sắp xếp từ số lượng cao nhất xuống thấp nhất. 
 
Dùng head N để lấy N kết quả đầu tiên. Ví dụ: head 5 để lấy 5 IP có số lượng cao nhất.


### Biểu đồ theo thời gian

Lệnh timechart giúp bạn vẽ biểu đồ số lượng sự kiện theo thời gian. 

 Ví dụ: timechart span=1d count sẽ vẽ biểu đồ số lượng sự kiện theo từng ngày.

Kết hợp với sort by count | reverse để xem ngày nào có số lượng log nhiều nhất được xếp ở đầu.


### Định dạng kết quả

Lệnh table giúp bạn tạo một bảng với các cột tùy chọn. Ví dụ: table _time, path, user_agent, status sẽ hiển thị 4 cột: thời gian, đường dẫn, user_agent và mã trạng thái.

Lệnh rename giúp bạn đổi tên field. Ví dụ: rename bytes as size.

---

## 4. Quy trình điều tra SOC

### Bước 1 - Phát hiện bất thường

Bắt đầu bằng cách chạy một truy vấn đơn giản để xem toàn bộ log web. Sau đó dùng timechart span=1d count để xem số lượng log theo từng ngày. Phát hiện ra một ngày có số lượng log tăng đột biến (traffic spike) - đó chính là cửa sổ thời gian của cuộc tấn công.

### Bước 2 - Sàng lọc và xác định thủ phạm

Đặt điều kiện lọc loại trừ các user_agent hợp lệ như Mozilla, Chrome, Safari, Firefox. Chỉ giữ lại các user_agent bất thường. Nếu thấy một IP duy nhất chịu trách nhiệm cho tất cả các user_agent bất thường đó. Tiếp tục dùng stats count by client_ip | sort -count | head 5 để xác nhận IP đó là thủ phạm.

### Bước 3 - Truy vết chuỗi tấn công

Với IP đã xác định, đi theo từng giai đoạn tấn công.

#### Trinh sát (Reconnaissance)

Tìm các yêu cầu đến các file nhạy cảm như /.env, /*phpinfo*, /.git*. Kết quả cho thấy kẻ tấn công dùng curl và wget để thăm dò, và bị trả về mã 404/403/401.

#### Liệt kê và kiểm tra lỗ hổng (Enumeration)

Tìm các path chứa ../../ (Path Traversal) hoặc redirect. Thấy các nỗ lực đọc tệp hệ thống, cho thấy kẻ tấn công đã chuyển từ quét sang thử nghiệm khai thác.

#### Khai thác SQL Injection

Tìm user_agent chứa sqlmap hoặc Havij và thấy các payload SLEEP(5) - dấu hiệu của tấn công SQL Injection dựa trên thời gian (time-based). Mã trạng thái 504 xác nhận cuộc tấn công thành công.

#### Đánh cắp dữ liệu (Exfiltration)

Tìm các path chứa backup.zip hoặc logs.tar.gz và thấy kẻ tấn công đang tải xuống các tệp log nén với dung lượng lớn.

#### Webshell và RCE

Tìm shell.php?cmd= và bunnylock.bin và thấy kẻ tấn công đã thực thi thành công webshell và chạy lệnh ransomware ./bunnylock.bin.

### Bước 4 - Tương quan dữ liệu từ log tường lửa

Chuyển sang log tường lửa (sourcetype=firewall_logs) và tìm các kết nối từ máy chủ web bị xâm nhập (src_ip=10.10.1.5) đến IP của kẻ tấn công (dest_ip=<IP C2>). Thấy các kết nối C2 được tường lửa cho phép (action=ALLOWED) và tính tổng dung lượng dữ liệu đã bị đánh cắp bằng stats sum(bytes_transferred).

### Bước 5 - Kết luận

Tổng hợp toàn bộ hành trình: kẻ tấn công đã trinh sát, thử Path Traversal, tấn công SQL Injection, tải webshell, thực thi ransomware, đánh cắp dữ liệu và thiết lập kênh liên lạc C2.

---

## 5. Các truy vấn mẫu

### Tìm top 5 IP tấn công

index=main sourcetype=web_traffic 
user_agent!=*Mozilla* 
user_agent!=*Chrome* 
user_agent!=*Safari* 
user_agent!=*Firefox*
| stats count by client_ip
| sort -count
| head 5

### Tìm Path Traversal

index=main sourcetype=web_traffic 
client_ip="<REDACTED>" 
AND (path="*..\/..\/*" OR path="*..%2F*")
| table _time, path, user_agent, status

### Tìm SQL Injection

index=main sourcetype=web_traffic 
client_ip="<REDACTED>" 
AND user_agent IN ("*sqlmap*", "*Havij*")
| table _time, path, status

### Tìm Webshell và RCE

index=main sourcetype=web_traffic 
client_ip="<REDACTED>" 
AND path IN ("*shell.php?cmd=*", "*bunnylock.bin*")
| table _time, path, user_agent, status

### Tính tổng dung lượng dữ liệu bị đánh cắp (log tường lửa)

sourcetype=firewall_logs 
src_ip="10.10.1.5" 
dest_ip="<REDACTED>" 
action="ALLOWED"
| stats sum(bytes_transferred) as total_bytes

### Xác định thời điểm tấn công

index=main sourcetype=web_traffic
| timechart span=1d count
| sort by count
| reverse
### Xem tất cả các field có trong một log

sourcetype=firewall_logs | table *
(Lệnh này giúp biết tên chính xác các field để dùng trong truy vấn)

---

## 6. Các giai đoạn tấn công

Khi điều tra, sẽ thường gặp các giai đoạn tấn công sau đây. Nhận diện được kẻ tấn công đang ở giai đoạn nào sẽ giúp phản ứng kịp thời.

### Giai đoạn 1 - Trinh sát (Reconnaissance)

Kẻ tấn công dùng các công cụ như curl, wget, nmap để quét tìm file cấu hình, thư mục nhạy cảm. Dấu hiệu trong log: path như /.env, /*phpinfo*, /.git*. User_agent thường là curl/* hoặc wget/*. Mã trạng thái thường là 404, 403, 401.

### Giai đoạn 2 - Liệt kê và kiểm tra lỗ hổng (Enumeration)

Kẻ tấn công thử các kỹ thuật như Path Traversal (../../), Open Redirect (redirect), hoặc các tham số đặc biệt. Dấu hiệu trong log: path chứa ../../ hoặc redirect. Mã trạng thái có thể là 200 (thành công) hoặc 404/403.

### Giai đoạn 3 - Khai thác (Exploitation)

Kẻ tấn công dùng các công cụ tự động như sqlmap, Havij để khai thác SQL Injection, hoặc gửi các payload RCE. Dấu hiệu trong log: user_agent chứa sqlmap, Havij. Path chứa các payload SQL như SLEEP(5) (time-based) hoặc UNION SELECT. Mã trạng thái 504 thường là dấu hiệu của time-based SQL Injection thành công.

### Giai đoạn 4 - Đánh cắp dữ liệu (Exfiltration)

Kẻ tấn công tải xuống các file nhạy cảm như backup, log, cơ sở dữ liệu. Dấu hiệu trong log: path chứa backup.zip, logs.tar.gz, dump.sql. User_agent thường là curl, wget, zgrab.

### Giai đoạn 5 - Webshell và RCE

Kẻ tấn công tải lên webshell (shell.php, cmd.php) để duy trì quyền truy cập và thực thi lệnh từ xa. Dấu hiệu trong log: path chứa shell.php?cmd=, cmd.php, bunnylock.bin. Mã trạng thái 200 xác nhận webshell hoạt động.

### Giai đoạn 6 - C2 Communication

Máy chủ bị xâm nhập thiết lập kết nối ra ngoài đến máy chủ điều khiển của kẻ tấn công. Dấu hiệu trong log tường lửa: src_ip là máy chủ nội bộ, dest_ip là IP ngoài (C2), action=ALLOWED. Kết hợp với stats sum(bytes_transferred) để tính tổng dung lượng dữ liệu đã bị gửi ra.

---

## 7. Một số thuật ngữ

Benign - Hợp lệ, vô hại, không phải tấn công. Ví dụ: user_agent từ Chrome, Firefox là benign.

IoC (Indicator of Compromise) - Dấu hiệu cho thấy một cuộc tấn công đã xảy ra. IoC bao gồm: IP độc hại, domain độc hại, file hash, user_agent bất thường, path bất thường.

C2 (Command & Control) - Máy chủ điều khiển của kẻ tấn công. Sau khi xâm nhập thành công, máy chủ của nạn nhân sẽ kết nối đến C2 để nhận lệnh và gửi dữ liệu.

Webshell - Một file script (PHP, ASP, JSP) được tải lên máy chủ, cho phép kẻ tấn công điều khiển máy chủ từ xa thông qua trình duyệt.

RCE (Remote Code Execution) - Lỗ hổng cho phép kẻ tấn công thực thi các lệnh tùy ý trên máy chủ. RCE là một trong những lỗ hổng nguy hiểm nhất.

Path Traversal - Một kỹ thuật tấn công cho phép kẻ tấn công đọc các file nằm ngoài thư mục web bằng cách dùng ../../. Ví dụ: http://server.com/../../etc/passwd.

Exfiltration - Hành động đánh cắp dữ liệu từ hệ thống bị xâm nhập ra bên ngoài. Dữ liệu thường được nén và gửi qua mạng.

Traffic Spike - Một đột biến bất thường về số lượng log hoặc lưu lượng mạng trong một khoảng thời gian ngắn. Thường là dấu hiệu của một cuộc tấn công.

Pivoting - Kỹ thuật chuyển từ một nguồn log sang một nguồn log khác để tìm thêm thông tin. Ví dụ: từ log web sang log tường lửa để xác nhận C2 communication.

