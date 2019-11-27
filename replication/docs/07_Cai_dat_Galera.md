# Hướng dẫn cài đặt Galera cho MariaDB trên CentOS 8
## Mô hình


## Bước 1 : Cài đặt MariaDB Server
- Mở file repo của cả 3 node :
```
# vi /etc/yum.repos.d/MariaDB10.1.repo
```
- Chèn thông tin sau và lưu file:
```
# MariaDB 10.1 CentOS repository list
# https://downloads.mariadb.org/mariadb/repositories/
[mariadb]
name = MariaDB
baseurl = https://yum.mariadb.org/10.1/centos7-amd64
gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
gpgcheck=1
```
- Cài đặt MariaDB server sử dụng câu lệnh sau:
```
# yum install MariaDB-server MariaDB-client MariaDB-common -y
```
- Sau khi các gói được cài đặt, chúng tôi sẽ bảo mật MariaDB bằng lệnh sau.
```
# mysql_secure_installation
```
- Galera có thể sử dụng rsync để thực hiện sao chép giữa nút cụm. Ngoài ra, chúng tôi sẽ xác minh bằng lsof để đảm bảo MariaDB liên kết với cổng chính xác. Vì vậy, chúng ta cần cài đặt cả gói rsync và lsof:
```
# yum install rsync lsof -y
```
- Cho phép dịch vụ MariaDB tự động khởi động lại trong khi khởi động lại:
```
# systemctl enable mariadb
```

## Bước 2 : Cấu hình node Galera Master

- Ta cần thêm nội dung sau vào tệp cấu hình MariaDB trong /etc/my.cnf.d/server.cnf trong phần [galera] :

```
binlog_format=ROW
default-storage-engine=innodb
innodb_autoinc_lock_mode=2
bind-address=
wsrep_on=ON
wsrep_provider=/usr/lib64/galera/libgalera_smm.so
wsrep_cluster_address="gcomm://10.10.0.11,10.10.0.12,10.10.0.13"

## Galera Cluster Configuration
wsrep_cluster_name="galeracluster1"

## Galera Synchronization Configuration
wsrep_sst_method=rsync

## Galera Node Configuration
wsrep_node_address=""
wsrep_node_name="node1"
```

- Lưu ý:

    - `wsrep_cluster_address` : Danh sách các node thuộc Cluster, sử dụng địa chỉ IP (Trong bài lab, tôi sẽ sử dụng dải IP Replicate 10.10.11.86, 10.10.11.87, 10.10.11.88)
    - `wsrep_cluster_name`: Tên của cluster
    - `wsrep_node_address`: Địa chỉ IP của node đang thực hiện
    - `wsrep_node_name`: Tên node (Giống với hostname)

- Cần thêm vị trí log vào phần [mysql] :
```
log_error=/var/log/mariadb.log
```
- Thoát và lưu file, vì chưa có file đó nên ta cần tạo mới:
```
touch /var/log/mariadb.log
```
- Cung cấp quyền phù hợp để có thể đăng nhập:
```
chown mysql:mysql /var/log/mariadb.log
```

## Bước 3 : Tạo Galera Cluster và xác minh ports
- Tạo ra các cụm bằng câu lệnh sau:
```
galera_new_cluster
```
- Lưu lượng truy cập sao chép Galera diễn ra trên cổng 4567. Để xác minh Galera đã ràng buộc với các cổng chính xác, chúng tôi sẽ sử dụng lsof như sau:
```
lsof -i:4567
 COMMAND PID USER FD TYPE DEVICE SIZE/OFF NODE NAME
 mysqld 2563 mysql 11u IPv4 30210 0t0 TCP *:tram (LISTEN)
```
- MariaDB lắng nghe các kết nối máy khách trên cổng 3306. Chúng tôi sẽ sử dụng lsof để xác minh cổng đang lắng nghe: 

```
lsof -i:3306
 COMMAND PID USER FD TYPE DEVICE SIZE/OFF NODE NAME
 mysqld 2563 mysql 26u IPv4 30210 0t0 TCP *:mysql (LISTEN)
```

## Bước 4 : Add quy tắc firewall 

Để đảm bảo lưu lượng truy cập không bị chặn, chúng tôi sẽ mở một số cổng bằng tường lửa như sau:

```
# firewall-cmd --zone=public --add-service=mysql --permanent
# firewall-cmd --zone=public --add-port=3306/tcp --permanent
# firewall-cmd --zone=public --add-port=4567/tcp --permanent
# firewall-cmd --zone=public --add-port=4567/udp --permanent
```

Restart firewall :

```
firewall-cmd --reload
```

## Bước 5 : Xác minh node Galera Master

- Node master Galera đầu tiên sẽ hoạt động , ta sử dụng lệnh sau để xác minh size của cụm :
```
mysql -uroot -p

MariaDB [(none)]> SHOW STATUS LIKE 'wsrep_cluster_size';
   +--------------------+-------+
   | Variable_name | Value | 
   +--------------------+-------+
   | wsrep_cluster_size | 1 |
   +--------------------+-------+
```

## Bước 6 : Add node vào Galera Cluster 

- Vào file /etc/my.cnf.d/server.cnf trong phần [galera] :
```
wsrep_node_address="10.10.0.12"
wsrep_node_name="node2"
wsrep_node_address="10.10.0.13"
wsrep_node_name="node3"
```
- Khởi động lại MariaDB 

```
systemctl start mariadb
```
Khi chúng ta nối từng nút vào cụm, wsrep_cluster_size sẽ tăng lên. Sau khi thêm tất cả ba nút, chúng ta có thể kiểm tra lại trạng thái MariaDB của bất kỳ nút nào:
```
MariaDB [(none)]> SHOW STATUS LIKE 'wsrep_cluster_size';
   +--------------------+-------+
   | Variable_name | Value |
   +--------------------+-------+
   | wsrep_cluster_size | 3 |
   +--------------------+-------+
```

- Ta có thể xem cấu hình đầy đủ của Galera bằng cách nhập :
```
MariaDB [(none)]> show status like 'wsrep%';
```

## Bước 7 : Replication Galera Cluster 

- Ta có thể kiểm tra sao chép giữa các node cụm đang hoạt động như mong đợi bằng cách tạo cơ sở dữ liệu kiểm tra trên một node và xem danh sách cơ sở dữ liệu trên một nút khác.
- 

## TÀI LIỆU THAM KHẢO 

- https://www.symmcom.com/docs/how-tos/databases/how-to-configure-mariadb-galera-cluster-on-centos-7
- https://ahmermansoor.blogspot.com/2019/02/install-mariadb-galera-cluster-on-centos-7.html
- https://ahmermansoor.blogspot.com/2019/02/install-mariadb-galera-cluster-on-centos-7.html
