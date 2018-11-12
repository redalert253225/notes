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

 Flask-appbuilder
--

* 建立虛擬環境

$ cd  ~/flaskAppBuilderLearn  #移動到你要安裝的資料夾

$ python3 -m venv myVenv   #create  venv  虛擬環境

$ source myVenv/bin/activate  #進入虛擬環境
```
(myVenv) [yu@archlinux flaskAppBuilderLearn]$
```
(myVenv) $ pip install --upgrade pip    #更新pip ,  pip 在p ython 3.4 以後即內建

* 安裝  flask-appbuilder  

（venv）$ pip install flask-appbuilder   

* 安裝  flask-mongoengine    #似乎是一種連接Mongodb的框架

（venv)$ pip install flask-mongoengine

* create flask-appbuilder    #在此之前先確認Mongodb資料庫要啟動狀態

$ fabmanager create-app

```
Your new app name: myApp
Your engine type, SQLAlchemy or MongoEngine [SQLAlchemy]: MongoEngine
Downloaded the skeleton app, good coding!
```

* 創建admin 帳號

(myVenv) $ cd myApp

(myVenv) $ fabmanager create-admin

```
Username [admin]: yu
User first name [admin]: Chang
User last name [user]: yujen
Email [admin@fab.org]: yu@yushei.me
Password:
Repeat for confirmation:
```

(venv)$ fabmanager run

 http://localhost:8080  check it
