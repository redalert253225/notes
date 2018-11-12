try...except       
如果try語句裡有異常,執行except    
raise     
引發異常,後面接異常物件,如果在except語句內,則默認引發原異常     
```
def change( number ):
    print("F_Start")
    change2(number)
    print("F_End")

def change2( number ):
    print("S_Start")
    try:
        if number== 1 :   #可以利用異常 一次跳出多層呼叫
            raise Exception('my bad') #主動引發異常,自訂異常,異常訊息'my bad'
        print('test')
    except :         # 如果try 裡有異常 執行該句
        print('S_Error')
        raise     #再次引發異常,所以後面不執行,跳出該程式
    print("S_End")

if __name__ =='__main__' :
    allowFileName=set(['456'])
    name =1
    print('Main_Start')
    try:
        change(name)   
    except Exception as e: #如果有指定異常類別,except只有該異常會觸發
        print(e)   #印出異常訊息  str(e)  自動變字串
    print('Main_End')    #沒有再次引發異常,所以後面的語句正常執行

[yu@archlinux ~]$ python3 -m pythonLearn.test
Main_Start
F_Start
S_Start
S_Error
my bad
Main_End
```

如果change有處理異常,且未再引發,則後面正常執行
```
def change( number ):
    print("F_Start")
    try:
        change2(number)   #change2 有異常,且處理後再引發,所以這裡會執行 except
    except :
        pass  
    print("F_End")    #except沒有再次引發異常,所以後面的語句正常執行

def change2( number ):
    print("S_Start")
    try:
        if number== 1 :
            raise Exception('my bad')
        print('test')
    except :         
        print('S_Error')
        raise     
    print("S_End")

if __name__ =='__main__' :
    allowFileName=set(['456'])
    name =1
    print('Main_Start')
    try:
        change(name)   #change 有處理異常, 且沒再引發 , 所以這裡沒有異常
    except Exception as e:  #不會觸發
        print(e)   
    print('Main_End')    

[yu@archlinux ~]$ python3 -m pythonLearn.test
Main_Start
F_Start
S_Start
S_Error
F_End
Main_End
```
