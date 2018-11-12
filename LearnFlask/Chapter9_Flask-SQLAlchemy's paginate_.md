Flask-SQLAlchemy's paginate
--

.paginate( int, int , boolean)

第一個參數 : 設定共幾頁,默認值為1  
第二個參數 : 每頁包還幾項  
第三個參數 :  
True : 超出範圍返回 404 錯誤  
False : 超出範圍以空列表顯示

Paginate 方法 :  
has_next: True if there is at least one more page after the current one  
has_prev: True if there is at least one more page before the current one  
next_num: page number for the next page  
prev_num: page number for the previous page  

* 設定一個分頁的項目數量

config.py:
```
class Config(object):
    # ...
    POSTS_PER_PAGE = 3  #設定每頁的項目數量
```
* 增加新的form

myApp/forms.py:  
```
class PostForm(FlaskForm):
    post = TextAreaField('Say something', validators=[
        DataRequired(), Length(min=1, max=140)])
    submit = SubmitField('Submit')
```

* 設定templates

myApp/templates/base.html:
```
<body>
    <div>
        yuBlog:
        # ...
        <a href="{{ url_for('explore') }}">Explore</a>
        # ...
    </div>
</body>
```

myApp/templates/index.html:  
```
{% extends "base.html" %}

{% block content %}
    <h1>Hi, {{ current_user.username }}!</h1>
    {% if form %} <!--因為被兩個程式引用, 這裡判斷有定義才顯示-->
    <form action="" method="post">
        {{ form.hidden_tag() }}
        <p>
            {{ form.post.label }}<br>
            {{ form.post(cols=32, rows=4) }}<br>
            {% for error in form.post.errors %}
            <span style="color: red;">[{{ error }}]</span>
            {% endfor %}
        </p>
        <p>{{ form.submit() }}</p>
    </form>
    {% endif %}

    {% for post in posts %}  <!--秀出所有文章-->
        {% include '_post.html' %}     <!--使用樣板_post.html-->
    {% endfor %}

    {% if prev_url %}    <!--有上一頁或下一頁才秀出來-->
    <a href="{{ prev_url }}">Newer posts</a>
    {% endif %}
    {% if next_url %}
    <a href="{{ next_url }}">Older posts</a>
    {% endif %}

{% endblock %}
```

myApp/templates/\_post.html:
```
    <table>
        <tr valign="top">
            <td><img src="{{ post.author.avatar(36) }}"></td>
            <td>
                <a href="{{ url_for('user', username=post.author.username) }}">  <!--新增連結到作者的個人頁面超連結-->
                    {{ post.author.username }}
                </a>
                says:<br>{{ post.body }}
            </td>
        </tr>
    </table>
```

myApp/templates/user.html:
```
  <table>
    #...
    {% for post in posts %}
        {% include '_post.html' %}
    {% endfor %}
    {% if prev_url %}
    <a href="{{ prev_url }}">Newer posts</a>
    {% endif %}
    {% if next_url %}
    <a href="{{ next_url }}">Older posts</a>
    {% endif %}
    #...
  </table>
```

* 設定routes

myApp/routes.py:
```
from myApp.forms import PostForm
from myApp.models import Post

@app.route('/', methods=['GET', 'POST'])
@app.route('/index', methods=['GET', 'POST'])
@login_required
def index():
    form = PostForm()
    if form.validate_on_submit():
        post = Post(body=form.post.data, author=current_user)   
        db.session.add(post)
        db.session.commit()
        flash('Your post is now live!')
        return redirect(url_for('index'))   

    page = request.args.get('page', 1, type=int)   # request.args 用來查詢字串符中的參數
    posts = current_user.followed_posts().paginate(
        page, app.config['POSTS_PER_PAGE'], False)

    next_url = url_for('index', page=posts.next_num) \   
        if posts.has_next else None         #判斷有沒有下一頁
    prev_url = url_for('index', page=posts.prev_num) \
        if posts.has_prev else None        #判斷有沒有上一頁
    return render_template('index.html', title='Home', form=form,
                           posts=posts.items, next_url=next_url,
                           prev_url=prev_url)

    # ...

@app.route('/user/<username>')
@login_required
def user(username):
    user = User.query.filter_by(username=username).first_or_404()
    page = request.args.get('page', 1, type=int)
    posts = user.posts.order_by(Post.timestamp.desc()).paginate(
        page, app.config['POSTS_PER_PAGE'], False)      #order_by  設定排序方式
    next_url = url_for('user', username=user.username, page=posts.next_num) \
        if posts.has_next else None
    prev_url = url_for('user', username=user.username, page=posts.prev_num) \
        if posts.has_prev else None
    return render_template('user.html', user=user, posts=posts.items,
                           next_url=next_url, prev_url=prev_url)    

    # ...

@app.route('/explore')   #此頁面顯示所有用戶的文章
@login_required
def explore():
    page = request.args.get('page', 1, type=int)
    posts = Post.query.order_by(Post.timestamp.desc()).paginate(
        page, app.config['POSTS_PER_PAGE'], False)

    next_url = url_for('explore', page=posts.next_num) \
        if posts.has_next else None
    prev_url = url_for('explore', page=posts.prev_num) \
        if posts.has_prev else None
    return render_template("index.html", title='Explore', posts=posts.items,
                          next_url=next_url, prev_url=prev_url)      #這裡調用的板模是index.html因為需要的格式差不多
```

 * url_for() 如果參數名稱沒有在URL中引用，那Flask會將它包在URL中作為查詢參數
