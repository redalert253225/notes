Follow
--

* 設置一對多 追隨者列表

myApp/models.py:
```
# ...

followers = db.Table('followers',                      #為了多對多關係創建關聯表
    db.Column('follower_id', db.Integer, db.ForeignKey('user.id')),
    db.Column('followed_id', db.Integer, db.ForeignKey('user.id'))
)
    # ...
class User(UserMixin, db.Model):
    # ...
    followed = db.relationship(                    #創建與followers的關係
        'User', secondary=followers,              #引用followers關聯表
        primaryjoin=(followers.c.follower_id == id),            #引用followers關聯表的follower_id
        secondaryjoin=(followers.c.followed_id == id),                
        backref=db.backref('followers', lazy='dynamic'), lazy='dynamic')         #lazy='dynamic' 設置為 在特定查詢前 不運行
    # ...
    def follow(self, user):            #追隨
        if not self.is_following(user):
            self.followed.append(user)         #.append   relationship內建方法

    def unfollow(self, user):          #退追隨
        if self.is_following(user):
            self.followed.remove(user)         #.remove   relationship內建方法

    def is_following(self, user):              #檢查是否追隨
        return self.followed.filter(
            followers.c.followed_id == user.id).count() > 0    #查詢關聯表的follower_id

    def followed_posts(self):                  #顯示追隨的所有文章
        followed = Post.query.join(             #合併Post table, 條件:與followers裡  followed_id == Post.user_id    Post.user_id為所連結Use的id
            followers, (followers.c.followed_id == Post.user_id)).filter(       
                followers.c.follower_id == self.id)                    #再用.filter修飾   follower_id == self.id
        own = Post.query.filter_by(user_id=self.id)          #查詢自己的Post
        return followed.union(own).order_by(Post.timestamp.desc())      #union聯集
```

* 有新增資料庫  要更新遷移資料庫

(venv) $ flask db migrate -m "followers"

(venv) $ flask db upgrade

* 設定route

myApp/routes.py: Follow and unfollow routes.
```{% if user == current_user %}               <!-- 修改 關於我  超連結-->
@app.route('/follow/<username>')       #追隨
@login_required
def follow(username):
    user = User.query.filter_by(username=username).first()
    if user is None:
        flash('User {} not found.'.format(username))
        return redirect(url_for('index'))
    if user == current_user:
        flash('You cannot follow yourself!')
        return redirect(url_for('user', username=username))
    current_user.follow(user)
    db.session.commit()
    flash('You are following {}!'.format(username))
    return redirect(url_for('user', username=username))

@app.route('/unfollow/<username>')         #退追隨頁面
@login_required
def unfollow(username):
    user = User.query.filter_by(username=username).first()
    if user is None:
        flash('User {} n樣板ot found.'.format(username))
        return redirect(url_for('index'))
    if user == current_user:
        flash('You can樣板not unfollow yourself!')
        return redirect(url_for('user', username=username))
    current_user.unfollow(user)
    db.session.commit()
    flash('You are not following {}.'.format(username))
    return redirect(url_for('user', username=username))
```

* 設定樣板

myApp/templates/user.html: Follow and unfollow links in user profile page.
```
            ...
            <h1>User: {{ user.username }}</h1>
            {% if user.about_me %}<p>{{ user.about_me }}</p>{% endif %}
            {% if user.last_seen %}<p>Last seen on: {{ user.last_seen }}</p>{% endif %}

            <p>{{ user.followers.count() }} followers, {{ user.followed.count() }} following.</p>

            {% if user == current_user %}              <!--如果瀏覽的是自己的頁面-->
            <p><a href="{{ url_for('edit_profile') }}">Edit your profile</a></p>

            {% elif not current_user.is_following(user) %}            <!--如果瀏覽的不是自己追隨的頁面-->
            <p><a href="{{ url_for('follow', username=user.username) }}">Follow</a></p>
            {% else %}                                <!--如果瀏覽的是自己追隨的頁面-->
            <p><a href="{{ url_for('unfollow', username=user.username) }}">Unfollow</a></p>
            {% endif %}
            ...
```
