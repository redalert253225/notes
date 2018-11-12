Mongodb
--
*  安裝 mongodb

$ sudo pacman -S mongodb

$ sudo pacman -S mongodb-tools

* 設定 mongdb  資料庫路徑

$ cat /etc/mongodb.conf   #查看設定檔
```
# See http://www.mongodb.org/display/DOCS/File+Based+Configuration for format details
# Run mongod --help to see a list of options

bind_ip = 127.0.0.1
quiet = true
dbpath = /var/lib/mongodb
logpath = /var/log/mongodb/mongod.log
logappend = true
```
 * 此時 mongodb 應該是空的,   如果有內容, 掛載後內容會隱藏, 而不是被移動到新磁區內

$ sudo ls -l /var/lib/mongodb  #查看dbpath 路徑
 ```
total 0
```

$ sudo mount /dev/sda7 /var/lib/mongodb  #將mongdb資料庫掛載到一個獨立磁區

$ reboot  #重新開機

* 啟動 mongodb

$ sudo systemctl start mongodb.service

* 確認 mongodb 狀態

$ sudo systemctl status mongodb.service
