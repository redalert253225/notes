&sect;Chapter 1
===
Datatype
===
* you don't need to declare the datatype of variables, and you can't declate
```
>>> x=True
>>> type(x)
<class 'bool'>
>>> x=1.0
>>> type(x)
<class 'float'>
>>> x='abc'
>>> type(x)
<class 'str'>
```

>Booleans
--

True or False

True == 1  False==0  //The first letter should be capitaized
```python
>>> True+True
2
>>> True*False
0
>>> 1<2
True
>>> 2>4
False
```

if  != 0    it is True
```
>>> if -2:
...     x=2
...
>>> x
2
>>> if 0.01:
...     x=3
...
>>> x
3
>>> if 0.0:
...  x=4
...
>>> x
3
```
>Numbers
--

* integers

no limit

```
>>> x=10000000000000000000000000000
>>> x
10000000000000000000000000000
```
 int() will truncate number to close to 0
 ```
 >>> int(2.5)
2
>>> int(-2.5)
-2
 ```
* floats

accurate to 15 decimal places
```
>>> x=1.999999999999999999999999999
>>> x
2.0
>>> import math
>>> math.pi
3.141592653589793
>>> math.sin(math.pi/2)
1.0
>>> math.tan(math.pi/4)
0.9999999999999999
```
* operator  " / " always reture  float

```
>>> 10/2
5.0
```
operator  " // " can reture  integers  and truncate to less one
```
>>> 11//5
2
>>> -11//5
-3
```
if  either number is float than it will  truncate  but return float
```
>>> 11//5.0
2.0
```
operator  " ** " is  x to power of  n

operator  " % " is remainder
```
>>> 11.0**3
1331.0
>>> 10%3
1
```
* fractions

need to import fractions

auto reduce
```
>>> import fractions
>>> x=fractions.Fraction(4,2)
>>> x
Fraction(2, 1)
```
no zero denominator
```
>>> x=fractions.Fraction(4,0)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "/usr/lib/python3.7/fractions.py", line 178, in __new__
    raise ZeroDivisionError('Fraction(%s, 0)' % numerator)
ZeroDivisionError: Fraction(4, 0)
```
>Strings
--


>Bytes  and byte arry
--


>Lists
--

like a item array,declare by " [ ]"

it is orderde and zero-based

* negative index mean from end  , -1 always last item

```
>>> test_list=[1,2.0,'three',['four','five']]
>>> test_list[0]
1
>>> test_list[-1]
['four', 'five']
```
you can get part of list ,called "slice"

test_list[x:y]    mean test_list[x] t0 test_list[y-1]
```
>>> test_list[1:4]
[2.0, 'three', ['four', 'five']]
>>> test_list[0:-2]
[1, 2.0]
```
you can use "+" or .extend()   to add list
```
>>> test_list=test_list+['six']
>>> test_list      
[1, 2.0, 'three', ['four', 'five'], 'six']
>>> test_list.extend([7,8])
>>> test_list
[1, 2.0, 'three', ['four', 'five'], 'six', 7, 8]
```
.append()  is only add 0ne item
```
>>> test_list.append([9,10])
>>> test_list
[1, 2.0, 'three', ['four', 'five'], 'six', 7, 8, [9, 10]]
```
count item and index
```
>>> test_list
[1, 2.0, 'three', ['four', 'five'], 'six', 7, 8, [9, 10]]
>>> test_list.count('1')
0
>>> test_list.count(1)
1
>>> test_list.count(2)
1
>>> test_list.count(2.0)
1
>>> 'five' in test_list
False
>>> ['four','five'] in test_list
True
>>> test_list.index(7)
5
```
you can't index a item which is not in the list

it will error  not like other program return -1

because -1 is a index on python3
```
>>> test_list.index(10)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ValueError: 10 is not in list
```
use 'del' or .remove()  to remove  item

```
>>> test_list=[1, 2.0, 'three', ['four', 'five'], 'six', 7, 8, [9, 10]]
>>> del test_list[3]
>>> test_list[3]
'six'
>>> test_list  
[1, 2.0, 'three', 'six', 7, 8, [9, 10]]
>>> test_list.remove(7)
>>> test_list          
[1, 2.0, 'three', 'six', 8, [9, 10]]
```

you can't remove a item which is not in the list,too
```
>>> test_list.remove(10)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ValueError: list.remove(x): x not in list
```

* del all list at once dosen't lose memory (from  stack overflow)
```
>>> test_list
[1, 2.0, 'three', ['four', 'five'], 'six', 7, 8, [9, 10]]
>>> del test_list
>>> test_list
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
NameError: name 'test_list' is not defined
```

.pop()  will remove item and return this item
```
>>> test_list          
[1, 2.0, 'three', 'six', 8, [9, 10]]
>>> test_list.pop()     
[9, 10]
>>> test_list.pop(0)
1
>>> test_list=[]
>>> test_list.pop()
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
IndexError: pop from empty list
```
>Tuples
--

Tuples is a constants List,declare by " ( )"

there is no the keyword 'const' on python3
```
>>> test_tuple=(1,'2',3.0,'four')
>>> test_tuple[0]                
1
>>> test_tuple[-1]
'four'
>>> test_tuple[1:2]
('2',)
>>> test_tuple[1:3]
('2', 3.0)
```
You can't add , remove or change after created
```
>>> test_tuple.pop()
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: 'tuple' object has no attribute 'pop'
>>> test_tuple.exten([0])
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: 'tuple' object has no attribute 'exten'
>>> test_tuple.remve(1)  
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: 'tuple' object has no attribute 'remve'
```
there is a way to easy copy  Tuples at once
```
>>> test_cp=('a',2,'c')
>>> (x,y,z)=test_cp
>>> x
'a'
>>> y
2
>>> z
'c'
```
quiky assig names to range  of values
```
>>> (zero,one,two,three,four,five)=range(6)
>>> zero
0
>>> one
1
>>> two
2
>>> three
3
>>> four
4
>>> five
5
>>> [zero,one,two,three,four,five]=range(6)
>>> zero
0
>>> one
1
>>> two
2
```
>Sets
--

it is  unorder and unique , declare by " { } "

you can't use index to get item from Sets

you can create a set from list
```
>>> test_set={10,8,2,3}
>>> test_set           
{8, 3, 10, 2}
>>> test_list=[1,2,5,6]
>>> test_set=set(test_list)
>>> test_set
{1, 2, 5, 6}
>>> test_set[0]
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'set' object does not support indexing
```
* you can set() a empty set,  but not use "{ }"
```
>>> test_set=set()    
>>> type(test_set)
<class 'set'>
>>> test_set={}   
>>> type(test_set)
<class 'dict'>
```

.add() and .update()

if you add a same item it just no-op
```
>>> test_set={0,1}
>>> test_set.add(3)
>>> test_set       
{0, 1, 3}
>>> test_set.add(0)
>>> test_set
{0, 1, 3}
>>> test_set.update({3,4,5},[1,2,3,6,7,8])
>>> test_set
{0, 1, 2, 3, 4, 5, 6, 7, 8}
```
.discard() and .remove()
there is one difference you can't remove a item which is  not contained
```
>>> test_set
{0, 1, 2, 3, 4, 5, 6, 7, 8}
>>> test_set.discard(8)
>>> test_set
{0, 1, 2, 3, 4, 5, 6, 7}
>>> test_set.discard(8)
>>> test_set
{0, 1,2, 3, 4, 5, 6, 7}
>>> test_set.remove(2)
>>> test_set
{0, 1, 3, 4, 5, 6, 7}
>>> test_set.remove(2)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
KeyError: 2
```
.pop() can remove a item, but you can't control
```
>>> test_set={17,15,3,45}
>>> test_set.pop()
17
>>> test_set.pop()
3
>>> test_set      
{45, 15}
```
.clear()  will remove all
```
>>> test_set={17,15,3,45}
>>> test_set.clear()     
>>> test_set
set()
>>> test_set.pop()
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
KeyError: 'pop from an empty set'
```

.difference() return a set which in a but not b
```
>>> x_set={1,2,3,4}
>>> y_set={3,4,5,6}
>>> x_set.difference(y_set)
{1, 2}
```
.union()   like OR

.intersection()  like AND

.symmetric_difference()   like XOR
```
>>> x_set={1,2,3,4}
>>> y_set={3,4,5,6}
>>> x_set.union(y_set)
{1, 2, 3, 4, 5, 6}
>>> x_set.intersection(y_set)
{3, 4}
>>> x_set.symmetric_difference(y_set)
{1, 2, 5, 6}
```

>Dictionaries
--

a set with a index by user-defined

declare by "{}" ,but need to give index
```
>>> web_url_dict={'google':'www.google.com','youtube':'www.youtube.com','facebook':'www.facebook.com'}
>>> web_url_dict['google']            
'www.google.com'
>>> web_url_dict['www.google.com']
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
KeyError: 'www.google.com'
>>> web_url_dict['baidu']='www.baidu.com'
>>> web_url_dict                  
{'google': 'www.google.com', 'youtube': 'www.youtube.com', 'facebook': 'www.facebook.com', 'baidu': 'www.baidu.com'}
>>> web_url_dict['baidu']='www.baidu.com.tw'
>>> web_url_dict['baidu']                
'www.baidu.com.tw'
```
>None
--

* it is a datatype  and diferent from other

cmparing to another will always reurn False  

anyitem can be None
```
>>> None==0
False
>>> None=={}
False
>>> None==None
True
>>> x=None
>>> y=None
>>> x==y
True
>>> None==False
False
>>> None==True
False
```
