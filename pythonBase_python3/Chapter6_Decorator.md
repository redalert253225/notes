In python3  all the function  can pass to other function like parameters
```
def use_logging(func):
    print("%s is running" % func.__name__)   #func.__name__=='foo'
    func()

def foo():
    print('i am foo')

use_logging(foo)
```

Simple decorator
```
def use_logging(func):
    def wrapper():
        print("%s is running" % func.__name__)
        return func()   # 把 foo 當參數傳過來,執行func()就相當於執行foo()
    return wrapper   # 沒有()是回傳此函式  不是執行

def foo():
    print('i am foo')

foo = use_logging(foo)  #  use_logging 返回時的函式對象wrapper,相當於 foo -> wrapper, 等於重新定義foo 函式

foo()                   # 此後執行foo()就相當於執行 wrapper()
```
* 因為是重新定義foo ,  所以原先的 foo()其實已經不存在了 ,  
如果回傳的不是 wrapper 而是 wrapper () ,
之後使用 foo() , 會出現  ' 'NoneType' object is not callable '

Decorator by using '@'
```
def use_logging(func):
    def wrapper():
        print("%s is running" % func.__name__)
        func()            #因為我定義的函數'foo()'沒有回傳值, 如果沒有return執行無異
        print("end!")
    return wrapper

@use_logging                 #相當於 foo = use_logging(foo)
def foo():
    print("i am foo")

foo()
```

if  function 'foo' has  parameters
```
def use_logging(func):
    def wrapper( *args, **kwargs ):                # ' *args '是List，' **kwargs '是字典
        print("%s is running" % func.__name__)
        r= func( *args, **kwargs )
        print("end!")
        return r     #嚴謹的寫法 讓foo()回傳值可以傳回去
    return wrapper

@use_logging
def foo( x, y='i am foo'):
    print('第' + x+ ':    ' + y)func

foo('2')
```

Decorator with parameters
```
def level( z ):     #再包一層
    def use_logging(func):
        def wrapper( *args, **kwargs ):    #實際會取代原function的function
            if (z==1):
                print('max!  ', end=' ')
            else:
                print('min.', end=' ')
            print("%s is running" % func.__name__)
            r = func( *args, **kwargs )
            print("end!")
            return r     
        return wrapper
    return use_logging

@level( 1 )            
def foo( x, y='i am foo'):
    print('第' + x+ ':    ' + y)

foo('2')
```

after using decorator ,some  attributes of the function lose
```
>>>print(foo.__name__)
wrapper
```

use another  decorator  'wraps'
```
from functools import wraps   #import  wraps

def level( z ):
    def use_logging(func):
        @wraps(func)                            #the decorator will let some  attributes of foo come back
        def wrapper( *args, **kwargs ):   
            if (z==1):
                print('max!', end=' ')
            else:
                print('min.', end=' ')
            print("%s is running" % func.__name__)
            r = func( *args, **kwargs )
            print("end!")
            return r
        return wrapper
    return use_logging

@level( 1 )            
def foo( x, y='i am foo'):
    print('第' + x+ ':    ' + y)

print(foo.__name__)         #now it is 'foo'
```

Decorator  Class   
```
class use_logging( object ):

    def __init__( self, func):
        self.func = func

    def __call__( self ,  *args, **kwargs ):               #run the decorator of the function  which you want
        print("%s is running" % self.func .__name__)
        r = self.func ( *args, **kwargs )
        print("end!")
        return r

@use_logging          #等於呼叫__call__
def foo( x, y='i am foo'):
    print('第' + x+ ':    ' + y)

```

Decorator Class with parameters
```
from functools import wraps

class use_logging( object ):

    def __init__( self, level):    #改成decorator  的 參數
        self.level = level

    def __call__( self ,  func ):      #這裡才開始導入被裝飾的function,以下用法就跟前面的 decorator function 用法一樣
        @wraps(func)      #純Decorator Class(上一個範例)如何套用@wraps 未知
        def wrapper(  *args, **kwargs ):
            self.checkLevel()
            print("%s is running" % func .__name__)
            r = func ( *args, **kwargs )
            print("end!")
            return r
        return wrapper

    def checkLevel( self ):
        if (self.level ==1):
            print('max!', end=' ')
        else:
            print('min.', end=' ')

@use_logging( 1 )           
def foo( x, y='i am foo'):
    print('第' + x+ ':    ' + y)
```
裝飾句為重新定義function, 以上例而言, 當程式碼跑到''@''即是執行'foo=use_logging( 1 )'

在flask裡,變形應用
```
class NotFlask():
    def __init__(self):
        self.routes = {}
        print('1')
    def route(self, route_str):
        print('3')
        def decorator(f):               
            print('4')
            self.routes[route_str] = f      #將函式 和 路徑  存成routes字典
            return f                                     #傳回原程式 hello = hello()
        return decorator

    def serve(self, path):
        print('7')
        view_function = self.routes.get(path)   #從routes字典取出對應funtion
        if view_function:
            return view_function()
        else:
            raise ValueError('Route "{}"" has not been registered'.format(path))

app = NotFlask()

print('2')
@app.route("/")   #主要是將程式存到另一個地方
def hello():
    return "Hello World!"

print('5')

if __name__=='__main__':
    print('6')
    print ( app.serve("/") )
    print ( hello.__name__ )

[yu@archlinux ~]$ python3 -m pythonLearn.test
1
2
3
4
5
6
7
Hello World!
hello
```

many decorator in one function
```
@a
@b
@c
def f ():      # f = a(b(c(f)))
    pass
```
