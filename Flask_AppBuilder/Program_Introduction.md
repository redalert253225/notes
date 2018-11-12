```
class AddInvoiceNumberView(SimpleFormView):   
# 上傳CSV 檔案寫入發票號碼
    form                 #指定要使用的表單                                                  
    form_title      #表單的標題
    invoTuple      #用來比對excel各表格的欄位名稱 , 可以更改 名稱  不可以更動順序    
    leInvonumber            # 發票數字部份的長度
    leInvoiceWord            # 發票字母的長度
    leBusinessNumber  # 營業人統編的長度
    allowFileName= set(['csv'])   #設定可以允許的副檔名

    form_post(self, form): #當按下save 按鈕 , 執行的程式會呼叫此函數
    # 檢查檔案  並寫入
        form = 一個表單物件
        如果有錯誤訊息 :
            return flash(str(e))顯示錯誤訊息,重新抓新此頁面
        return 指向InvoiceNumberView

    checkFileName( name ):
    #確認副檔名是否存在於  allowFileName
        name = 字串,包含副檔名的整個檔案名稱

    upLoadInvoiceNumber( works , invoDict):
    #將發票號碼寫入資料庫
        works = 字串陣列  ,分割過後的檔案內文
        invoDict 字典 , 儲存各欄位在 works 的位置

    getTime():
    # 取得時間,時間格式與資料庫一致
        return 字串,呼叫當下的時間

    checkFile(works , invoDict ,businessNumber, monthSlot ):
    #確認檔案內容格式
        works = 字串陣列  ,分割過後的檔案內文
        invoDict = 字典 , 儲存各欄位在 works 的位置
        businessNumber = 字串 ,表單回傳的營業人統編
        monthSlot = 字串 ,表單回傳的期別

    checkInvoiceWord(InvoiceWord , line ):
    #確認發票號碼字母 是否大寫 , 長度
        InvoiceWord = 字串 , 發票的抬頭字母  
        line = 整數 , 目前在第幾行

    checkBusinessNumber(number ,businessNumber ,line):
    #確認營業人統編是否與輸入一致   統編長度是否正確
        number = 字串,被比對的數字
        businessNumber  = 字串,表單回傳的 營業人統編
        line = 整數 , 目前在第幾行

    checkRepeat(header , startNumber , endNumber , line):
    #資料庫搜尋 確認有無重複
        header = 字串 , 發票字母
        startNumber = 字串 , 發票起始號碼
        endNumber = 字串 , 發票末尾號碼
        line = 整數 , 目前在第幾行

    strAdd( self ,number ):
    #字串加一
        number = 數字字串
        return 字串,數字加一 , overflow會錯報

    checkNumber( self , startNumber , endNumber , line):
    #確認發票號碼數字 ,長度
        是否為50倍數 , 起始數字為 '00' '50'結尾
        startNumber = 字串 , 發票起始號碼
        endNumber = 字串 , 發票末尾號碼
        line = 整數 , 目前在第幾行

    strTOint(self , number):
    #字串轉成數字,檢查是否有非數字
        number = 數字字串
        return 整數

    checkMonthSlot(monthSlot , checkMonth,line):
    #確認期別 與輸入是否相符
        期別是否為 目前時間所屬期別後四個月內, EX: 2018 11月 ,只能使用 2018/11-12,2019/1-2
        monthSlot = 字串,檔案內的期別
        checkMonth = 字串,表單回傳的期別
        line  = 整數 , 目前在第幾行
        return 字串,符合資料庫的期別格式   1112

    checkKey(works):
    #檢查關鍵字 字否存在檔案內 以及對應的位置
        works = 字串,檔案的第一行
        return invoDict 字典,各關鍵字對應的陣列位置

    cutStr(word):
    #將資料分段存成二維字串陣列
        word = 字串,原檔案內容
        return word 二維字串陣列 將原檔一換行  逗號 分割儲存  EX : word[0][2]  第一行第三格

執行流程:
form_post{
    checkFileName()                                                         #確認檔名
    works=file.decode(encoding='Big5')                 #轉編碼  原檔為Big5
    works=cutStr()                                                            #切割字串
    invoDict=checkKey()                                                #確認關鍵字 在第0行
    checkFile{                                                                      #檢查內容
        checkBusinessNumber()                                    #確認營業人統編
        checkMonthSlot()                                                 #調整期別格式,確認期別
        checkInvoiceWord()                                             #確認字軌
        checkNumber{                                                        #確認發票數字
            strTOint()
        }
        checkRepeat{                                                          #確認重複
            strAdd()
        }
    }
    upLoadInvoiceNumber{                                       #寫入資料庫
        getTime()
        strAdd()     
    }                            
}
```
```
class InvoiceNumberView( ModelView ):
#秀資料庫發票號碼
    datamodel                     #MongoEngineInterface物件 指定要使用的資料庫collection
    label_columns             #資料庫欄位所要對應顯示的名稱
    list_columns                 #要顯示的資料庫欄位
    list_title                          # list 的標題

    muldelete( items ):
    # 刪除所勾選的資料欄位 , 如果 item.status 為'0'才刪除
        items = 頁面上所句選的物件
        return 刷新頁面

    changeDel(item, myTime ,count ):
    #刪除發票號碼  實則更改資料庫資料
      item.status 更改為2
      item.invoiceNumber 更改為('MM'+'DD' + char(時) + char(分) + char(秒) + count(3位數)
        item = 要刪除的物件
        myTime = datetime.utcnow()
        count = 整數第幾筆資料
```
```
class YuTsaiLprCashJournalView(ModelView):
#秀CashJournal,下載資料
    datamodel                     #MongoEngineInterface物件 指定要使用的資料庫collection
    excel_col0                      #Excel 所需要的所有列表, 目前是依序寫入的
    label_columns             #資料庫欄位所要對應顯示的名稱
    search_columns         #收尋要顯示的資料庫欄位
    list_columns                # list 要顯示的資料庫欄位
    extra_args = {               # 一個字典 , 要傳到自訂頁面的參數
        'cashTatol': 0,                 #總現金
        'receivableTatol': 0,     #總應收
        'discountTatol': 0         #總折扣
    }
    list_title                         # list 的標題
    base_permissions    # 該頁面的權限(由程式設定時 即使是admin 也無法開啟未允許的權限)
    list_widget                  # 設定自己的 widget
    list_template             # 設定自己的 html
    nowListData              #目前 list 上的資料

    list():
    #/list/ 會呼叫的程式
    return 要傳給cline 端的資料

    getMoney():
    #統計金額

    downloadExcel():
    # 按download 會執行的程式
        return response #下載的文件

    checkMonth(month):
    #確認是哪個期別(年+月)
        month = 字串, 201801
        return invoiceMonth  #字串陣列 , 所屬期別  EX: 201801 ,201802

    monthAdd(month , i):
    #期別(年+月)加減(對月份加減),期別不跨年,不考慮年份
        month = 字串, 201801'
        i = 整數,通常是1,-1
        return #字串 , EX: 201802

    countTax(cash):
    #計算稅額
        return dictMoney #字典,['priceOfGoods'] =  金額
                                                        ,['tax'] = 稅額
                                                        ,['cash'] = 總金額

    myRound( number):
    #四捨五入到小數第二位
        number = float
        return #float ,到小數第二位

    getNumber(str):
    # 取出字串裡的數字  如果非數字會剔除
        str = 字串
        return number #字串

    setFont(name, height = 220 ,color_index = 4, bold = False ):
    #設定自己的字形
        return #XFStyle() 物件

執行流程:
進入該頁面
list{
    getMoney():
}

按download
downloadExcel{
    myExcel = Workbook()                                                   #建立Excel檔
    myStyle = setFont()                                                         #建立字形
    sheet1 = myExcel.add_sheet()                                   #建立分頁
    for index ,content in enumerate( self.excel_col0 ) :   #將第一行標題寫入
        sheet1.write(0, index, content,myStyle)
    for item in  self.nowListData :
        # 寫入其他內容
        ...
        strTime = getNumber()
        # ...
        money = countTax{                                                    #計算稅額
            myRound()
        }
        # ...
        invoiceMonth = checkMonth{                                #符合格式的期別
            monthAdd()
        }
        # ...
}
```
```
class SpecailDayView(ModelView):
#特別日
    datamodel                            #MongoEngineInterface物件 指定要使用的資料庫collection
    label_columns                    #資料庫欄位所要對應顯示的名稱
    search_columns                #收尋要顯示的資料庫欄位
    list_columns                       # list 要顯示的資料庫欄位
    add_form                             # 設定自己的 add 表單
    base_permissions            # 該頁面的權限
    list_title                                # list 的標題

    _add():
    # 按 ＋ 會呼叫到此函數,  複寫原物件的方法  加入檢查日期
```
```
class MonthlyCarPlateTextView(ModelView):
#月租車
    datamodel                            #MongoEngineInterface物件 指定要使用的資料庫collection
    label_columns                    #資料庫欄位所要對應顯示的名稱
    search_columns                #收尋要顯示的資料庫欄位
    list_columns                       # list 要顯示的資料庫欄位
    add_form                             # 設定自己的 add 表單
    base_permissions            # 該頁面的權限
    list_title                                # list 的標題

    _add():
      # 按 ＋ 會呼叫到此函數,  複寫原物件的方法 ,加入檢查車牌 ,檢查日期

    checkItemTime( item):
    #檢查時間 , 將時間改成符合資料庫的格式
        return boolean
```
```
class DayFinancialStatementsView(YuChartsView):
#日報表
    charData                              #圖表的陣列在YuChartsView 已有宣告
                                                      [[a1,b1,c1],[a2,b2,c2]]
                                                      第一層 為一筆資料, 第二層 a 為橫軸  ,其他為縱軸數值
    form                                       # 設定自己的表單
    form_title                            # 設定自己的表單標題
    form_template                  # 設定自己的頁面
    chart_title                            # 設定自己的圖形標題
    extra_args ={                       # 一個字典 , 要傳到自訂頁面的參數
        'cashTatol': 0,                 #總金額
        'receivableTatol': 0,     #總應收
        'discountTatol': 0 ,        #總折扣
        'carsTatol': 0,                  #總車次
        'day':'0',                            #日期
        'invoiceNumber':[0,0]    #發票起訖
    }
    label_columns                    #資料庫欄位所要對應顯示的名稱
    definitions = [                      # 圖形的欄位
    {
        'label': 'Day',                    #圖形的名稱
        'group': 'day',                  #長條圖橫軸
        'series': ['receivableTatol','discountTatol','cashTatol']            #長條圖資料欄位
    }]

    updateData():
    #更新圖形資料

    form_get(form):#當進入該頁面時, 會呼叫到此函式
    #檢查是否為第一次進入,如果為第一次設定查詢昨天

    form_post(form):#當 按下check 提交表單 會執行到此程式
    #將查詢日期設定成表單所選的日期

    searchDateTime(day):
    # 設定搜尋的區間  9:00 - 9:00
        day = 字串 ,要查詢的日期 EX: 2018-10-23
        return startTime, endTime #起始時間,結束時間  同資料庫格式

    statistics(day):
    #統計該天資料
        day = 字串 ,要查詢的日期 EX: 2018-10-23

執行流程:
form_get{
    statistics{                             #依日期更新資料
        searchDateTime()        #設定搜尋期間
    }
}

form_post{
  statistics{                             #依日期更新資料
      searchDateTime()        #設定搜尋期間
  }
}
```
```
class TrafficFlowView(YuChartsView):
#車流量統計
    charData                              #圖表的陣列在YuChartsView 已有宣告
                                                      [[a1,b1,c1],[a2,b2,c2]]
                                                      第一層 為一筆資料, 第二層 a 為橫軸  ,其他為縱軸數值
    form                                       # 設定自己的表單
    form_title                            # 設定自己的表單標題
    form_template                  # 設定自己的頁面
    chart_title                            # 設定自己的圖形標題
    extra_args = {                      # 一個字典 , 要傳到自訂頁面的參數
        'countCar': 0 }                #總車數
    label_columns                  #資料庫欄位所要對應顯示的名稱
    definitions = [{                  # 圖形的欄位
        'label': 'Day',                  # 圖形的名稱
        'group': 'hour',              # 圖形橫軸
        'series': ['cars']              # 圖形長條圖資料欄位 縱軸
    }]
    inTatolCar                           # 入場車統計
    outTatolCar                        # 出場車統計
    choseChart                        # 顯示進出場選擇  IN = 進場  OUT = 出場
    startDay = ''                       # 起始日期
    endDay = ''                        # 結束日期

    updateData(self):
    #更新資料


    form_get(self, form):#當進入該頁面時, 會呼叫到此函式
    #檢查是否為第一次進入,如果為第一次設定查詢昨天

    checkChoseChart(chose):
    # 點選 切換 進出場圖示
        chose = 字串, 'IN' |  'OUT'
        return 重整頁面

    initTatolCar():
    #初始化資料   extra_args['countCar'] , inTatolCar  ,outTatolCar

    form_post(self, form):#當 按下check 提交表單 會執行到此程式
    #將查詢日期設定成表單所選的日期

    statistics():
    #統計資料

執行流程:
form_get{
  如果是第一次 設定日期為昨天
    updateData{                         #更新資料
        initTatolCar()                    #重置資料
        statistics()                          #統計資料
    }
}

form_post{                                 #按check
    更新查詢日期
    updateData{                         #更新資料
        initTatolCar()                    #重置資料
        statistics()                          #統計資料
    }
}

checkChoseChart{                 #按 in car | out car
    # 更新choseChart
    # 重整頁面
}
```
