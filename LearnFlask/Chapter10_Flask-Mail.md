利用mail認證重製密碼
--

* 安裝 flask-mail 及 pyjwt

(venv) $ pip install flask-mail

(venv) $ pip install pyjwt     #這是一個 JSON Web Tokens

config.py:
```
class Config(object):
    # ...
    MAIL_SERVER = os.environ.get('MAIL_SERVER')
    MAIL_PORT = int(os.environ.get('MAIL_PORT') or 25)
    MAIL_USE_TLS = os.environ.get('MAIL_USE_TLS') is not None
    MAIL_USERNAME = os.environ.get('MAIL_USERNAME')
    MAIL_PASSWORD = os.environ.get('MAIL_PASSWORD')
    ADMINS = ['your-email@example.com']
```

myApp/\__init__.py:
```
# ...
from flask_mail import Mail

app = Flask(__name__)
# ...
mail = Mail(app)
```


* 設定框架

myApp/forms.py:
```
class ResetPasswordRequestForm(FlaskForm):   #驗證信箱框架
    email = StringField('Email', validators=[DataRequired(), Email()])
    submit = SubmitField('Request Password Reset')

class ResetPasswordForm(FlaskForm):     #重設密碼框架
    password = PasswordField('Password', validators=[DataRequired()])
    password2 = PasswordField(
        'Repeat Password', validators=[DataRequired(), EqualTo('password')])
    submit = SubmitField('Request Password Reset')
```

* 設定樣板

myApp/templates/login.html:    #登入頁面加入超連結
```
    <p>
        Forgot Your Password?
        <a href="{{ url_for('reset_password_request') }}">Click to Reset It</a>
    </p>
```

myApp/templates/reset_password_request.html:  寄認證mail的樣板
```
{% extends "base.html" %}

{% block content %}
    <h1>Reset Password</h1>
    <form action="" method="post">
        {{ form.hidden_tag() }}
        <p>
            {{ form.email.label }}<br>
            {{ form.email(size=64) }}<br>
            {% for error in form.email.errors %}
            <span style="color: red;">[{{ error }}]</span>
            {% endfor %}
        </p>
        <p>{{ form.submit() }}</p>
    </form>
{% endblock %}
```

myApp/templates/reset_password.html:    #重設密碼的頁面
```
{% extends "base.html" %}

{% block content %}
    <h1>Reset Your Password</h1>
    <form action="" method="post">
        {{ form.hidden_tag() }}
        <p>
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

* mail 內文樣式

myApp/templates/email/reset_password.html:    #mail的內文
```
<p>Dear {{ user.username }},</p>
<p>
    To reset your password
    <a href="{{ url_for('reset_password', token=token, _external=True) }}">
        click here  <!--_external=True 代表用絕對路徑 -->
    </a>.
</p>
<p>Alternatively, you can paste the following link in your browser's address bar:</p>
<p>{{ url_for('reset_password', token=token, _external=True) }}</p>
<p>If you have not requested a password reset simply ignore this message.</p>
<p>Sincerely,</p>
<p>The blog Team</p>
```

* 設定routes

myApp/routes.py:
```
from myApp.forms import ResetPasswordRequestForm
from myApp.email import send_password_reset_email
from myApp.forms import ResetPasswordForm


@app.route('/reset_password_request', methods=['GET', 'POST'])  #寄mail的頁面
def reset_password_request():
    if current_user.is_authenticated:   #登入狀態不需要進入此頁面
        return redirect(url_for('index'))
    form = ResetPasswordRequestForm()
    if form.validate_on_submit():
        user = User.query.filter_by(email=form.email.data).first()    #尋找此mail的user
        if user:
            send_password_reset_email(user)  #呼叫寄mail的程式
        flash('Check your email for the instructions to reset your password')  
        #在if外 , 不管有沒有找到都會顯示 , 避免被利用確認是否為成員
        return redirect(url_for('login'))
    return render_template('reset_password_request.html',
                           title='Reset Password', form=form)

@app.route('/reset_password/<token>', methods=['GET', 'POST']) #重設密碼的頁面
def reset_password(token):
    if current_user.is_authenticated:
        return redirect(url_for('index'))
    user = User.verify_reset_password_token(token)  #檢查token
    if not user:
        return redirect(url_for('index'))
    form = ResetPasswordForm()
    if form.validate_on_submit():
        user.set_password(form.password.data)
        db.session.commit()
        flash('Your password has been reset.')
        return redirect(url_for('login'))
    return render_template('reset_password.html', form=form)

```

* 生成 驗證 token
```
jwt.encode(  
  {'reset_password': self.id, 'exp': time() + expires_in},    #有效負載
   app.config['SECRET_KEY'],     #密匙
   algorithm='HS256'   #使用的演算法
   )     
```

myApp/models.py:
```
from time import time
import jwt
from myApp import app

class User(UserMixin, db.Model):
    # ...

    def get_reset_password_token(self, expires_in=600): #生成token
        return jwt.encode(
            {'reset_password': self.id, 'exp': time() + expires_in},
            app.config['SECRET_KEY'], algorithm='HS256').decode('utf-8')

    @staticmethod
    def verify_reset_password_token(token):   #認證token
        try:
            id = jwt.decode(token, app.config['SECRET_KEY'],    
                            algorithms=['HS256'])['reset_password']    #jwt.decode() 驗證token  返回一個字典 , 內含有當初設定的'reset_password': self.id
           # back = jwt.decode(token, app.config['SECRET_KEY'],   algorithms=['HS256'])      
           # id = back['reset_password']
        except:
            return
        return User.query.get(id)
```

* 設定寄 email.py

myApp/email.py:
```
from threading import Thread
from flask_mail import Message
from myApp import mail
from flask import render_template
from myApp import app

# ...

def send_async_email(app, msg):
    with app.app_context():
        mail.send(msg)


def send_email(subject, sender, recipients, text_body, html_body): #負責發送電子郵件
    msg = Message(subject, sender=sender, recipients=recipients)
    msg.body = text_body
    msg.html = html_body
    Thread(target = send_async_email, args=(app, msg)).start() #另開執行序發送電子郵件

def send_password_reset_email(user):
    token = user.get_reset_password_token()
    send_email('[Microblog] Reset Your Password',
               sender=app.config['ADMINS'][0],
               recipients=[user.email],
               text_body=render_template('email/reset_password.txt',
                                         user=user, token=token),
               html_body=render_template('email/reset_password.html',
                                         user=user, token=token))

```

設定本機 mail server

(venv) $ python -m smtpd -n -c DebuggingServer localhost:8025

(venv) $ export MAIL_SERVER=localhost

(venv) $ export MAIL_PORT=8025
