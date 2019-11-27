## Crontab backup Mariadb


- Tạo 1 file backup.sh
```
vi backup.sh

#!/bin/bash
mysqldump -uroot -pngocpt replica_db > /root/database_$(date +"%H:%M:%S--%Y:%m:%d").sql

```
- Sau đó mình cho script này chạy định kỳ vào 15h bằng cách tạo một file crontab như sau:
```
crontab -e
```
Thêm vào file:
```
10 23 * * * root bash backup.sh
```
