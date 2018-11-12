啟動 mongodb

$ sudo systemctl start mongodb.service

確認 mongodb 狀態

$ sudo systemctl status mongodb.service

$ mongo   #進入mongo shell

$ show dbs  #查看有哪些DB

$ use DB_name     #進入DB

$ show collections   #查看有哪些collections

$ db.Collections_name.find()  #查詢目前進入的DB下,該collections所有內容

$ db.Collections_name.find().sort({ $natural:-1 }).limit( 1)  
 #查詢目前進入的DB下,該collections最新的一筆    

$ db.Collections_name.find().count()  #查詢目前進入的DB下,該collection總數量

$ db.Collections_name.drop()  #刪除該collection

$ db.Collections_name.isCapped()  #檢查該collection是否為 capped collection

$ db.Collections_name.revome({"\_id":ObjectID("XXXXXX")}) #刪除

$ db.Collections_name.revome({"\_id":ObjectID("XXXXXX")} ,{$set:{"disType.0.chungyo":"1"}})    
 #更改disType=[{'chungyo': ''}, {'store': ''}]

刪除DB:    
$use DB_name    
$db.dropDatabase()    
