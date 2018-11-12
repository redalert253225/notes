* 安裝 flask-bootstrap

(venv) $ pip install  flask-bootstrap

* 初始化  flask-bootstrap

myApp/__init__.py: Flask-Bootstrap实例。
```
# ...
from flask_bootstrap import Bootstrap

app = Flask(__name__)
# ...
bootstrap = Bootstrap(app)
```

* 修改 templates 套用 bootstrap

myApp/templates/base.html：重新设计后的基础模板。
```
{% extends 'bootstrap/base.html' %}  {# 用 bootstrap/base.html 包住本來的網頁#}

{% block title %}    {# bootstrap  title 模塊 #}
    {% if title %}{{ title }} - YuoBlog{% else %}Welcome to YuBlog{% endif %}
{% endblock %}

{% block navbar %}  {# bootstrap 導航欄樣式 #}
    <nav class="navbar navbar-default">
       <div class="container">
           <div class="navbar-header">
               <button type="button" class="navbar-toggle collapsed" data-toggle="collapse" data-target="#bs-example-navbar-collapse-1" aria-expanded="false">
                   <span class="sr-only">Toggle navigation</span>
                   <span class="icon-bar"></span>
                   <span class="icon-bar"></span>
                   <span class="icon-bar"></span>
               </button>
               <a class="navbar-brand" href="{{ url_for('index') }}">Microblog</a>
           </div>
           <div class="collapse navbar-collapse" id="bs-example-navbar-collapse-1">
               <ul class="nav navbar-nav">
                   <li><a href="{{ url_for('index') }}">Home</a></li>
                   <li><a href="{{ url_for('explore') }}">Explore</a></li>
               </ul>
               <ul class="nav navbar-nav navbar-right">
                   {% if current_user.is_anonymous %}
                   <li><a href="{{ url_for('login') }}">Login</a></li>
                   {% else %}
                   <li><a href="{{ url_for('user', username=current_user.username) }}">Profile</a></li>
                   <li><a href="{{ url_for('logout') }}">Logout</a></li>
                   {% endif %}
               </ul>
           </div>
       </div>
    </nav>
{% endblock %}

{% block content %}   {# bootstrap content  模塊 #}
    <div class="container">
        {% with messages = get_flashed_messages() %}
        {% if messages %}
            {% for message in messages %}
            <div class="alert alert-info" role="alert">{{ message }}</div>
            {% endfor %}
        {% endif %}
        {% endwith %}

        {# application content needs to be provided in the app_content block #}
        {% block app_content %}{% endblock %}     {# 定義模塊 app_content   其他樣板從此插入  所以模塊名都要改成app_content#}
    </div>
{% endblock %}
```

myApp/templates/404.html：重新设计后的404错误模板。
```
{% extends "base.html" %}

{% block app_content %}  {#  app_content模塊 }
    <h1>File Not Found</h1>
    <p><a href="{{ url_for('index') }}">Back</a></p>
{% endblock %}
```

myApp/templates/\_post.html: 重新设计后的用户动态子模板。
```
    <table class="table table-hover">
        <tr>
            <td width="70px">
                <a href="{{ url_for('user', username=post.author.username) }}">
                    <img src="{{ post.author.avatar(70) }}" />
                </a>
            </td>
            <td>
                <a href="{{ url_for('user', username=post.author.username) }}">
                    {{ post.author.username }}
                </a>
                says:
                <br>
                {{ post.body }}
            </td>
        </tr>
    </table>
```

myApp/templates/index.html: 重新设计后的分页链接。
```
    ...
    <nav aria-label="...">
        <ul class="pager">
            <li class="previous{% if not prev_url %} disabled{% endif %}">
                <a href="{{ prev_url or '#' }}">
                    <span aria-hidden="true">&larr;</span> Newer posts
                </a>
            </li>
            <li class="next{% if not next_url %} disabled{% endif %}">
                <a href="{{ next_url or '#' }}">
                    Older posts <span aria-hidden="true">&rarr;</span>
                </a>
            </li>
        </ul>
    </nav>
```
