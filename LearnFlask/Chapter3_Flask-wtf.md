建立框架  登入
--

 (venv) $ pip install flask-wtf    #安裝flask-wtf

* 在根目錄創建config.py檔

 config.py:                                  #used to defined as class variables

```
import os

class Config(object):
    SECRET_KEY = os.environ.get('SECRET_KEY') or 'you-will-never-guess'
```

myApp/\__init__.py: Flask configuration

```
from flask import Flask
from config import Config

app = Flask(__name__)
app.config.from_object(Config)             #導入config.py 的設定值

from myApp import routes
```

* 建立 form 設定檔

myApp/forms.py:
```
from flask_wtf import FlaskForm
from wtforms import StringField, PasswordField, BooleanField, SubmitField    
from wtforms.validators import DataRequired                         

class LoginForm(FlaskForm):                                        #設定 Login 框架
    username = StringField('Username', validators=[DataRequired()])
    password = PasswordField('Password', validators=[DataRequired()])
    remember_me = BooleanField('Remember Me')
    submit = SubmitField('Sign In')
```

```
變數名             框架類別          label名稱        字串驗證方法 可以附加多個
username = StringField('Username', validators=[DataRequired()])

StringField   字串輸入框架
PasswordField   密碼輸入框架
SubmitField   按鈕
validators=[DataRequired()]  檢查是不是空字串
```

myApp/templates/login.html:  #建立login頁面
```
{% extends "base.html" %}

{% block content %}
    <h1>Sign In</h1>
    <form action="" method="post" novalidate>      <!--建立表單   POST 傳遞-->
        {{ form.hidden_tag() }}                                                 <!--建立隱藏字串防止CSRF 攻擊  首先包含此方法 並定義 SECRET_KEY    flask-wtf  會自行判斷-->
        <p>
            {{ form.username.label }}<br>              <!--框架label位置 -->
            {{ form.username(size=32) }}<br>                 <!--框架位置,大小-->
            {% for error in form.username.errors %}         <!--處理 validators  的錯誤訊息-->
               <span style="color: red;">[{{ error }}]</span>   <!--文字顏色紅色-->
               {% endfor %}
           </p>
           <p>
               {{ form.password.label }}<br>
               {{ form.password(size=32) }}<br>
               {% for error in form.password.errors %}
               <span style="color: red;">[{{ error }}]</span>
               {% endfor %}
           </p>
           <p>{{ form.remember_me() }} {{ form.remember_me.label }}</p>
           <p>{{ form.submit() }}</p>
    </form>
{% endblock %}
```

myApp/routes.py:                                       #在 routes 新增 login
```
from myApp import app
from flask import render_template, flash, redirect, url_for    
from myApp.forms import LoginForm          

  # ...

@app.route('/login', methods=['GET', 'POST'])           #宣告接收 GET ,POST 傳遞方法,若沒宣告預設只接收GET
def login():
    form = LoginForm()
    if form.validate_on_submit():                #觸發form.submit() 執行  如果網頁以GET 傳送資料時, 此函數會回傳False
        flash('Login requested for user {}, remember_me={}'.format(
            form.username.data, form.remember_me.data))        #用flash儲存來自框架的訊息
        return redirect(url_for('index'))                        #redirect  引導網頁進入其他頁面  url_for 動態生成 url
    return render_template('login.html', title='Sign In', form=form)
```

myApp/templates/base.html: #編輯公共樣板 處理flash暫存的訊息
```
<html>
    <head>
        {% if title %}
        <title>{{ title }} - yuBlog</title>
        {% else %}
        <title>yuBlog</title>
        {% endif %}
    </head>
    <body>
        <div>
            yuBlog:
            <a href="{{ url_for('index') }}">Home</a>        <!--加入超連結-->
            <a href="{{ url_for('login') }}">Login</a>          
        </div>
        <hr> <!--水平線-->
        {% with messages = get_flashed_messages() %}    <!--用with 開啟flash暫存的訊息的訊息-->
        {% if messages %}                                                          <!--如果有訊息-->
        <ul>
            {% for message in messages %}                              <!--迴圈顯示所有訊息,  flashed_messages一旦被取用過就會被消除-->
            <li>{{ message }}</li>
            {% endfor %}
        </ul>
        {% endif %}
        {% endwith %}                                                         <!--關閉wish-->
        {% block content %}{% endblock %}
    </body>
</html>

```
