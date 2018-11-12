創建首頁
--

(venv) $ mkdir myApp/templates      #創建樣板資料夾

myApp/templates/base.html: #建立公共樣板 之後都可以省去重複的程式片段
```
<html>
    <head>
      {% if title %}
      <title>{{ title }} - yuBlog</title>
      {% else %}
      <title>Welcome to yuBlog</title>
      {% endif %}
    </head>
    <body>
        <div>yuBlog: <a href="/index">Home</a></div>
        <hr>
        {% block content %}{% endblock %}       <!--其他樣板會由此處插入-->
    </body>
</html>
```
myApp/templates/index.html: #建立index網頁樣板
```
{% extends "base.html" %}      <!--宣告使用某一樣板-->

{% block content %}

    <h1>Hi, {{ user.username }}!</h1>
    {% for post in posts %}
    <div><p>{{ post.author.username }} says: <b>{{ post.body }}</b></p></div>
    {% endfor %}

{% endblock %}
```

myApp/routes.py:
```
from flask import render_template   
from myApp import app

@app.route('/')
@app.route('/index')
def index():
    user = {'username': 'Miguel'}
    posts = [
        {
            'author': {'username': 'John'},
            'body': 'Beautiful day in Portland!'
        },
        {
            'author': {'username': 'Susan'},
            'body': 'The Avengers movie was so cool!'
        }
    ]
    return render_template('index.html', title='Home', user=user, posts=posts)
    #render_template 將程式指定到 template/index.html 並帶入參數title,user,posts,  到樣板上{{   }}

```

---
分離程式更方便維護

以上等同

myApp/routes.py: #?? 真的能跑?
```
from myApp import app

@app.route('/')
@app.route('/index')
def index():
    user = {'username': 'Miguel'}
    posts = [
        {
            'author': {'username': 'John'},
            'body': 'Beautiful day in Portland!'
        },
        {
            'author': {'username': 'Susan'},
            'body': 'The Avengers movie was so cool!'
        }
    ]
    return title='Home'
    <html>
        <head>
          {% if title %}
          <title>{{ title }} - yuBlog</title>
          {% else %}
          <title>Welcome to yuBlog</title>
          {% endif %}
        </head>
        <body>
            <div>yuBlog: <a href="/index">Home</a></div>
            <hr>
            <h1>Hi, {{ user.username }}!</h1>
            {% for post in posts %}
            <div><p>{{ post.author.username }} says: <b>{{ post.body }}</b></p></div>
            {% endfor %}
        </body>
    </html>
```
