>if
--

if  your_condition :
    XXX
elif  your_condition :
    XXX
else :
    XXX
```
if __name__ == '__main__':
    if False :
        print('1')
    else  :
        print('0')
        a=10
        if  a>10 :
            print('a>10')
        elif a<10 :
            print('a<10')
        else :
            print(a)

[yu@archlinux pythonLearn]$ python3 pythonTest.py
0
10
```
x = a if  your_condition  else b     

like    
```
if your_condition :
    x = a
else :
    x = b
```

```
>>> x = 3 if True else -3
>>> x
3
>>> x = 3 if False else -3
>>> x
-3
```

>While
--

while   your_condition :

```
while iCount<2:
      print(iCount)
      iCount+=1
```

>For
--

for  i  in  range( start, end, difference ) :

run from 'start' to 'end-1'

```
if __name__ == '__main__':
    y=10
    for n in range(2, y):            //default value  of difference =1     
        test='alive?'
        for x in range(2, n):
            if n % x == 0:
                print(n, '=', x, '*', n//x)
                break
        else :                  //when work with 'for', if  program 'break'  than not run
            print(n,'is Prime number')
        print('now n=',n,'\n')                  //run every time
    print(' x still alive x=',x)                 
    print(test)

    [yu@archlinux pythonLearn]$ python3 pythonTest.py
    2 is Prime number
    now n= 2

    3 is Prime number
    now n= 3

    4 is Even
    4 = 2 * 2
    now n= 4

    5 is Prime number
    now n= 5

    6 is Even
    6 = 2 * 3
    now n= 6

    7 is Prime number
    now n= 7

    8 is Even
    8 = 2 * 4
    now n= 8

    9 = 3 * 3
    now n= 9

     x still alive x= 3
    alive?
```
negative in For

```
for n in range(2, -10,-2):       
    for x in range(2, n):
            print('start')
    print(x,'n=2')                    //when n=2  the x was not declared  ,so it will error

    [yu@archlinux pythonLearn]$ python3 pythonCopy.py
    Traceback (most recent call last):
      File "pythonCopy.py", line 6, in <module>
        print(x,'n=2')
    NameError: name 'x' is not defined
'''
'''
if __name__ == '__main__':
    for n in range(2, -10,-2):
        for x in range(2, n):                         
            print('start')                      // never run
        print(n,'n=2')

    [yu@archlinux pythonLearn]$ python3 pythonCopy.py
    2 n=2
    0 n=2
    -2 n=2
    -4 n=2
    -6 n=2
    -8 n=2

```
for  i  in  arrary :    ==  for  i  in range ( 0, array.len, 1 ) :    
i == value of arrary
```
if __name__ == '__main__':
    listTest=['one','two','three']
    tupleTest=('o','t','h')
    setTest={8,6,9}
    dictionaryTest={'one':1,'two':2}
    for i in listTest:
        print(i)
    for i in tupleTest:
        print(i)
    for i in setTest:
        print(i)
    for i  in dictionaryTest:
        print(i)
    for i , j in dictionaryTest.items() :
        print(i,":",j)

        [yu@archlinux pythonLearn]$ python3 pythonTest.py
        one
        two
        three
        o
        t
        h
        8
        9
        6
        one
        two
        one : 1
        two : 2

```
if you need index and value of list ,use 'enumerate'
```
>>> list = ['a','b','c']
>>> for i ,j in enumerate(list):
...     print( i , " " , j)
...
0   a
1   b
2   c
```
>Continue , Break , Else   with loop
--

continue: go to your_condition to start next loop

break : leave the loop

else (with loop) :  it wil run when condition fale  in  'while' or 'for' ,but not after 'break'

```
if __name__ == '__main__':
    listTest=['one','two','three']
    iCount=len(listTest)-1
    while iCount>0:
        print( listTest[iCount] )
        iCount-=1
        if iCount == 0 :
            break
    else :
        print('useless')
    for i in listTest :
        if  i != 'one' :
            continue
        print(i,'-->in for')

        [yu@archlinux pythonLearn]$ python3 pythonTest.py
        three
        two
        one -->in for
```
