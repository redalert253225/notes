&sect;Chapter 2
==

> about  \__pycache__
--

When you run a program in python, after the interpreter compiles it to bytecode  and stores it in the \__pycache__ folder. If you look in there you will find a bunch of files sharing the names of the .py files in your project's folder, only their extensions will be either .pyc or .pyo. These are bytecode-compiled and optimized bytecode-compiled versions of your program's files, respectively.

 it does is make your program start a little faster.


 >import
 --

 "import name"   include other program
 ```
import otherpy

otherpy.fun1(a,b)
```
```
 from otherpy import fun1

 fun1(a,b)
 ```
 ```
import otherpy as imp1

imp1.fun1(a,b)
```

>Functions
--

use "def"  to declare a funtion

you don't need to define retrun Datatype, and  input Datatype

* if no return ,function always return None

in python3 ,use colon (:) and the indentation  to decide the function code starts and stops

```
def   funtion_name ( item1_name, item2_name ):
    ...
    ...
```
you can use \*itemN_name to get many value
```
def   sum ( *getAll ):
    addGetAll=0
    for i in getall :
        addGetAll += i
    return addGetAll
```
you can give a initial value to the input of the function, than it will work when the caller don't give value
 ```
 >>> def test(x,y=5):
...  print(x+y)
...
>>> test(3)
8
>>> test(3,4)
7
 ```
if you have a function behind  a function, there is in same block
```
def   funtion1_name ( item1_name, item2_name ):
    def   funtion2_name ( item3_name, item4_name ):
        ...
    item3_name     //it is  still uesful

item1_name  // it is undefined
```

>about   \__name__ == '__main__'
--

```
# cool.py

def cool_func():
    print('cool_func(): Super Cool!')

if __name__ == '__main__' :
    print('Call it locally')
    cool_func()
```

if  no  use
" \__name__ == '\__main__' "
, when  other program import
" from cool import cool_func "
, all the program wil run once
, even you only call "cool_func()"
when program is imported  it will be run once
```
# other.py

from cool import cool_func

print('Call it remotely')
cool_func()
```
output:
```
Call it locally
cool_func(): Super Cool!
Call it remotely
cool_func(): Super Cool!
```

>Function variable transfer
--

there is two type to datatype

immutable : Booleans, Numbers, Strings, Tuples, Bytes

mutable :  Lists, Sets, Dictionaries
```
>>> x=4
>>> y=4
>>> x is y
True
>>> x==y
True
>>> x=[1,2]
>>> y=[1,2]
>>> x is y                 //'is' compare  address
False
>>> x==y                // '==' compare  value
True

>>> x=y=3
>>> y=1
>>> x
3
>>> x=y=[1,2]
>>> y[0]=9
>>> x[0]
9
```

immutable  call by value

mutable  call by address

```
＃pythonImport.py

def  test (  x , y  ) :
    x=6
    y[0]='zero'

if __name__ == '__main__':
    print('import')

#pythonTest.py
import   pythonImport

if __name__ == '__main__':
    i=1
    listTest=['one','two','three']
    print( i, '-', listTest )
    pythonImport .test( i, listTest )
    print( i, '-', listTest )

    [yu@archlinux pythonLearn]$ python3 pythonTest.py
    1 - ['one', 'two', 'three']
    1 - ['zero', 'two', 'three']
```
if you want to change  immutable value after call function  

use 'return' or 'global'

'global'  let outside value can use on inside , but it not good for maintaining

```
def function1(a):      
    function2()

def function2():
    global a     
    a=3
if __name__=='__main__':
    a=1
    function1(a)

>>>a
3
```
or you don't want to change mutable  value after call function

rebend it

```
def changeList(b):
    b[0]=4
    b=b+[4]
    b[0]=5

if __name__=='__main__':
    a=[1,2,3]
    changeList(a)

>>>a[0]
1
```

>docstring, remark  
--

''' your_remark '''
 ```
 if __name__ == '__main__':    //it ok
     print('test')
     '''
     print('mark')
     '''
     print('ok')

if __name__ == '__main__':    //it ok,too
    print('test')
    '''
    print('mark')
'''
    print('ok')

//but
if __name__ == '__main__':
    print('start')
'''                                                       //first '''  let program think function is end
    print('so')
'''
    print('OK')
IndentationError: unexpected indent

```

>print  input
--

print( a, b, c)     and it will  auto change line
because it has a keyword 'end',  default value ='\n'

```
if __name__ == '__main__':
    print('keyin interval char:')
    x=input()
    y=[2,4,'six']

    print('string',2,y)
    for i in y :
        print(i,end=x)      #use end='  XX  '   to change  '\n'

    [yu@archlinux pythonLearn]$ python3 pythonCopy.py
    start
    3 string 2 [2, 4, 'six']
    2-- 4-- six--

  ```
old :  
print( '%s %s' % ('one', 'two') )  
new :  
print( '{} {}'.format('one', 'two') )   # you can add index in {}
```
>>> print('{1}{0}{1}'.format('a','b'))
bab
```

 >pass
 --

  it’s a  placeholder

  in python3  if you declare something like function , need  indentation

  but you do nothing , than you can use it

```
>>> def fun1():
...       
...
  File "<stdin>", line 3
    ^
IndentationError: expected an indented block
>>> def fun1():
...     pass
...
>>> x=1
>>>

```
