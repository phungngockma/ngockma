## 1.
```
#sed 's/# urls = \["http:\/\/127.0.0.1:8086"]/urls = \["http:\/\/192.168.69.69:8086"]/g' filethuchanh.conf
```
## 2.
```
sed 's/# \[\[inputs.net]]/\[\[inputs.net]]/g' filethuchanh.conf
```
## 3.
```
sed 's/# username = "telegraf"/username = "nguoidung@hocchudong.com"/g' filethuchanh.conf
```
## 4.
```
sed 's/# password = "metricsmetricsmetricsmetrics/password = "Matkhau@ok$#/g' filethuchanh.conf
```