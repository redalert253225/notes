>class
--

in class, all the function has to  pass parameter  'self' , or you will get Error
```
TypeError: yuShow() takes 0 positional arguments but 1 was given
```
when use all the value you create in class, you need to use 'self.name', even you create  on outside of function

polymorphism :  just create more value and give it initial value 'None'

```
class YuClass():
    min=0
    def __init__(self, start,max,three=None):
        self.start=start
        self._max=max
        if three != None :
            self.min=three
    def __iter__(self):
        self.count=self.start
        return self
    def __next__(self):
        now=self.count
        if self.count>=  self._max:
            raise StopIteration
            return self.count
        self.count+=1
        return now
    def yuShow(self):
        print (self.min)

```
Inheritance

you can use  value and funtion of parentClassName  like yourself  
if you define a same  function or value of name , it wil be covered

```
class ClassName( parentClassName ) :   //Inheritance  parent Class

    def __init__( self,  val1, val2 ,val3, val4 ) :   
        super( ClassName ,self ).__init__(val3, val4 )  //initializer parentClassName  
```
also you can use "parentClassName.\__init__(val3, val4 )''  
but if in  Multiple threads , it is not good , because some parentClass may be called many times
```
class A:
    def __init__(self):
        print("Enter A")
        print("Leave A")

class B(A):
    def __init__(self):
        print("Enter B")
        A.__init__(self)
        print("Leave B")

class C(A):
    def __init__(self):
        print("Enter C")
        A.__init__(self)
        print("Leave C")

class D(A):
    def __init__(self):
        print("Enter D")
        A.__init__(self)
        print("Leave D")

class E(B, C, D):
    def __init__(self):
        print("Enter E")
        B.__init__(self)
        C.__init__(self)
        D.__init__(self)
        print("Leave E")

E()  

[yu@archlinux ~]$ python3 -m pythonLearn.test
Enter E
Enter B
Enter A
Leave A
Leave B
Enter C
Enter A
Leave A
Leave C
Enter D
Enter A
Leave A
Leave D
Leave E

```

about  'super' , it just only search the list of Method Resolution Order
```
def super( cls , inst ):
    mro = inst.__class__.mro()
    return mro[ mro.index( cls ) + 1 ]
```
use 'super' ,it can save resources  
and if you change parentClassName , it easy to maintain  
notice, in the 'super'  the 'self'  is the children class
```
class A:
    def __init__(self):
        print("Enter A")
        print("Leave A")

class B(A):
    def __init__(self):
        print("Enter B")
        print("now self = %s"%self)  #now the self is E
        super(B, self).__init__()
        print("Leave B")

class C(A):
    def __init__(self):
        print("Enter C")
        super(C, self).__init__()
        print("Leave C")

class D(A):
    def __init__(self):
        print("Enter D")
        super(D, self).__init__()
        print("Leave D")

class E(B, C, D):
    def __init__(self):
        print("Enter E")
        super(E, self).__init__()
        print("Leave E")

e=E()
print(e.__class__.__mro__)

[yu@archlinux ~]$ python3 -m pythonLearn.test
Enter E
Enter B
now self = <__main__.E object at 0x7f5cd609c668>  
Enter C
Enter D
Enter A
Leave A
Leave D
Leave C
Leave B
Leave E
(<class '__main__.E'>, <class '__main__.B'>, <class '__main__.C'>, <class '__main__.D'>, <class '__main__.A'>, <class 'object'>)
```


the value created in class, but not in functon is static value ,used by 'classs.name'
```
>>> from pythonLearn.myModule import YuClass
>>> test1=YuClass(1,10)
>>> test1.min+=10      
>>> test1.min    
10
>>> YuClass.min
0
>>> YuClass.min=2
>>> test2=YuClass(1,10)
>>> test2.min          
2
```
'\__name__'   is Magic methods

'\_name'   to tell others user it is private

on the outsid, use   .\_name   also change it , but don't do that     


Magic methods | mean |
--------------|:-----------------
\__lt__	             |        <
\__le__	            |       <=
\__eq__           | 	     ==
\__ne__	          |         !=
\__gt__	           |        >
\__ge__           | 	     >=
\__add__        | 	     +
\__iadd__	      |        +=
\__sub__	       |         –
\__isub__	       |        -=  
\__mul__	       |       *  
\__imul__	       |      *=  
\__truediv__	   |       /
\__itruediv__   | 	     /=  
\__floordiv__	  |	      //  
\__ifloordiv__ |	     //=  
\__mod__	      |	     %
\__imod__	      |	     %=
\__pow__		    |       **
\__ipow__	    |	      **=
\__iter__       |      疊代器
\__next__     |      利用 next 和iter  自定義for迴圈
\__str__        |       如何在shell顯示
\__repr__     |       如何在print顯示

---
get the all object on class
```
>>>dir(class_name)
['__add__', '__class__',..., '__subclasshook__', 'capitalize', 'casefold',..., 'zfill']
```

python is Dynamic language    
you can change the structrue of class       
and  when you run the program ,it check what class type on the value    
```
>>> class Student():
...     pass
...
>>> s=Student()
>>> s.name='ok'    #隨時綁定方法
>>> s.name
'ok'
>>> z=Student()
>>> z.name              #其他不影響
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: 'Student' object has no attribute 'name'
>>> def age(self,number):
...     return number
...
>>> Student.age=age  # 直接以物件綁定 ,所有都影響
>>> s.age(100)
100
>>> z.age(90)
90
```
 \__slots__  可以指定能綁定的物件,但是對繼承的子類沒有作用
```
class Student(object):
    __slots__ = ('name', 'age') # 用tuple定义允许绑定的属性名称
```
@property    讓函數可以直接存取
```
class Student(object):

    @property   #當你為一個方法添加此裝飾句  讓你可以用'='來呼叫
    def score(self):
        return self._score

    @score.setter    #同時自動創建此裝飾句  用來修改
    def score(self, value):
        #你對於參數的檢查
        if not isinstance(value, int):
            raise ValueError('score must be an integer!')
        if value < 0 or value > 100:
            raise ValueError('score must between 0 ~ 100!')
        self._score = value

>>> s = Student()
>>> s.score = 60 # 自動轉成s.set_score(60)
>>> s.score # 自動轉成s.get_score()
60
>>> s.score = 9999
Traceback (most recent call last):
  ...
ValueError: score must between 0 ~ 100!
```
