in python3 , if you definition  \__iter__ ,then  it is a Iterator  ,
with \__next__  , the object can use by  for()
```
class MyObject():
    #..
    def __iter__( self ):
        return self
    def __next__( self ) :
        try:
            #...
        except:
            raise StopIteration

test=MyObject()
for  test in range(N):
    #...
```
And if you definition \__enter__ and \__exit__ , it  is a 'contextor'   
it like Decorator , decorator is use '@'  , and contextor is 'with'    
```
def wapper():
    def myProgram( f ):
        #...A
        rn = f()
        #...B
        return rn
    return myProgram

@myProgram
othrerProgram():
    #...C
```
```
class MyObject():
    #...
    def  __enter__( self ):
        #...A
    def __exit__( self ):
        #...B

testContextor = MyObject()

def otherProgram():
    #...C

with testContextor as var:
        otherProgram()
```
in the example , what is the 'with' doing
1. run  testContexter
2. run \__enter__
3. if there is 'as' ,  let 'var' behind the 'as'  get the return from \__enter__()
4. run othrerProgram
5. if any error , run \__exit__ , return the error message
6. before leave 'with' ,run \__exit__

in python3 , there are some object  had supported Contextor
file     
decimal.Context     
thread.LockType    
threading.Lock     
threading.RLock     
threading.Condition    
threading.Semaphore     
threading.BoundedSemaphore     

for example open file
```
# open file
f = open(filename)
# ...
f.close()
```
if we are afriad  after error  , program dosen't close the file      
so we will add 'try ... finally ...'
```
f = open(filename)

try:
  # ...
finally:
  f.close()
```
the above program , we can replace by 'with'  
```
# open file
with open(filename) as f:
  # ...

# open file and write
with open('file.txt', 'w') as f:
  f.write('Hello, world!')
```
for example threading.RLock     
```
lock =  threading.RLock()
lock.acquire()    
#...
lock.release()
```
```
lock =  threading.RLock()
with lock :
    #...
```
