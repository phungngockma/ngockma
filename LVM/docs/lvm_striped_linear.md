# Mục lục 
1.[So sánh hai kiểu dữ liệu](#a)  
2.[Ưu nhược điểm của từng kiểu dữ liệu](#b)  
3.[Cách thực hiện tạo một Logical Volume với LVM Stripe, LVM linear](#c)  



# Tìm hiểu Logical Volume Manager(LVM) linear và striped

Vậy ở một disk sẽ lưu trữ dữ liệu ra sao và kiểu lưu dữ liệu nào sẽ là tối ưu.Ở đây có hai kiểu lưu trữ trong disk là linear và striped

<a name="a">

## 1.So sánh hai kiểu dữ liệu </a>  
Ta có thể hình dung được cách thức hoạt động của cơ chế theo 2 hình ảnh sau:

- Đối với LVM Linear:

![](../images/linear-read-write-pattern.gif) 

- Đối với LVM Stripe:

![](../images/striped-read-write-pattern.gif)

Tóm lại, ta được so sánh như sau:

![](../images/a12.png) 

Khi ta lưu trữ ổ dữ liệu vào ổ đĩa thì ta sẽ có hai kiểu lưu trữ như trên đó là linear và striped. Giả sử ta có các phân vùng từ b tới i như trên thì các kiểu lưu trữ sẽ được lưu trữ như sau

- Linear : Dữ liệu sẽ được lưu hết phân vùng này rồi bắt đầu chuyển sang phân vùng khác để lưu trữ
- Striped: sẽ chia đều các dữ liệu ra và ghi vào các phân vùng đã có. Và cách chia dữ liệu ra bao nhiêu thì được định sẵn bởi người cài đặt nó

<b name="b">

## 2. Ưu điểm và nhược điểm của hai kiểu lưu trữ</b>
### Linear

- Ưu điểm : Các dữ liệu tập trung vào một phân vùng sẽ dễ dàng quản lý
- Nhược điểm : Khi bị mất dữ liệu sẽ mất hết dữ liệu của một phần đó. Làm việc chậm hơn bởi vì chỉ có một phân vùng mà trong khi các khu vừng khác không hoạt động
### Striped

- Ưu điểm: Tốc độ sẽ nhanh hơn vì tất cả các phân vùng sẽ cùng làm việc. Tốc độ đọc và ghi cũng nhannh hơn phương pháp Linear
- Nhược điểm: Khi mất dữ liệu ở một phân vùng thì sẽ bị mất và ảnh hưởng rất nhiều dữ liệu bởi vì mỗi dữ liệu đều được lưu ở nhiều phân vùng khi sử dụng phương pháp striped

<c name="c">

## 3. Cách thực hiện tạo một Logical Volume với LVM Stripe, LVM linear</c>

Ta thực hiện 1 ví dụ để làm rõ về 2 kiểu dữ liệu stripe và linear. Ta cần chuẩn bị:
- Bao gồm 3 disk : vda, vdb và vdc
- Cài các gói sau : wget, bwn-ng

### Bước 1: Cài đặt lệnh wget
```
yum install wget 
```
![](../images/a13.png)

### Bước 2 : Cài lệnh giám sát quá trình đọc ghi ổ đĩa bwn-ng

![](../images/b1.png)

### Bước 3: Tạo ra các phân vùng để tạo 2 logical có 2 kiểu lưu trữ riêng biệt

![](../images/b2.png)

### Bước 4 : Tạo ra các Volume Group 

![](../images/b3.png) 

### Bước 5: Ta sẽ tạo ra một logical với kiểu lưu trữ là linear và một logical với kiểu lưu trữ là striped. Ở đây tôi sẽ tạo ra logical có tên là linear_lv với group1 theo cú pháp 
```
lvcreate --extents (số %)FREE --name (tên logical)
```
và striped logical với group2 theo cú pháp:
```
lvcreate --extents N%FREE --stripes (số physical) --stripesize (số dung lượng) --name (tên logical) (tên group )
```

![](../images/b5.png) 

### Bước 6: Sau đó ta đi tạo định dạng cho logical để có thể mount lại nó vào thư mục và dùng chúng  

Ta dùng lệnh sau:
```
mkfs -t ext4 /dev/vg1/lv-linear  
mkfs -t ext4 /dev/vg2/lv-striped
```

![](../images/b6.png)

![](../images/b7.png)

### Bước 7: Ta mount nó lại vào cây thư mục root là có thể sử dụng chúng. Và để kiểm tra lại xem logical đã được mount hay chưa ta sử dụng lệnh df -h

Ta cần tạo thư mục để mount logical vào thư mục đó:
```
mkdir linear stripes 
```

Thực hiện câu lệnh mount:
```
mount /dev/vg1/lv-linear /root/linear/
```

![](../images/b8.png)

### Bước 8: Copy file root vào 2 logical này để xem tốc độ độc ghi của nó và cách lưu trữ dữ liệu.

Sử dụng với linear ta sử dụng 2 tab terminal với 2 lệnh sau:
```
dd if=<địa chỉ đầu vào> of=<địa chỉ đầu ra> option
```

Trong đó:

- if= địa chỉ nguồn của dữ liệu nó sẽ bắt đầu đọc
- of= viết đầu ra của file
- option : các tùy chọn cho câu lệnh

![](../images/b14.png)

Với tab terminal thứ 2 ta thực hiện lệnh sau:

```
bwm-ng -u bytes -i disk
```
![](../images/b15.png)

kết quả ta thấy được rằng chỉ có ổ sdb1 là đang chạy để lưu trữ khi ta copy phân vùng.Ta copy sẽ mất 93,0278s thì hoàn thành được.

Sử dụng với cách lưu trữ striped :

![](../images/b13.png)

![](../images/b12.png)

Kết quả ở logical striped ta thấy rằng cả physical sdb2 và sdc2 cùng chạy để có thể lưu trữ được khi ta copy phân vùng. Và khi ta để ý rằng copy sẽ mất 88,6238s là hoàn thành được.



