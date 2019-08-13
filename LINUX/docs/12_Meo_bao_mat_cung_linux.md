# Mẹo bảo mật cứng cho máy chủ Linux

Bảo mật máy chủ Linux của bạn là rất quan trọng để bảo vệ dữ liệu, tài sản trí tuệ và thời gian của bạn khỏi tay kẻ bẻ khóa / tin tặc. Quản trị viên hệ thống chịu trách nhiệm bảo mật Máy chủ Linux.

![](../images/m1.jpg)

## 1. Mã hóa truyền dữ liệu cho máy chủ Linux
Tất cả dữ liệu được truyền qua mạng được mở để theo dõi. Mã hóa dữ liệu truyền bất cứ khi nào có thể bằng mật khẩu hoặc sử dụng keys.  
1. Sử dụng scp, ssh , rsync hoặc sftp để truyền tệp.
- scp (Secure Copy) là một ứng dụng sử dụng giao thức SSH thường dùng để copy dữ liệu từ máy linux sang linux.
- rsync (remote sync) là 1 công cụ dùng để sao chép và đồng bộ file/thư mục.
- sftp (ssh file transfer protocol) chuyển an toàn bằng phương pháp mã hóa.

2. GnuPG ( GNU Privacy Guard)cho phép mã hóa và ký dữ liệu và liên lạc của bạn, có hệ thống quản lý khóa linh hoạt cũng như các mô-đun truy cập cho tất cả các loại thư mục khóa công khai.      
3. OpenVPN là phần mềm VPN ( virtual private network ) miễn phí và mã nguồn mở dành cho Debian Linux 9. Nó thực hiện mở rộng mạng bảo mật lớp 2 hoặc 3 OSI bằng giao thức SSL / TLS.  
4. Lighttpd SSL : Cài đặt và cấu hình HTTPS.   
5. Apache SSL : Cài đặt và cấu hình HTTPS.  
6. Cách cấu hình Nginx với chứng chỉ SSL mã hóa miễn phí trên Debian hoặc Ubuntu Linux  

## 2. Tránh sử dụng các dịch vụ FTP, Telnet và Rlogin / Rsh trên Linux

- `FTP` - File Transfer Protocol (Giao thức truyền tải tập tin) được dùng trong việc trao đổi dữ liệu trong mạng thông qua giao thức TCP/IP, thường hoạt động trên 2 cổng là 20 và 21.  
Với giao thức này, các máy client trong mạng có thể truy cập đến máy chủ FTP để gửi hoặc lấy dữ liệu. Điểm nổi bật là người dùng có thể truy cập vào máy chủ FTP để truyền và nhận dữ liệu dù đang ở xa.  
- Các lệnh `rlogin` và `rsh` để đăng nhập từ xa và thực thi các lệnh từ xa, `rlogin` sử dụng port 513, `rsh` sử dụng port 514.
- `Telnet` là một giao thức đơn giản nên có rất ít tính năng. Giao thức Telnet cung cấp các tính năng sau để quản lý hệ thống từ xa, sử dụng port 23.  

Lệnh `FTP / telnet / rsh` và các tệp được truyền có thể bị bắt bởi bất kỳ ai trên cùng một mạng bằng cách sử dụng gói sniffer. Giải pháp chung cho vấn đề này là sử dụng OpenSSH , SFTP hoặc FTPS (FTP qua SSL), bổ sung mã hóa SSL hoặc TLS vào FTP. Nhập lệnh yum sau để xóa NIS, rsh và các dịch vụ không phù hợp khác:
```
# yum erase xinetd ypserv tftp-server telnet-server rsh-server
```

Nếu bạn đang sử dụng máy chủ dựa trên Debian / Ubuntu Linux, hãy thử lệnh apt-get lệnh / apt để xóa dịch vụ không an toàn:
```
$ sudo apt-get --purge remove xinetd nis yp-tools tftpd atftpd tftpd-hpa telnetd rsh-server rsh-redone-server
```

## 3.  Giảm thiểu phần mềm để giảm thiểu lỗ hổng trong Linux
Tránh cài đặt phần mềm không cần thiết để tránh lỗ hổng trong phần mềm. Sử dụng trình quản lý gói RPM như yum hoặc apt-get hoặc dpkg để xem lại tất cả các gói phần mềm đã cài đặt trên một hệ thống. Xóa tất cả các gói không mong muốn.

```
# yum list installed
# yum list packageName
# yum remove packageName
```
OR
```
# dpkg --list
# dpkg --info packageName
# apt-get remove packageName
```

## 4. Một dịch vụ mạng trên mỗi hệ thống hoặc VM riêng biệt
Chạy các dịch vụ mạng khác nhau trên các máy chủ hoặc VM riêng biệt . Điều này giới hạn số lượng dịch vụ khác có thể bị xâm phạm.

## 5. Luôn cập nhật kernel và phần mềm Linux
Sử dụng trình quản lý gói RPM như yum và / hoặc apt-get hoặc dpkg để áp dụng tất cả các cập nhật bảo mật.
``` 
# yum update
```
HOẶC
``` 
# apt-get update && apt-get upgrade
```
## 6. SELinux

## 7. Tài khoản người dùng Linux và Chính sách mật khẩu mạnh
Sử dụng các lệnh useradd / usermod để tạo và duy trì tài khoản người dùng. Hãy chắc chắn rằng bạn có một chính sách mật khẩu tốt và mạnh mẽ.
## 8. Thiết lập mật khẩu an toàn trên Linux 
Để vô hiệu hóa lão hóa mật khẩu, hãy nhập: 
```
# chage -M 99999 userName
```
Để lấy thông tin hết hạn mật khẩu, hãy nhập: 
```
# chage -l userName
```
Các thiết lập về mật khẩu an toàn trên Linux đều được lưu tại `/etc/login.defs`. 
### 8.1. Thiết lập số ngày hết hạn của mật khẩu 
Ta truy cập vào file `/etc/login.defs` và tìm cụm `PASS_MAX_DAYS` và đặt số ngày ta muốn thiết lập :
```
PASS_MAX_DAYS 90
```
### 8.2. Thiết lập thời gian tồn tại của mật khẩu
Khi bạn thay đổi mật khẩu hay đặt mật khẩu cho user, mật khẩu đó sẽ tồn tại trong khoảng thời gian mà chúng ta quy định sau đó mới có thể đổi mật khẩu:  
Tìm cụm `PASS_MAX_DAYS` và đặt thời gian mật khẩu tồn tại, ở đây là 1 ngày:
```
PASS_MIN_DAYS 1
```
### 8.3. Cảnh báo thời gian hết hạn  
Thiết lập số ngày cảnh báo trước khi mật khẩu hết hạn.Thiết lập này chỉ áp dụng đối với user được tạo mới, không có tác dụng đối với user đang tồn tại trên hệ thống. Đối với user đang tồn tại trên hệ thống chúng ta sử dụng câu lệnh `change -W <số ngày> <user>`   
Tìm cụm `PASS_WARN_AGE` thay đổi số ngày trước khi hết hạn nhận cảnh báo:
```
PASS_WARN_AGE 5
```
### 8.4. Giới hạn mật khẩu đã được đặt trước đó
Hạn chế sử dụng mật khẩu đã được sử dụng.Người dùng không thể thiết lập password quá số lượt quy định.  
Mở file `/etc/pam.d/system-auth`:
```
# vi /etc/pam.d/system-auth
```
Tại dòng 16, đặt số lần mật khẩu đó được thiết lập:
```
password     sufficient     pam_unix.so sha512 shadow nullok try_first_pass
use_authtok remember=5
```
### 8.5. Đặt độ dài mật khẩu tối thiểu

Thiết lập độ dài ngắn nhất của mật khẩu. Người dùng không thể đặt mật khẩu ngắn hơn số ký tự quy định.

Thiết lập mật khẩu ngắn nhất là 8 kí tự:
```
# authconfig --passminlen=8 --update
```
### 8.6. Thiết lập độ phức tạp của mật khẩu theo lớp
Độ phức tạp của mật khẩu được thiết lập theo số class xuất hiện trong mật khẩu.
(kinds ⇒ UpperCase / LowerCase / Digits / Others)  
Cấu hình mật khẩu phải xuất hiện ít nhất 2 class:
```
# authconfig --passminclass=2 --update
```
### 8.7. Thiết lập số lần lặp kí tự
Đặt số lượng tối đa các ký tự liên tiếp được phép trong mật khẩu mới.
Số lượng ký tự liên tiếp được phép tối đa là 2.  
```
# authconfig --passmaxrepeat=2 --update
```
### 8.8. Thiết lập độ phức tạp đối với mật khẩu mới.
Yêu cầu ít nhất một ký tự chữ thường trong mật khẩu mới.  
```
authconfig --enablereqlower --update
```
Yêu cầu có ít nhất một ký tự in hoa :
```
authconfig --enablerequpper --update
```
Yêu cầu có ít nhất một ký tự số :
```
authconfig --enablereqdigit --update
```

### 8.9. Thiết lập độ dài monotonic.
Đặt độ dài tối đa của chuỗi ký tự đơn điệu trong mật khẩu mới.
Mở file `/etc/security/pwquality.conf`:
```
vi /etc/security/pwquality.conf
```
Thêm vào cuối file dòng cấu hình
```
maxsequence = 5
```
### 8.10. Thiết lập số lượng ký tự trong mật khẩu mới không được có trong mật khẩu cũ.

Mở file `/etc/security/pwquality.conf`:
```
vi /etc/security/pwquality.conf
```
Thêm vào cuối file dòng cấu hình sau:
```
difok = 5
```

### 8.11. Cấu hình danh sách các ký tự không được xuất hiện trong mật khẩu.
Mở file `/etc/security/pwquality.conf`:
```
vi /etc/security/pwquality.conf
```
### 8.12. Thêm vào cuối file dòng cấu hình với những từ không được phép xuất hiện trong mật khẩu:
```
badwords = denywords1 denywords2 denywords3
``` 

Đầu tiên, kiểm tra thuật toán đang dùng hiện tại :
```
authconfig --test | grep hashing
```
Thay đổi sang thuật toán mong muốn sử dụng, ở đây là SHA512
```
authconfig --passalgo=sha512 --update
```

## 9. Khóa tài khoản người dùng sau khi đăng nhập thất bại
Bạn có thể sử dụng lệnh faillog để hiển thị các bản ghi faillog hoặc để đặt giới hạn lỗi đăng nhập.Để xem các lần đăng nhập thất bại, hãy nhập: 
```
faillog
```
Để mở khóa tài khoản sau khi đăng nhập thất bại,hãy chạy: 
```
faillog -r -u userName
```
## 10. Xác minh không có tài khoản nào có mật khẩu trống?
Gõ lệnh sau 
```
# awk -F: '($2 == "") {print}' /etc/shadow
```
Khóa tất cả các tài khoản mật khẩu trống:
```
# passwd -l accountName
```
## 11. Liệt kê các cổng mở và chương trình liên quan
Để liệt kê tất cả các cổng mở ta dùng lệnh:
```
netstat -tulpn
```
HOẶC sử dụng lệnh ss như sau :
```
$ ss -tulpn
```
## 12. Cấu hình firewall dựa trên Iptables và TCPWrappers trên Linux 
Iptables là một chương trình ứng dụng không gian người dùng cho phép bạn định cấu hình tường lửa (Netfilter)  
Có thể ngăn chặn nhiều cuộc tấn công từ chối dịch vụ với sự trợ giúp của Iptables.
### 13. Cấu hình file Linux /etc/sysctl.conf  
Tệp `/etc/sysctl.conf` được sử dụng để định cấu hình các tham số kernel khi chạy. Linux đọc và áp dụng các cài đặt từ `/etc/sysctl.conf` tại thời điểm khởi động.  

Ví dụ :

```
# Turn on execshield
kernel.exec-shield=1
kernel.randomize_va_space=1
# Enable IP spoofing protection
net.ipv4.conf.all.rp_filter=1
# Disable IP source routing
net.ipv4.conf.all.accept_source_route=0
# Ignoring broadcasts request
net.ipv4.icmp_echo_ignore_broadcasts=1
net.ipv4.icmp_ignore_bogus_error_messages=1
# Make sure spoofed packets get logged
net.ipv4.conf.all.log_martians = 1
### 14.Phân vùng đĩa riêng cho hệ thống Linux
```

### 14. Phân vùng đĩa riêng cho hệ thống Linux  
Đảm bảo các hệ thống tệp sau được gắn trên các phân vùng riêng biệt:

- / usr
- / home
- / var và / var / tmp
- / tmp

Tạo các phân vùng riêng cho gốc máy chủ Apache và FTP. Chỉnh sửa tập tin / etc / fstab và đảm bảo bạn thêm các tùy chọn cấu hình sau:

- noexec : Ngăn chặn việc thực thi các chương trình , nhưng cho phép chạy scripts .

- no dev : Không cho sử dụng các thiết bị quan trọng trên partition này (ngăn không cho sử dụng sda , ...)

- nosuid : Không đặt quyền truy cập SUID / SGID trên phân vùng này 

Ví dụ:

Cấu hình file `/etc/fstab`
```
/dev/sda3  /ftpdata    ext4   defaults,nosuid,nodev,noexec 1 2 
```  
### 15. Disk Quotas
Disk Quotas là một giới hạn được thiết lập bởi một quản trị hệ thống mà hạn chế một số khía cạnh của hệ thống tập tin sử dụng trên hiện đại hệ điều hành . Chức năng của việc sử dụng Disk Quotas là phân bổ không gian đĩa hạn chế một cách hợp lý. 

### 16. Tắt IPv6 khi không sử dụng
### 17. 