ch1
where start
app = Flask(__name__)           #??

ch2
SECRET_KEY = os.environ.get('SECRET_KEY') or 'you-will-never-guess'

ch3
return redirect(url_for('index'))        

url_for函數就可以幫我們實現這個功能。url_for函數接收兩個及以上的參數，他接收函數名作為第一個參數，接收對應URL規則的命名參數，如果還出現其他的參數，則會添加到URL的後面作為查詢參數。

原文網址：https://read01.com/BO0Kdz.html

ch4  
database migration engine ??
id = db.Column(db.Integer, primary_key=True)   primary_key ??
posts = db.relationship('Post', backref='author', lazy='dynamic')   lazy??

ch8
followed = db.relationship(
    'User', secondary=followers,              #引用followers關聯表
    primaryjoin=(followers.c.follower_id == id),            #引用followers關聯表的follower_id
    secondaryjoin=(followers.c.followed_id == id),                
    backref=db.backref('followers', lazy='dynamic'), lazy='dynamic')  

    def followed_posts(self):
        followed = Post.query.join(                                            #查詢追隨者的Post
            followers, (followers.c.followed_id == Post.user_id)).filter(
                followers.c.follower_id == self.id)
        own = Post.query.filter_by(user_id=self.id)          #查詢自己的Post
        return followed.union(own).order_by(Post.timestamp.desc())      #union聯集

ch9
        return redirect(url_for('index'))   
        #It avoids inserting duplicate posts when a user inadvertently refreshes the page after submitting a web form. ??

         * url_for() 如果參數名稱沒有在URL中引用，那Flask會將它包在URL中作為查詢參數
ch10
<a href="{{ url_for('reset_password', token=token, _external=True) }}">
<!--_external=True 代表用絕對路徑 -->
