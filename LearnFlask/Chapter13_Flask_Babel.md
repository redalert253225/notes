(venv) $ pip install flask-babel


myApp/__init__.py: Flask-Babel实例。
```
# ...
from flask_babel import Babel

app = Flask(__name__)
# ...
babel = Babel(app)
```
config.py：支持的语言列表。
```
class Config(object):
    # ...
    LANGUAGES = ['en', 'es']
```

myApp/__init__.py：选择最匹配的语言。
```
from flask import request

# ...

@babel.localeselector
def get_locale():
    return request.accept_languages.best_match(app.config['LANGUAGES'])
```
Flask中request对象的属性accept_languages。 request对象提供了一个高级接口，用于处理客户端发送的带Accept-Language头部的请求。 该头部指定了客户端语言和区域设置首选项。 该头部的内容可以在浏览器的首选项页面中配置，默认情况下通常从计算机操作系统的语言设置中导入

py
```
from flask_babel import _
# ...
flash(_('Your post is now live!'))

flash('User {} not found.'.format(username))

flash(_('User %(username)s not found.', username=username))
```
```
from flask_babel import lazy_gettext as _l

class LoginForm(FlaskForm):
    username = StringField(_l('Username'), validators=[DataRequired()])
    # ...


login = LoginManager(app)
login.login_view = 'login'
login.login_message = _l('Please log in to access this page.')
```
templates
```
<h1>File Not Found</h1>
启用翻译之后的版本是：

<h1>{{ _('File Not Found') }}</h1>


<h1>{{ _('Hi, %(username)s!', username=current_user.username) }}</h1>
```

\_post.html中的一个特别棘手的案例让我花了一些时间才理顺：
```
        {% set user_link %}
            <a href="{{ url_for('user', username=post.author.username) }}">
                {{ post.author.username }}
            </a>
        {% endset %}
        {{ _('%(username)s said %(when)s',
            username=user_link, when=moment(post.timestamp).fromNow()) }}

```
这里的问题是我希望username是一个超链接，指向用户的个人主页，而不仅仅是名字，
所以我必须使用set和endset模板指令创建一个名为user_link的中间变量 ，然后将其作为参数传递给翻译函数。

babel.cfg：PyBabel配置文件。
```
[python: app/**.py]
[jinja2: app/templates/**.html]
extensions=jinja2.ext.autoescape,jinja2.ext.with_
```
(venv) $ pybabel extract -F babel.cfg -k \_l -o messages.pot .

(venv) $ pybabel init -i messages.pot -d app/translations -l es

```
#: app/email.py:21
msgid "[Microblog] Reset Your Password"
msgstr ""
```
(venv) $ pybabel compile -d app/translations

app/__init__.py：选择最佳语言。
```
@babel.localeselector
def get_locale():
    # return request.accept_languages.best_match(app.config['LANGUAGES'])
    return 'es'
```

更新
(venv) $ pybabel extract -f babel.cfg -k \_l -o messages.pot .

(venv) $ pybabel update -i messages.pot -d app/translations

(venv) $ pybabel compile -d app/translations



時間
app/routes.py：存储选择的语言到flask.g中。
```
# ...
from flask import g
from flask_babel import get_locale

# ...

@app.before_request
def before_request():
    # ...
    g.locale = str(get_locale())
```

變更命令
app/cli.py：翻译命令组
```
from app import app

@app.cli.group()
def translate():
    """Translation and localization commands."""
    pass

import os


@translate.command()
def update():
    """Update all languages."""
    if os.system('pybabel extract -F babel.cfg -k _l -o messages.pot .'):
        raise RuntimeError('extract command failed')
    if os.system('pybabel update -i messages.pot -d app/translations'):
        raise RuntimeError('update command failed')
    os.remove('messages.pot')

@translate.command()
def compile():
    """Compile all languages."""
    if os.system('pybabel compile -d app/translations'):
        raise RuntimeError('compile command failed')

```

app/cli.py：Init子命令。
```
import click

@translate.command()
@click.argument('lang')
def init(lang):
    """Initialize a new language."""
    if os.system('pybabel extract -F babel.cfg -k _l -o messages.pot .'):
        raise RuntimeError('extract command failed')
    if os.system(
            'pybabel init -i messages.pot -d app/translations -l ' + lang):
        raise RuntimeError('init command failed')
    os.remove('messages.pot')
```
microblog.py：注册命令。
```
from app import cli
```
