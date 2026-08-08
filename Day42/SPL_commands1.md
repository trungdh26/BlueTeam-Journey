# Splunk - SPL command (Phần 2)

---

## Phần 1: Toán tử và cú pháp cơ bản trong SPL

### 1.1 Các loại toán tử quan hệ

"="; "!="; ">"; ">="; "<"; "<="

### 1.2 Các toán tử logic

Toán tử AND: Yêu cầu tất cả các điều kiện phải đúng. Trong Splunk, nếu bạn viết nhiều điều kiện liền nhau không có toán tử, mặc định nó sẽ hiểu là AND.

Toán tử OR: Yêu cầu ít nhất một trong các điều kiện đúng.

Toán tử IN: Là cách viết ngắn gọn hơn của OR cho nhiều giá trị. Đặc biệt hữu ích khi danh sách dài.

Toán tử NOT: Phủ định một điều kiện. Khác với != ở chỗ NOT có thể phủ định cả sự tồn tại của trường.

Phân biệt != và NOT:

    NOT UserName=* có nghĩa là tìm các sự kiện không có trường UserName (UserName rỗng hoặc không tồn tại).

    UserName!=Mark có nghĩa là trường UserName phải tồn tại và giá trị không bằng Mark.

### 1.3 Ký tự đại diện (Wildcards) và CIDR

Dấu * (sao): Đại diện cho bất kỳ chuỗi ký tự nào (có thể rỗng).

    status=*fail* sẽ tìm tất cả các giá trị có chứa từ "fail" như: failed, failure, appfail, something_fail.

    DestinationIp=172.* sẽ tìm tất cả các IP bắt đầu bằng 172. (ví dụ: 172.90.0.1, 172.18.5.22).

CIDR (Classless Inter-Domain Routing): Dùng để chỉ định một dải địa chỉ IP.

    DestinationIp=172.18.0.0/16 sẽ tìm tất cả các IP trong dải từ 172.18.0.0 đến 172.18.255.255.

Lưu ý quan trọng: CIDR chỉ dùng được cho các trường chứa địa chỉ IP.

### 1.4 Thứ tự ưu tiên và cách kiểm soát

Quy tắc quan trọng: Toán tử OR được thực hiện trước toán tử AND (nếu không có dấu ngoặc).

Dùng dấu ngoặc đơn (): Để kiểm soát thứ tự ưu tiên.

    (alice AND bob) OR charlie sẽ thực hiện AND trước, sau đó OR.

Dấu ngoặc kép "": Dùng để tìm kiếm cụm từ chính xác, thứ tự từ quan trọng.

    "failed login" chỉ tìm cụm từ "failed login" đúng thứ tự.

    failed login (không có ngoặc kép) sẽ tìm các sự kiện có cả từ failed và login, không cần đúng thứ tự.

---

## Phần 2: Các lệnh lọc và sắp xếp dữ liệu

Trong Splunk, các lệnh được kết nối với nhau bằng dấu pipe |. Dấu pipe chuyển đầu ra của lệnh này sang lệnh tiếp theo, cho phép bạn tinh chỉnh kết quả từng bước.

### 2.1 Lệnh fields

Mục đích: Chọn hoặc loại bỏ các trường muốn hiển thị trong kết quả.

Cách dùng: | fields field1 field2 field3 - Chỉ hiển thị các trường được liệt kê.

Loại bỏ trường: | fields - field_loai_bo - Ẩn trường đó đi.

Tác dụng: Giúp kết quả tìm kiếm gọn gàng hơn, đặc biệt khi log có hàng trăm trường.

Ví dụ: index=windowslogs | fields host User SourceIp

Lệnh này sẽ chỉ hiển thị 3 trường: host, User, và SourceIp. Tất cả các trường khác bị ẩn.

### 2.2 Lệnh dedup

Mục đích: Loại bỏ các giá trị trùng lặp trong kết quả tìm kiếm.

Cách dùng: | dedup field_name - Giữ lại 1 sự kiện duy nhất cho mỗi giá trị của field đó.

Lưu ý quan trọng: dedup chỉ giữ lại sự kiện đầu tiên mà nó tìm thấy cho mỗi giá trị.

    Ví dụ: Nếu IP 192.168.1.1 có cả log 4624 (thành công) và 4625 (thất bại), dedup SourceIp sẽ chỉ giữ lại một trong hai (log đầu tiên gặp).

Do đó: dedup KHÔNG phải là công cụ để phân tích số lượng log thành công/thất bại. Để làm việc đó, bạn cần dùng stats.

Ví dụ: index=windowslogs | fields EventID User Image Hostname SourceIp | dedup SourceIp

Lệnh này trả về danh sách các IP duy nhất đã xuất hiện trong log, mỗi IP chỉ xuất hiện 1 lần.

### 2.3 Lệnh rename

Mục đích: Đổi tên một trường trong kết quả tìm kiếm.

Cách dùng: | rename old_name as new_name

Tác dụng: Cải thiện khả năng đọc, đặc biệt hữu ích khi tạo báo cáo cho ban lãnh đạo.

    Ví dụ đổi tên trường :  index = windowslogs | fields EventID User Image Hostname SourceIp | rename User as Employee

    Ví dụ làm phẳng JSON subfields:

    Với log JSON như {"request": {"path": "/admin", "ip": "10.0.0.2"}} Splunk tạo ra hai trường: request.path và request.ip 

    -> Có thể dùng rename để loại bỏ tiền tố "request.":

    index=jsondata | rename request.* as *

    -> Sau lệnh này, request.path trở thành path, request.ip trở thành ip.

### 2.4 Lệnh regex

Mục đích: Lọc kết quả bằng biểu thức chính quy (Regular Expression). Sử dụng khi cần tìm các mẫu phức tạp mà toán tử thông thường không làm được.

Cách dùng: | regex field_name = "pattern"

Lưu ý: Splunk sử dụng PCRE (Perl Compatible Regular Expressions).

    Ví dụ:  index = windowslogs | regex Image = "\.exe$"

    -> Lệnh này tìm các sự kiện có trường Image kết thúc bằng .exe. Dấu $ có nghĩa là "kết thúc chuỗi".

---

## Phần 3: Biểu thức chính quy (Regex) - Kiến thức cần nhớ

### 3.1 Các ký tự cơ bản

Dấu chấm (.): Đại diện cho bất kỳ 1 ký tự nào (trừ xuống dòng).

    Ví dụ: a.c sẽ khớp với abc, aac, a1c.

Dấu sao (*): Lặp lại ký tự đứng trước nó 0 hoặc nhiều lần.

    Ví dụ: ab*c sẽ khớp với ac (0 lần b), abc (1 lần b), abbbc (nhiều lần b).

    => Quan trọng: Trong Regex, * KHÔNG có nghĩa là "bất kỳ chuỗi nào" như trong Command Line.

Dấu cộng (+): Lặp lại ký tự đứng trước nó 1 hoặc nhiều lần.

    Ví dụ: ab+c sẽ khớp với abc, abbbc, nhưng KHÔNG khớp với ac.

Dấu hỏi (?): Lặp lại ký tự đứng trước nó 0 hoặc 1 lần.

    Ví dụ: ab?c sẽ khớp với ac và abc, nhưng KHÔNG khớp với abbc.

### 3.2 Các ký tự đặc biệt về vị trí

Dấu mũ (^): Khớp với vị trí bắt đầu của chuỗi.

    Ví dụ: ^Hello chỉ khớp với chuỗi bắt đầu bằng "Hello".

Dấu đô la ($): Khớp với vị trí kết thúc của chuỗi.

    Ví dụ: world$ chỉ khớp với chuỗi kết thúc bằng "world".

Sự khác biệt: Bạn không thể thay thế ^Hello bằng Hello* vì Hello* có nghĩa là "Hell" theo sau bởi 0 hoặc nhiều chữ "o", không phải là "bắt đầu bằng".

### 3.3 Các lớp ký tự và khoảng

- [abc]: Khớp với 1 trong các ký tự a, b, hoặc c.

- [a-z]: Khớp với bất kỳ chữ cái thường từ a đến z.

- [0-9]: Khớp với bất kỳ chữ số từ 0 đến 9.

- \d: Khớp với bất kỳ chữ số (tương đương [0-9]).

- \w: Khớp với ký tự từ, số, và gạch dưới (a-z, A-Z, 0-9, _).

- \s: Khớp với khoảng trắng (dấu cách, tab, xuống dòng).

### 3.4 Số lần lặp cụ thể

{n}: Lặp lại chính xác n lần.

    Ví dụ: \d{4} khớp với đúng 4 chữ số (2024, 1999).

{n,m}: Lặp lại từ n đến m lần.

    Ví dụ: \d{2,4} khớp với 2 đến 4 chữ số (12, 123, 1234).

### 3.5 Nhóm và OR

- (ab): Nhóm các ký tự thành một đơn vị.

    Ví dụ: (ab)+ khớp với ab, abab, ababab.

- "|" (Pipe trong Regex): Toán tử HOẶC (OR).

    Ví dụ: (cat|dog) khớp với từ "cat" hoặc từ "dog".

- Phân biệt pipe trong Regex và pipe trong Splunk:

    Regex |: Là toán tử HOẶC, dùng để lựa chọn giữa các mẫu.

        Ví dụ: (admin|guest) tìm admin HOẶC guest.

    SPL |: Là đường ống (pipe), dùng để chuyển dữ liệu từ lệnh này sang lệnh khác.

        Ví dụ: index=main | stats count nghĩa là lấy dữ liệu, sau đó đếm.

