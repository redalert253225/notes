template method pattern

RESTful
```
利用   HTTP protocol
設計程式
/GET /user/1
 /POST /user
 /PUT /user/1
 /DELETE /user/1

 @app.route('/user', methods=['GET'])
 def user():
    #....

 @app.route('/user', methods=['POST'])
 def add_user():
    #....

可以直接利用  傳輸協定 判斷所要執行的動作
```

高聚合
    單一類別不可在拆分

低耦合
    改變一個類別 不影響其他類

```

```
