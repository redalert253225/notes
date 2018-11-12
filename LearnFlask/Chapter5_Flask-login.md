帳戶管理   登入  註冊
--
* 安裝登入插件

(venv) $ pip install flask-login   

* 設定載入模組

myApp/\__init__.py:        #Flask-Login initialization
```
# ...
from flask_login import LoginManager

app = Flask(__name__)
# ...
login = LoginManager(app)
login.login_view = 'login'    #讓flask_login知道處理登入的網頁是哪個
# ...

```
myApp/models.py:       #Password hashing and verification
```
from werkzeug.security import generate_password_hash, check_password_hash #werkzeug在安裝flask就已經安裝

from flask_login import UserMixin
from myApp import login
# ...

class User(UserMixin, db.Model):
    # ...

    def set_password(self, password):           # 設定密碼的hash值
        self.password_hash = generate_password_hash(password)

    def check_password(self, password):        # 確認密碼
        return check_password_hash(self.password_hash, password)

@login.user_loader             #對在Flask-Login登入者加載程序
def load_user(id):
    return User.query.get(int(id))  #取得的id為字串 要轉成整數query.get才能使用
```
UserMixin :    通過Flask-Login提供的方法UserMixin實做附加的屬性
```
is_authenticated:   True 表示帳戶憑證有效
is_active:    True 表示帳戶正在活動
is_anonymous:    True 表示匿名用戶
get_id():           取得帳戶id  取得的id為字串
```

* 設定網頁

myApp/routes.py:  #Login view function
```
# ...
from flask_login import current_user, login_user
from myApp.models import User

from flask_login import logout_user
from flask_login import login_required

from flask import request
from werkzeug.urls import url_parse

from myApp import db
from myApp.forms import RegistrationForm
# ...

@app.route('/')
@app.route('/index')
@login_required       #flask_login 藉此修飾符阻擋非登入用戶,當非法用戶會自動跳轉登入畫面
def index():
    # ...
    return render_template("index.html", title='Home Page', posts=posts)

@app.route('/login', methods=['GET', 'POST'])   # 登入
def login():
    if current_user.is_authenticated:   #確認是否登入, current_user 來自flask_login 的模組
        return redirect(url_for('index'))
    form = LoginForm()
    if form.validate_on_submit():
        user = User.query.filter_by(username=form.username.data).first()  #.filter_by() 查詢符合的資料 .first() 只找一筆
        if user is None or not user.check_password(form.password.data):
            flash('Invalid username or password')
            return redirect(url_for('login'))
        login_user(user, remember=form.remember_me.data)  #將current_user 設置成user, login_user()來自flask_login
        next_page = request.args.get('next')     #透過  flask_login 取得跳入登入之前的頁面
        if not next_page or url_parse(next_page).netloc != '':  #確認URL 是相對還是絕對的,使用Werkzeug's url_parse() 避免絕對路徑的URL是netloc攻擊
            next_page = url_for('index')
        return redirect(next_page)
    return render_template('login.html', title='Sign In', form=form)

@app.route('/logout')   #新增登出
def logout():
    logout_user()
    return redirect(url_for('index'))

@app.route('/register', methods=['GET', 'POST'])    #使用者註冊
def register():
    if current_user.is_authenticated:
        return redirect(url_for('index'))
    form = RegistrationForm()
    if form.validate_on_submit():
        user = User(username=form.username.data, email=form.email.data)
        user.set_password(form.password.data)
        db.session.add(user)
        db.session.commit()
        flash('Congratulations, you are now a registered user!')
        return redirect(url_for('login'))
    return render_template('register.html', title='Register', form=form)
```

myApp/templates/base.html:  #新增登出入按鈕

```
    <div>
        yuBlog:
        <a href="{{ url_for('index') }}">Home</a>
        {% if current_user.is_anonymous %}  <!--當用戶未登入時.is_anonymous 為 True-->
        <a href="{{ url_for('login') }}">Login</a>
        {% else %}
        <a href="{{ url_for('logout') }}">Logout</a>
        {% endif %}
    </div>
```
myApp/templates/index.html:   #更改首頁界面
```
{% extends "base.html" %}

{% block content %}
    <h1>Hi, {{ current_user.username }}!</h1>
    {% for post in posts %}
    <div><p>{{ post.author.username }} says: <b>{{ post.body }}</b></p></div>
    {% endfor %}
{% endblock %}
```
myApp/forms.py:     #新增註冊表單
```
from flask_wtf import FlaskForm
from wtforms import StringField, PasswordField, BooleanField, SubmitField
from wtforms.validators import ValidationError, DataRequired, Email, EqualTo    #文字篩選器
from myApp.models import User

# ...

class RegistrationForm(FlaskForm):     #設定 註冊 框架
    username = StringField('Username', validators=[DataRequired()])
    email = StringField('Email', validators=[DataRequired(), Email()])   #增加email驗證器
    password = PasswordField('Password', validators=[DataRequired()])
    password2 = PasswordField(
        'Repeat Password', validators=[DataRequired(), EqualTo('password')])  #比對字串驗證器
    submit = SubmitField('Register')

    def validate_username(self, username):
        user = User.query.filter_by(username=username.data).first()
        if user is not None:
            raise ValidationError('Please use a different username.')

    def validate_email(self, email):
        user = User.query.filter_by(email=email.data).first()
        if user is not None:
            raise ValidationError('Please use a different email address.')
```

myApp/templates/register.html:    #新增註冊樣板

```
{% extends "base.html" %}

{% block content %}
    <h1>Register</h1>
    <form action="" method="post">
        {{ form.hidden_tag() }}
        <p>
            {{ form.username.label }}<br>
            {{ form.username(size=32) }}<br>
            {% for error in form.username.errors %}
            <span style="color: red;">[{{ error }}]</span>
            {% endfor %}
        </p>
        <p>
            {{ form.email.label }}<br>
            {{ form.email(size=64) }}<br>
            {% for error in form.email.errors %}
            <span style="color: red;">[{{ error }}]</span>
            {% endfor %}
        </p>
        <p>from app.forms import RegistrationForm
            {{ form.password.label }}<br>
            {{ form.password(size=32) }}<br>
            {% for error in form.password.errors %}
            <span style="color: red;">[{{ error }}]</span>
            {% endfor %}
        </p>
        <p>
            {{ form.password2.label }}<br>
            {{ form.password2(size=32) }}<br>
            {% for error in form.password2.errors %}
            <span style="color: red;">[{{ error }}]</span>
            {% endfor %}
        </p>
        <p>{{ form.submit() }}</p>
    </form>
{% endblock %}
```

myApp/templates/login.html:    #登入畫面增加註冊連結

```
    <p>New User? <a href="{{ url_for('register') }}">Click to Register!</a></p>  <!--註冊連結 -->
```
