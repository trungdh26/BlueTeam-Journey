PHẦN 1: REFLECTED XSS (XSS PHẢN CHIẾU)
1.1 Định nghĩa
Reflected XSS là loại tấn công xảy ra khi kẻ tấn công chèn mã độc vào URL hoặc form, và mã độc đó được phản chiếu ngay lập tức từ server trở lại trình duyệt của nạn nhân.

1.2 Ví dụ mô tả
Hacker tạo một link độc hại chứa script.

Gửi link đó qua email, tin nhắn, hoặc mạng xã hội.

Nạn nhân click vào link tưởng là link an toàn (vì domain đúng).

Server nhận request, không kiểm tra, và in thẳng nội dung script vào trang kết quả.

Trình duyệt nạn nhân nhận trang HTML và thực thi script.

1.3 Cách tấn công
Tìm các trang web có tham số trên URL (ví dụ: ?q=, ?id=, ?search=).

Chèn payload vào tham số đó.

Rút gọn link (nếu cần) để che giấu payload.

Gửi link cho nạn nhân bằng các phương thức lừa đảo.

1.4 Giải pháp phòng chống
Mã hóa đầu ra (Output Encoding): Chuyển đổi các ký tự đặc biệt như <, >, " thành HTML entities trước khi hiển thị.

Xác thực đầu vào (Input Validation): Chỉ cho phép dữ liệu hợp lệ (ví dụ: chỉ số, chữ cái).

Sử dụng hàm escape: Dùng các hàm như htmlspecialchars() (PHP), escape() (Python) để xử lý dữ liệu.

Content Security Policy (CSP): Hạn chế nguồn script được phép chạy.

PHẦN 2: STORED XSS (XSS LƯU TRỮ)
2.1 Định nghĩa
Stored XSS là loại tấn công xảy ra khi mã độc được lưu trữ vĩnh viễn trên server (trong database) và được hiển thị cho bất kỳ người dùng nào truy cập vào trang đó.

2.2 Ví dụ mô tả
Hacker viết một bình luận trên blog hoặc diễn đàn, nhưng trong bình luận có chèn script độc hại.

Bình luận đó được server lưu vào database.

Người dùng khác (kể cả Admin) mở trang xem bình luận đó.

Trình duyệt của họ parse và thực thi script.

Cookie của họ bị gửi về máy chủ của hacker.

2.3 Cách tấn công
Tìm các vị trí trên trang web có thể lưu dữ liệu của người dùng: comment, đánh giá sản phẩm, hồ sơ cá nhân, form liên hệ, tiêu đề bài đăng, tên file upload...

Gửi payload vào các trường đó.

Không cần lừa từng người, chỉ cần gửi 1 lần duy nhất.

Đợi nạn nhân truy cập trang và bị tấn công.

2.4 Giải pháp phòng chống
Mã hóa đầu ra (Output Encoding): Tương tự như Reflected XSS, mã hóa dữ liệu trước khi hiển thị.

Lọc dữ liệu đầu vào (Input Sanitization): Sử dụng các thư viện lọc HTML như DOMPurify để loại bỏ thẻ script nguy hiểm.

Prepared Statements: Ngăn chặn cả SQL Injection và góp phần bảo vệ dữ liệu trước khi lưu.

CSP: Chặn script không được phép chạy ngay cả khi đã được lưu vào database.

PHẦN 3: DOM-BASED XSS (XSS DỰA TRÊN DOM)
3.1 Định nghĩa
DOM-based XSS là loại tấn công xảy ra hoàn toàn phía client (trình duyệt), không cần sự tham gia của server. JavaScript của trang web tự lấy dữ liệu từ URL và chèn vào DOM một cách không an toàn.

3.2 Ví dụ mô tả
Trang web có chức năng lấy tham số từ URL và hiển thị lên màn hình bằng JavaScript.

Hacker tạo link chứa payload trong phần fragment (sau dấu #).

Nạn nhân click vào link.

Trình duyệt gửi request lên server (chỉ gửi phần domain và path, không gửi phần #).

Server trả về trang HTML bình thường.

JavaScript của trang chạy, đọc nội dung sau #, và chèn vào DOM bằng innerHTML hoặc document.write().

Trình duyệt parse và thực thi script.

3.3 Cách tấn công
Tìm các trang web có JavaScript sử dụng dữ liệu từ URL: window.location, location.hash, document.URL, document.referrer.

Tạo link với payload trong fragment (#<img src=x onerror=...>).

Gửi link cho nạn nhân.

Server KHÔNG hề biết vụ tấn công, WAF không thể chặn.

3.4 Giải pháp phòng chống
Không dùng innerHTML với dữ liệu từ URL: Thay vào đó dùng textContent hoặc innerText.

Không dùng document.write(): Đây là hàm rất nguy hiểm, nên tránh hoàn toàn.

Không dùng eval() với dữ liệu từ URL: eval() cho phép chạy mọi chuỗi như code JavaScript.

Mã hóa dữ liệu trước khi sử dụng: Dùng encodeURIComponent() để mã hóa input.

CSP (Content Security Policy): Dù server không thấy payload, CSP vẫn có thể chặn script lạ.