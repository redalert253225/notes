Babel 設定
--

* 安裝Babel
(myVenv) $ pip install Flask-Babel

* 新增環境參數

config.py
```
BABEL_DEFAULT_LOCALE = 'en'  #設定預設語言
```

*  flask_babel 初始化    

\__init__.py

```
from flask import Flask
from config import Config
from flask_babel import Babel , gettext    #babel

app = Flask(\__name__)           
app.config.from_object(Config)       #導入config.py 的設定值

babel = Babel(app)  #babel
```
(myVenv) $ mkdir babel

(myVenv) $ touch babel/babel.cfg

babel.cfg :
```
[python: **.py]
[jinja2: **/templates/**.html]
extensions=jinja2.ext.autoescape,jinja2.ext.with_
encoding = utf-8
```
標記所有要翻譯的字串
--

Babel 會翻譯內建的訊息, 可是自訂的參數要自己翻譯

### 一般   

html:    
before
```
<h1>Sign In</h1>
```
after
```
<h1>{{ gettext( "Sign In" ) }}</h1>
```

### class裡

form:    
before
```
class BabelTestForm(FlaskForm):  
    testname = StringField( 'language', validators=[DataRequired()] )

```
after
```
from flask_babel import  lazy_gettext     #lazy??

class BabelTestForm(FlaskForm):  
    testname = StringField(  lazy_gettext("language"), validators=[DataRequired()] )
```
### 字串內有變數
routes:     
before
```
#..
        raise Exception('Fail File!{} check it !'.format(name))
#..
```
after    #用'%'和 's' 包住變數
```
#..
        raise Exception(lazy_gettext('Fail File!%(name)s check it !',name=name))
#..
```
in messages.po    
```
msgid "Fail File!%(name)s check it !"
msgstr "錯誤的檔案 ！%(name)s 請確認 "
```

創建新的語言樣板
--

* 創建翻譯版模

一般 ( 沒使用 lazy_gettext )

(myVenv)$ pybabel extract -F ./babel/babel.cfg -o ./babel/messages.pot .      # 最後這個' . '很重要


如果有使用  lazy_gettext  標記

(myVenv) $ pybabel extract -F ./babel/babel.cfg -k lazy_gettext -o ./babel/messages.pot .


* 創建某語言的翻譯   #zh_Hant 是繁體中文 ,

(myVenv) [yu@archlinux myApp]$ pybabel init -i ./babel/messages.pot -d ./app/translations -l zh_Hant    

會生成 translations/zh_Hant/LC_MESSAGES/messages.po

 * 手動編輯你的翻譯

translations/zh_Hant/LC_MESSAGES/messages.po :
 ```
 #: forms.py:54
msgid "language"
msgstr "選擇語言"          #msgstr "  "  輸入自訂翻譯

#: forms.py:55
msgid "change"
msgstr "變更語言"

#: templates/login.html:4
msgid "Sign In"
msgstr "登入"
 ```

* 編譯  translations 裡的 messages.po


 $ pybabel compile -d translations

會生成  translations/zh_Hant/LC_MESSAGES/messages.mo,  若無那你需要將 message.po 中的#,fuzzy刪除

更新樣板
--

更新版模 -> update各種語言樣板  ->  檢查並翻譯 -> 編譯

更新版模 :

(myVenv)$ pybabel extract -F ./babel/babel.cfg -k lazy_gettext -o ./babel/messages.pot .    # 最後這個' . '很重要

更新所有語言的樣板 :     #update 已翻譯的不會被清空

(myVenv)$ pybabel update -i ./babel/messages.pot -d ./app/translations  

檢查所有translations底下的messages.pot , 手動翻譯
```
#: templates/login.html:4
msgid "Sign In"
msgstr ""
```
編譯 :

 $ pybabel compile -d ./app/translations

 最終資料夾:
 --
```
 app/
     templates
     translations/
        zh/
            LC_MESSAGES/
                messages.mo
                messages.po
babel/    
    babel.cfg   
    messages.pot    
```

在程式中更換翻譯版模
--
routes.py:
```
from flask_babel import refresh
#...

app.config['BABEL_DEFAULT_LOCALE'] ='zh_Hant'
refresh()

#...
```
