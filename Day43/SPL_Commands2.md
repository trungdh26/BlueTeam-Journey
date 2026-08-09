# SPL Command (2)


## PHẦN 1: CÁC LỆNH CƠ BẢN GIỐNG SQL

1. Lệnh table: Chọn và hiển thị các trường dạng bảng.

    Ví dụ: index=windowslogs| table _time EventID Hostname SourceName

2. Lệnh stats: Tính toán thống kê. Các hàm hay dùng: count, sum, avg, max, min.

    Ví dụ: ...| stats count by EventID (đếm số lượng theo từng EventID)

3. Lệnh sort: Sắp xếp kết quả theo thứ tự tăng dần(mặc định) hoặc giảm dần (thêm "| reverse")

    Ví dụ: \| sort -count (giảm dần, dấu - ở trước tên trường)

4. Lệnh where: Lọc kết quả sau khi đã xử lý.

    Ví dụ: \| where count > 10

5. Lệnh head: Lấy N dòng đầu tiên.

    Ví dụ: \| head 20

6. Lệnh rename: Đổi tên trường.

Ví dụ: \| rename User as Employee

7. Lệnh eval (phần cơ bản): Tạo trường mới, tính toán số học.

    Ví dụ: \| eval Total = Price * Quantity

---

## PHẦN 2: ĐIỂM MẠNH KHÁC BIỆT CỦA SPLUNK

1. Lệnh eventstats: Giống stats nhưng không làm mất dữ liệu chi tiết.
Nó tính toán thống kê và thêm kết quả vào mỗi dòng log, thay vì gom nhóm.

Ví dụ: Bạn có log VPN. Bạn muốn biết tổng số lần đăng nhập của mỗi user, nhưng vẫn muốn xem từng lần đăng nhập cụ thể.

    index=vpnlogs
    | eventstats count as total_logins by user
    | table _time user src_ip total_logins

Kết quả: Mỗi dòng log đều có cột total_logins hiển thị tổng số lần đăng nhập của user đó.

- Phân biệt với stats: stats → Gom nhóm, mất dữ liệu chi tiết.

eventstats → Giữ nguyên dữ liệu, thêm cột thông tin tổng hợp.

2. Lệnh iplocation: Từ địa chỉ IP, Splunk tự động tra ra quốc gia, thành phố, vùng miền.

Ví dụ: Bạn có log chứa IP nguồn. Bạn muốn phân tích xem user đăng nhập từ quốc gia nào để phát hiện bất thường.

    index=vpnlogs
    | iplocation src_ip
    | stats count by Country

Kết quả: Splunk tự động chuyển IP thành tên quốc gia, bạn không cần file tra cứu bên ngoài.


3. Lệnh lookup: Tra cứu và ghép thêm thông tin từ file bên ngoài (CSV, lookup table) vào log.

Ví dụ: Bạn có file employees.csv chứa Hostname và Department. Log của bạn chỉ có Hostname. Bạn muốn biết mỗi máy thuộc phòng ban nào.

    index=windowslogs
    | lookup employees.csv Hostname OUTPUT Department
    | table _time Hostname Department EventID

Kết quả: Mỗi dòng log có thêm cột Department.


4. Lệnh timechart: Vẽ biểu đồ số lượng sự kiện theo thời gian, tự động chia khoảng (giờ, ngày, tháng).

Ví dụ: Bạn muốn xem log tăng đột biến vào giờ nào trong ngày để phát hiện tấn công.
    index=windowslogs
    | timechart span=1h count

Kết quả: Biểu đồ dạng cột theo từng giờ, giúp phát hiện các đỉnh bất thường.


5. Lệnh regex: Lọc log bằng biểu thức chính quy (Regular Expression).

Ví dụ: Bạn muốn tìm tất cả log có trường Image kết thúc bằng .exe.

    index=windowslogs | regex Image="\.exe$"

Kết quả: Chỉ lấy các dòng có Image kết thúc bằng .exe.


6. Lệnh eval với hàm strftime: Trích xuất giờ, phút, ngày, tháng, năm từ timestamp một cách dễ dàng.

Ví dụ: Bạn muốn tính giờ đăng nhập (không cần giây) để phân tích theo khung giờ.

    | eval hour = strftime(_time, "%H")
    | stats count by hour

Kết quả: Đếm số login theo từng giờ trong ngày.

7. Subsearch + join: Lấy kết quả từ một tìm kiếm này làm điều kiện cho tìm kiếm khác. Ghép dữ liệu từ 2 nguồn khác nhau trong cùng 1 truy vấn.

Ví dụ: Sysmon log có LogonId, Security log có LogonType. Bạn muốn ghép 2 log để biết mỗi tiến trình được tạo ra từ loại đăng nhập nào.

    index=windowslogs EventID=1
    | join LogonId
        [search index=windowslogs EventID=4624
        | rename TargetLogonId as LogonId
        | fields LogonId LogonType IpAddress]
    | table _time Image User LogonType IpAddress

Kết quả: Một bảng duy nhất chứa cả thông tin tiến trình và loại đăng nhập.

---

## PHẦN 3: PHÁT HIỆN BẤT THƯỜNG (ANOMALY DETECTION)

Đây là phần nâng cao, ứng dụng các lệnh eventstats, eval, where để tìm ra các sự kiện bất thường.

1. Phát hiện bất thường theo quốc gia

- Ý tưởng: Mỗi user thường chỉ đăng nhập từ 1-2 quốc gia quen thuộc. Nếu một user đăng nhập từ quốc gia khác với tần suất rất thấp, đó là dấu hiệu đáng ngờ.

- Các bước thực hiện

    + Đếm tổng số lần đăng nhập của mỗi user.

    + Đếm số lần đăng nhập của mỗi user theo từng quốc gia.

    + Tính tần suất xuất hiện của quốc gia đó.

    + Lọc ra những cặp (user, quốc gia) có tần suất dưới ngưỡng (ví dụ 10%).

- Ví dụ:
    
    index=vpnlogs
    | eventstats count as logins_by_user by user 
    | eventstats count as logins_by_user_country by user src_country 
    | eval country_freq = logins_by_user_country / logins_by_user
    | where country_freq < 0.1
    | table _time user src_ip src_country country_freq

Kết quả: Phát hiện user kbrown và hmiller đăng nhập từ quốc gia bất thường.

2. Phát hiện bất thường theo giờ đăng nhập

- Ý tưởng: Mỗi user có thói quen đăng nhập riêng. Nếu họ đăng nhập vào giờ khác xa so với thói quen, đó là dấu hiệu đáng ngờ.

- Các biến số cần hiểu

    + hour: Giờ đăng nhập của sự kiện (dạng số, ví dụ 13.5).

    + typical_hour: Giờ đăng nhập trung bình của user đó.

    + stdev_hour: Độ lệch chuẩn của giờ đăng nhập (thể hiện tính đều đặn).

    + zscore: Mức độ bất thường. zscore > 3 là rất bất thường.

- Ví dụ:

    index=vpnlogs
    | eval hour = tonumber(strftime(_time, "%H")) + tonumber(strftime(_time, "%M"))/60
    | eventstats avg(hour) as typical_hour stdev(hour) as stdev_hour by user
    | eval zscore = abs(hour - typical_hour) / stdev_hour
    | where zscore > 3
    | eval hour = round(hour, 2), typical_hour = round(typical_hour, 2)
    | eval stdev_hour = round(stdev_hour, 2), zscore = round(zscore, 2)
    | table _time user src_ip src_country hour typical_hour stdev_hour zscore
    | sort - zscore

Kết quả: Phát hiện user jsmith đăng nhập lúc 18:30 trong khi thói quen là 13:30.

3. Phát hiện nâng cao: Impossible Travel (Di chuyển bất khả thi)

- Phát hiện một người đăng nhập từ hai địa điểm quá xa nhau trong thời gian ngắn.

- Cần kết hợp iplocation để lấy vị trí địa lý và eventstats để tính khoảng cách, thời gian giữa các lần đăng nhập.