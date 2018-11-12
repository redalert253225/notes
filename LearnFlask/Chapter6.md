個人頁面
--

* 追加資料庫 及新增使用者圖片

myApp/models.py:     

```
from hashlib import md5
# ...

class User(UserMixin, db.Model):
    # ...
    about_me = db.Column(db.String(140))                                  
    last_seen = db.Column(db.DateTime, default=datetime.utcnow)
    # ...
    def avatar(self, size):        #從gravatar server 藉由使用者email取得 個人化圖片
        digest = md5(self.email.lower().encode('utf-8')).hexdigest()
        return 'https://www.gravatar.com/avatar/{}?d=identicon&s={}'.format(
            digest, size)
```

* 設定表單

myApp/forms.py:     #新增關於我的表單

```
from wtforms import StringField, TextAreaField, SubmitField
from wtforms.validators import DataRequired, Length

# ...

class EditProfileForm(FlaskForm):   #新增關於我的表單
    username = StringField('Username', validators=[DataRequired()])
    about_me = TextAreaField('About me', validators=[Length(min=0, max=140)])
    submit = SubmitField('Submit')
```

* 設定樣板

myApp/templates/user.html:       #個人頁面 template
```
{% extends "base.html" %}

{% block content %}
    <table>
        <tr valign="top">
            <td><img src="{{ user.avatar(128) }}"></td>
            <td>
                <h1>User: {{ user.username }}</h1>
                {% if user.about_me %}<p>{{ user.about_me }}</p>{% endif %}
                {% if user.last_seen %}<p>Last seen on: {{ user.last_seen }}</p>{% endif %}
                {% if user == current_user %}               <!-- 修改 關於我  超連結-->
                <p><a href="{{ url_for('edit_profile') }}">Edit your profile</a></p>
                {% endif %}               
            </td>
        </tr>    
    </table>
    <hr>
    {% for post in posts %}
        {% include '_post.html' %}    <!-- 載入其他版模 -->
    {% endfor %}
{% endblock %}
```
myApp/templates/base.html:   #新增移動到個人頁面的選項
```
    <div>
      yuBlog:
      <a href="{{ url_for('index') }}">Home</a>
      {% if current_user.is_anonymous %}
      <a href="{{ url_for('login') }}">Login</a>
      {% else %}
      <a href="{{ url_for('user', username=current_user.username) }}">Profile</a>  <!--個人頁面超連結-->
      <a href="{{ url_for('logout') }}">Logout</a>
      {% endif %}
    </div>
```    

myApp/templates/\_post.html:   #Post sub-template

```
<table>
    <tr valign="top">
        <td><img src="{{ post.author.avatar(36) }}"></td>
        <td>{{ post.author.username }} says:<br>{{ post.body }}</td>
    </tr>
</table>  
```

myApp/templates/edit_profile.html:     #增加使用者頁面

```
{% extends "base.html" %}

{% block content %}
    <h1>Edit Profile</h1>
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
            {{ form.about_me.label }}<br>
            {{ form.about_me(cols=50, rows=4) }}<br>
            {% for error in form.about_me.errors %}
            <span style="color: red;">[{{ error }}]</span>
            {% endfor %}
        </p>
        <p>{{ form.submit() }}</p>
    </form>
{% endblock %}
```

* 設定route

myApp/routes.py:   
```
from datetime import datetime
from myApp.forms import EditProfileForm

# ...

@app.route('/user/<username>')      #個人頁面
@login_required
def user(username):
    user = User.query.filter_by(username=username).first_or_404()   #未找到用戶觸發404 exception
    posts = [
        {'author': user, 'body': 'Test post #1'},
        {'author': user, 'body': 'Test post #2'}
    ]
    return render_template('user.html', user=user, posts=posts)

@app.before_request          #時間
def before_request():
    if current_user.is_authenticated:
        current_user.last_seen = datetime.utcnow()
        db.session.commit()    

@app.route('/edit_profile', methods=['GET', 'POST'])  #關於我
@login_required
def edit_profile():
    form = EditProfileForm()
    if form.validate_on_submit():
        current_user.username = form.username.data
        current_user.about_me = form.about_me.data
        db.session.commit()
        flash('Your changes have been saved.')
        return redirect(url_for('edit_profile'))
    elif request.method == 'GET':
        form.username.data = current_user.username
        form.about_me.data = current_user.about_me
    return render_template('edit_profile.html', title='Edit Profile', form=form)
```

* 有新增資料庫欄位需要更新資料庫

(venv) $ flask db migrate -m "new fields in user model"

(venv) $ flask db upgrade
