
就讀取而言    
client 定義的 model  class  只是容器    
 當從 monge 抓取資料後 會塞到對應名稱的變數下    
 而變數會根據你的定義顯示該數值   
```
 class YuTsaiLprCashJournal(Document):
    plateText = BooleanField()
    #....
plateText  True
```
```
class YuTsaiLprCashJournal(Document):
    plateText = StringField( )
    #....
plateText  M79162
```
如果沒有定義相同的名稱會錯報    
但是多定義不會錯報    
類似從mongo取資料  放到你定義的CLASS 初始化     
多定義等於宣告沒給值  少定義是多給值  
```
class YuTsaiLprCashJournal(Document):
    #plateText = StringField( )
    #....
mongoengine.errors.FieldDoesNotExist: The fields "{'plateText'}" do not exist on the document "YuTsaiLprCashJournal"
```
在mongo資料串為  "disType" : [{"chungyo": "1"} , {"store" : ''"}]    


data.objects( value1 = "any" ).first()    #查詢指定資料的第一筆 ,沒有返回None    
data.save()  #上傳文件

```
class Student(DynamicDocument):
    meta = {
        'collection': 'student', #定義指定的collection名稱 或要使用的collection
        "max_size" : 40000000  #指定size 就會成為 capped collection
    }
```
Was unable to import app Error: ValidationError (YuTsaiLprCashJournal:None) (StringField only accepts string values: ['disType'])
Was unable to import app Error: Could not save document (Document failed validation)
