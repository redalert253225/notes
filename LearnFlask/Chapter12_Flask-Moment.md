(venv) $ pip install flask-moment

JavaScript 的開源庫 Moment.js 的應用

myApp/__init__.py：Flask-Moment实例。
```
# ...
from flask_moment import Moment

app = Flask(__name__)
# ...
moment = Moment(app)
```


myApp/templates/base.html：在基础模板中包含moment.js
```
...
{% block scripts %}
    {{ super() }}
    {{ moment.include_moment() }}
{% endblock %}
```
Flask-Moment与moment.js一起工作，因此应用的所有模板都必须包含moment.js。为了确保该库始终可用，我将把它添加到基础模板中
scripts块是Flask-Bootstrap基础模板暴露的另一个块，这是JavaScript引入的地方。
追加moment.js库的话，就需要使用super()语句，才能继承基础模板中已有的内容，否则就是替换。

myApp/templates/\_post.html: 在用户动态子模板中渲染时间戳。
```
                <a href="{{ url_for('user', username=post.author.username) }}">
                    {{ post.author.username }}
                </a>
                said {{ moment(post.timestamp).fromNow() }}:
                <br>
                {{ post.body }}
```
