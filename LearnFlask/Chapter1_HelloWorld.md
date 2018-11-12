Hellow world
--
flask的wsgi使用的是Werkzeug，模版使用為jinja2    

https://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world

cd to  folder which has 'venv'

$ source venv/bin/activate   #進入虛擬環境

(venv) $ mkdir myApp  #新增myapp資料夾


myApp/\__init__.py:
```
from flask import Flask

app = Flask(__name__)           #??

from myApp import routes
```

myApp/routes.py:    #Home page route
```
from myApp import app

@app.route('/')                 #如果網址 /  or  /index  則執行 def index
@app.route('/index')      #實際上 是將''路徑'' 和 ''函式 ''配對  傳進 app              
def index():
    return "Hello, World!"

```

yuBlog.py:                             
```
from myApp import app    #執行 myApp/__init__.py
```

 目前的目錄：flaskLearn/
 ```
flaskLearn/
    venv/
    myApp/
          __init__.py
          routes.py
    yuBlog.py
```

* 執行第一個網站 hellow world

  (venv) $ export FLASK_APP=yuBlog.py     #自訂環境變數  FLASK_APP='yuBlog.py'

  #flask command  依賴於 FLASK_APP 環境變量  來了解Flask應用程序的位置

  (venv) $ flask run                                                         #啟動 flask
 * Serving Flask app "microblog"
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)


* 在虛擬環境中 建制環境設定檔         #之後就不用再執行  (venv) $ export FLASK_APP=yuBlog.py

(venv) $ pip install python-dotenv                  #安裝python-dotenv

* 在根目錄(venv所在目錄)建立.flaskenv檔案  

.flaskenv
```
FLASK_APP=yuBlog.py
```

---
測試網頁執行成果

cd to  folder which has 'venv'

$ source myVenv/bin/activate   #進入虛擬環境

本機測試:

(venv) $ flask run         #啟動 flask

#127.0.0.1:5000  本機連線網址

其他電腦測試:

(myVenv) $ sudo flask run -h    'ip'   -p     'prot'    

(myVenv) $ sudo flask run -h 192.168.1.108 -p 5000      
 #同區網可以用  192.168.1.108:5000 連線 , 但是同時只能有一台連線


$ deactivate    #離開虛擬環境
