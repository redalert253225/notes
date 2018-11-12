threading
--
use   'threading ' module
```
import threading
```
create  a thread  
--
threading.Thread(target = 'functionName' , args=( 'value' , ))  
.start()   : start run  thread    
.join( timeout = None )    : wait thread  ending or time out  , if time out != 0 , you must call is_alive() after join() to check  thread    
```
import threading
import time
import random

def job( i ):
    for j  in range(5) :
        time.sleep(random.random())
        i[0]+=1


if __name__=='__main__':
    i = [0]
    t = threading.Thread(target = job, args=( i , ))
    #traget : pass function , no()
    #args : pass parameter ,  if only one need to add '' , '' to become a tuple , or [ i ]  change to list

    t.start()   #start run  t
    for j  in range(5) :
        time.sleep(random.random())
        i[0]+=1
        print(i)
    t.join()    #wait thread  ending
    print("last i : {}".format( i ))

[yu@archlinux ~]$ python3 -m pythonLearn.test
[1]
[2]
[4]
[8]
[9]
last i : [10]
```
thread Class  
```
def __init__( self ):
    threading.Thread.__init__()
def run(self) :
    #do anything
```
```
import threading
import time
import random

class myThread( threading.Thread ) :
    def __init__( self , i ):
        threading.Thread.__init__(self)
        self.i=i

    def run(self ) :       #use  'run' , to do anything you want
        for j  in range(5) :
            time.sleep(random.random())
            self.i[0]+=1

if __name__=='__main__':

    i=[0]
    t = myThread(i)
    t.start()
    for j  in range(5) :
        time.sleep(random.random())
        i[0]+=1
        print(i)
    t.join()
    print("last i : {}".format( i ))

[yu@archlinux ~]$ python3 -m pythonLearn.test
[3]
[6]
[8]
[9]
[10]
last i : [10]
```
Lock   
--
threading.Lock()    
.acquire()  : get lock if locked it will  wait    
.release()  : release  lock
```
import threading
import time
import random

class myThread( threading.Thread ) :
    def __init__( self , i , lock , name ):
        threading.Thread.__init__(self)
        self.i=i
        self.lock = lock
        self.name = name

    def run(self ) :       
        self.lock.acquire()   #get lock if locked it will  wait  
        for j  in range(5) :
            time.sleep(random.random())
            self.i[0]+=1
            print( self.name, self.i[0] )
        self.lock.release()    #release  lock

if __name__=='__main__':
    i=[0]
    lock = threading.Lock()
    t1 = myThread( i , lock , 't1')
    t2 = myThread( i , lock , 't2')
    t1.start()
    t2.start()
    t1.join()
    t2.join()
    print("last i : {}".format( i ))

[yu@archlinux ~]$ python3 -m pythonLearn.test
t1 1
t1 2
t1 3
t1 4
t1 5
t2 6
t2 7
t2 8
t2 9
t2 10
last i : [10]
```
RLock   
--
 threading.RLock()    
 .acquire()  : get lock if locked by other it will  wait    
 .release()  : release  lock

Lock : can unlock by other thread  
RLock : only unlock by who locked it , and  can be acquired many times by the same thread  
notice ,  use  'release()' same number of times   as  'acquire()'  
```
import threading
import time
import random

class myThread( threading.Thread ) :
    def __init__( self , i , lock , name ):
        threading.Thread.__init__(self)
        self.i=i
        self.lock = lock
        self.name = name

    def run(self ) :       
        self.lock.acquire()       #first lock
        for j  in range(5) :
            time.sleep(random.random())
            self.i[0]+=1
            print( self.name, i[0] )
        self.cut()
        self.lock.release()        #release first lock

    def cut(self ) :      
        self.lock.acquire()       #second lock
        for j  in range(5) :
            self.i[0]-=1
            print( self.name, i[0] )
        self.lock.release()        #release second lock

if __name__=='__main__':
    i=[0]
    lock = threading.RLock()       #if you use Lock() ,  the program will dead lock
    t1 = myThread( i , lock , 't1')
    t2 = myThread( i , lock , 't2')
    t1.start()
    t2.start()
    t1.join()
    t2.join()
    print("last i : {}".format( i ))
```

Condition
--
threading.Condition()    
.acquire()  : get lock if locked  it will  sleep
.release()  : release  lock  
.wait()   :  sleep and  wait notify()   
.wait_for() :    wait condition
.notify()   :   wake other thread  up    
.notify_all()    : wake all thread    

notice, wait() and  notify()  need to called on Condition , if called after release() or before acquire()  , it will error
```
import threading
import time
import random

class MyThread( threading.Thread ) :
    min=0
    max=100
    answer=-1
    game = 1

    def __init__( self , condtion , name):
        threading.Thread.__init__(self)
        self.condtion = condtion
        self.myname = name
        self.random=random.Random()

    def run(self) :
        self.condtion.acquire()     # get lock
        while MyThread.game and self.checkAnswer(self.action()):    
            self.condtion.notify()      #  wake other  up
            self.condtion.wait()          # sleep and  wait
        self.condtion.release()         # relrase lock

    def myrandom(self, one ,two):
        return self.random.randint(one,two)

    def action(self):
        print('{} say:  I think...'.format(self.myname) )
        time.sleep(self.myrandom(1,3))
        myAnswer=self.myrandom( MyThread.min+1, MyThread.max -1)
        print('      ',myAnswer,'!')
        return myAnswer

    def checkAnswer(self , number):
        if number == MyThread.answer :
            MyThread.game = 0
            print('\n {}  is loser'.format(self.myname) )
            self.condtion.notify_all()     # game over  wake all up
            return 0
        if number > MyThread.answer :
            MyThread.max=number
        else :
            MyThread.min=number
        print("\n      {}-{}\n".format(MyThread.min,MyThread.max))
        return 1

if __name__=='__main__':
    condtion= threading.Condition()
    MyThread.answer=random.randint(MyThread.min+1, MyThread.max -1)

    t1= MyThread( condtion, 't1' )
    t2 = MyThread( condtion , 't2' )
    t1.start()
    t2.start()
    t1.join()
    t2.join()
    print("game over")
```

with Lock :
--
```
with lock :
    #do

wtih condition :
    #do
    self.condtion.notify()      
    self.condtion.wait()  
```
equal
```
lock.acquire()    
#do...
lock.release()

condition.acquire()    
#do...
self.condtion.notify()      
self.condtion.wait()  
condition.release()
```
