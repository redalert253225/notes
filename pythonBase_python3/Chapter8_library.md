import time
--
```
import time

time.sleep(3)   # wait 3 seconds
```
import random
--
```
import random

x=random.randint(1, 5)  #  x is integer ,1<=x<=5
print(x)

>>> 5

x= random.random() #  0<=x<1.0
print(x)

>>> 0.41406773531282004

items = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
random.shuffle(items)   #打亂序列
print(items)

>>>[3, 5, 8, 7, 1, 6, 10, 9, 4, 2]

x = random.sample(items,  1)  #例表中機選N項
print(x)

>>>[2]

myrandom=random.Random() #獨立的生成器
```

String
--
change to ASCII
```
>>> ord('a')
97  
>>> chr(97)
'a'
```
用'0'補滿位數
```
>>> str(3).zfill(3)
'003'
```
split 用特定字元分割字串  返回字串陣列
```
>>> part = 'add,bee,cf'
>>> part.split(',')
['add', 'bee', 'cf']
```
rfind 從字串最後開始找 返回第一個找到指定字元的位置 沒有返回 -1   
find   從字串前面
```
>>> name = 'Mrs.Li'
>>> name.rfind('.')
3
```
zfill 將字串前面補零到指定位數
```
>>> str='2'
>>> str.zfill(4)
'0002'
```
