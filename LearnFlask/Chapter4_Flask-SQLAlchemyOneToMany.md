資料庫flask-sqlalchemy
--
* 安裝資料庫flask-sqlalchemy

(venv) $ pip install flask-sqlalchemy

* 安裝 flask-migrate

(venv) $ pip install flask-migrate


config.py:
```
import os
basedir = os.path.abspath(os.path.dirname(__file__))

class Config(object):
    # ...
    SQLALCHEMY_DATABASE_URI = os.environ.get('DATABASE_URL') or \
        'sqlite:///' + os.path.join(basedir, 'app.db')       #設定DB環境變數
    SQLALCHEMY_TRACK_MODIFICATIONS = False     #禁用追蹤 Flask-SQLAlchemy 的修改   這需要額外的內存
```

myApp/\__init__.py:          #Flask-SQLAlchemy and Flask-Migrate initialization
```
from flask import Flask
from config import Config
from flask_sqlalchemy import SQLAlchemy
from flask_migrate import Migrate

app = Flask(__name__)
app.config.from_object(Config)

db = SQLAlchemy(app)
migrate = Migrate(app, db)

from myApp import routes, models   #models 用於定義資料庫結構
```

* 建立定義資料庫格式的 class

myApp/models.py:  #User database model
```
from datetime import datetime
from myApp import db

class User(db.Model):                     #定義資料庫格式
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(64), index=True, unique=True)
    email = db.Column(db.String(120), index=True, unique=True)
    password_hash = db.Column(db.String(128))
    posts = db.relationship('Post', backref='author', lazy='dynamic')  
    #.relationship 建立關係   User.posts 將在資料庫去搜尋資料所有相關Post
    #.relationship(被連結類別, 提供被連結者反查詢方法  author.username , lazy='dynamic')

    def __repr__(self):                     #定義  如何shell在顯示
        return '<User {}>'.format(self.username)

class Post(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    body = db.Column(db.String(140))
    timestamp = db.Column(db.DateTime, index=True, default=datetime.utcnow)   #傳遞datetime.utcnow  function, 這裡沒有包含 () , 所以只有傳函式不是傳函的回傳值, 使時間是顯示使用者本地時間
    user_id = db.Column(db.Integer, db.ForeignKey('user.id'))    #表示參考 users table  裡的 id,  要設成實際資料庫名稱  所以u小寫

    def __repr__(self):
        return '<Post {}>'.format(self.body)
```

```
db.Column( ):
    db.String (64)   #字串長度
    index=True        #是否做為索引
    unique=True     #是否唯一
    primary_key     ?
    db.ForeignKey    # 設定參考的目標資料庫
    nullable=False     #是否可為空值
relationship():  #連結多個資料  
    第一個參數 為 連結資料庫的類別名稱
    backref   #定義被關係資料取用的名子  
        Post.author= User
        Post.author.username= User.username
    lazy      ?
```

* 建立資料庫執行所設定的變更

(venv) $ flask db init  #初始化資料庫

(venv) $ flask db migrate -m "users and post table"   #生成遷移庫

(venv) $ flask db upgrade  # 更新DB    

#sqlalchemy 會將classa名以"snake case" 為資料庫命名  EX:  class name= 'User' -> 'user'
#如果要自行定義名子    可以在class 建立  \__tablename__   給予字串
#用  (venv) $ flask db downgrade   回溯DB


*  用python 操作資料庫

 ```
>>> from myApp import db
>>> from myApp.models import User, Post
>>> u = User(username='susan', email='susan@example.com')
>>> db.session.add(u)  新增資料     #db.session.rollback()  刪除所有異動
>>> db.session.delete(u)   刪除資料
>>> db.session.commit()    執行所設定的變更
```

```
u=User(username='yu',email='yu@com')
>>> db.session.add(u)
>>> db.session.commit()
>>> p=Post(body='what do y say',author=User.query.get(1))
>>> db.session.add(p)
>>> db.session.commit()

>>> u=User.query.get(1)
>>> u                   
<User yu>    # __repr__ 設定
>>> u.username
'yu'
>>> u.email
'yu@com'
>>> u.posts        
<sqlalchemy.orm.dynamic.AppenderBaseQuery object at 0x7f2df169a048>
>>> u.posts.all()
[<Post what do y say>]

>>> p
<Post what do y say>
>>> p=Post.query.get(1)
>>> p.id
1
>>> p.body
'what do y say'
>>> p.timestamp
datetime.datetime(2018, 9, 4, 10, 30, 6, 508147)
>>> p.author    
<User yu>
>>> p.author.username
'yu'

>>> db.session.delete(u)
>>> db.session.delete(p)
>>> db.session.commit()
```
