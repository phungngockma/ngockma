# Các lệnh hữu ích để quản lý máy chủ web Apache trong Linux

## Cài đặt máy chủ Apache
Để cài đặt máy chủ web Apache, hãy sử dụng trình quản lý gói phân phối mặc định của bạn:

- Trên Debian / Ubuntu
```
sudo apt install apache2
```
- Trên RHEL / CentOS
```
sudo yum install httpd
```
## Kiểm tra phiên bản Apache

```
sudo httpd -v 
```
HOẶC
``` 
sudo apache2 -v
```
## Kiểm tra lỗi cú pháp cấu hình Apache
Để kiểm tra các tệp cấu hình Apache của bạn xem có lỗi cú pháp nào không, hãy chạy lệnh sau, lệnh này sẽ kiểm tra tính hợp lệ của các tệp cấu hình, trước khi khởi động lại dịch vụ.
```
sudo httpd -t 
HOẶC 
sudo apache2ctl -t
```
## Start Apache Service
Để bắt đầu dịch vụ Apache , hãy chạy lệnh sau:
```
sudo systemctl start httpd
```
## Reload Apache Service
Nếu bạn đã thực hiện bất kỳ thay đổi nào đối với cấu hình máy chủ Apache, bạn có thể tải lại cấu hình của nó bằng cách chạy lệnh sau:
```
sudo systemctl reload httpd
```
## Stop Apache Service
Để dừng dịch vụ Apache , sử dụng lệnh sau.
```
sudo systemctl stop httpd
```
## Xem trạng thái Apache

Để kiểm tra trạng thái của dịch vụ Apache, sử dụng lệnh sau:
```
sudo systemctl status apache2
HOẶC
sudo systemctl status httpd
```
