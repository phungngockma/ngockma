# Điều gì xảy ra khi bạn nhập google.com vào hộp địa chỉ trình duyệt của bạn và nhấn enter?

# Mục lục
1.[Phím g được nhấn](#a)  
2.[Phím enter chạm đáy](#b)  
3.

<a name="a">

## 1. Phím g được nhấn </a>
- Khi bạn nhấn phím "g", trình duyệt sẽ nhận được sự kiện và các chức năng tự động hoàn tất sẽ xuất hiện.  
- Các thuật toán này sắp xếp và ưu tiên kết quả dựa trên lịch sử tìm kiếm, dấu trang, cookie và các tìm kiếm phổ biến từ internet nói chung. Khi bạn đang gõ "google.com", nhiều khối mã sẽ chạy và các đề xuất sẽ được tinh chỉnh với mỗi lần nhấn phím.

<b name="b">

## 2. Phím enter chạm đáy</b>
Bộ điều khiển bàn phím sau đó mã hóa mã khóa để vận chuyển đến máy tính. Điều này hiện nay gần như phổ biến trên kết nối Universal serial Bus (USB) hoặc Bluetooth, nhưng trong lịch sử đã vượt qua các kết nối PS / 2 hoặc ADB.  
Trong trường hợp bàn phím USB:
- Mạch USB của bàn phím được cung cấp qua chân 1 từ bộ điều khiển máy chủ USB của máy tính.
- Bộ điều khiển USB máy chủ thăm dò ý kiến ​​"điểm cuối" cứ sau ~ 10ms (giá trị tối thiểu được khai báo bởi bàn phím),nó nhận được giá trị mã khóa được lưu trữ trên nó.
- Giá trị này được chuyển đến USB SIE
- Giá trị của khóa sau đó được chuyển vào lớp trừu tượng phần cứng của hệ điều hành.  

Trong trường hợp Bàn phím ảo :
- Khi người dùng đặt ngón tay lên màn hình cảm ứng điện dung hiện đại, một lượng nhỏ dòng điện sẽ được chuyển đến ngón tay.Tạo ra sự sụt giảm điện áp tại điểm đó trên màn hình. Sau screen controller đó tăng một báo cáo gián đoạn tọa độ của phím bấm.
-  HĐH di động thông báo cho ứng dụng tập trung hiện tại của một sự kiện báo chí vào một trong các thành phần GUI của nó (mà bây giờ là các nút ứng dụng bàn phím ảo).
- Bàn phím ảo giờ đây có thể đưa ra một ng
àn phím ảo giờ đây có thể đưa ra một ngắt phần mềm để gửi tin nhắn 'phím được nhấn' trở lại HĐH.  

## 3. (Trên Windows) Một WM_KEYDOWN tin nhắn được gửi đến ứng dụng
Việc vận chuyển HID(Thiết bị giao diện con người) chuyển sự kiện xuống phím cho KBDHID.sys trình điều khiển chuyển đổi việc hành một `scancode`. 

## 4. Phân tích cú pháp URL
URL là viết tắt của "Uniform Resource Locator" được sử dụng để tham chiếu tới tài nguyên trên mạng Internet. URL tạo nên khả năng siêu liên kết cho các website. Mỗi tài nguyên khác nhau lưu trữ trên Internet được gán bằng một địa chỉ chính xác, địa chỉ đó chính là URL.
```
Protocol "http"
Use 'Hyper Text Transfer Protocol'
Resource "/"
Retrieve main (index) page
```

