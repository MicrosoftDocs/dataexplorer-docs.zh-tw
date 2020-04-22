---
title: 範例 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的範例。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 746017d41b5f1a13ce73f2c27df9cac5b5982ff0
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663582"
---
# <a name="samples"></a>範例

下面是一些常見的查詢需求,以及如何使用 Kusto 查詢語言來滿足它們。

## <a name="display-a-column-chart"></a>顯示柱圖

投影兩列或兩列以上,並將其用作圖表的 x 軸和 y 軸:

```kusto 
StormEvents
| where isnotempty(EndLocation) 
| summarize event_count=count() by EndLocation
| top 10 by event_count
| render columnchart
```

* 第一列形成 x 軸。 它可以是數位、日期時間或字串。 
* 使用`where``summarize`和`top`來限制顯示的數據量。
* 對結果進行排序,以定義 x 軸的順序。

:::image type="content" source="images/samples/060.png" alt-text="060":::

<a name="activities"></a>
## <a name="get-sessions-from-start-and-stop-events"></a>從開始和停止事件取得工作階段

假設我們有一事件記錄檔，其中某些事件標記了擴充的活動或工作階段的開始或結束。 

|名稱|City|SessionId|時間戳記|
|---|---|---|---|
|Start|London|2817330|2015-12-09T10:12:02.32|
|遊戲|London|2817330|2015-12-09T10:12:52.45|
|Start|曼徹斯特|4267667|2015-12-09T10:14:02.23|
|Stop|London|2817330|2015-12-09T10:23:43.18|
|取消|曼徹斯特|4267667|2015-12-09T10:27:26.29|
|Stop|曼徹斯特|4267667|2015-12-09T10:28:31.72|

每個事件都有 SessionId，因此符合開始和停止事件的問題就會有相同的識別碼。

```kusto
let Events = MyLogTable | where ... ;

Events
| where Name == "Start"
| project Name, City, SessionId, StartTime=timestamp
| join (Events 
        | where Name="Stop"
        | project StopTime=timestamp, SessionId) 
    on SessionId
| project City, SessionId, StartTime, StopTime, Duration = StopTime - StartTime
```

用於[`let`](./letstatement.md)命名在加入之前盡可能縮減的表投影。
[`Project`](./projectoperator.md)用於更改時間戳的名稱,以便開始時間和停止時間都可以顯示在結果中。 它也會選取我們要在結果中看到的其他資料行。 [`join`](./joinoperator.md)匹配同一活動的開始和停止條目,為每個活動創建一行。 最後， `project` 會再次加入資料行以顯示活動的持續時間。


|City|SessionId|StartTime|StopTime|Duration|
|---|---|---|---|---|
|London|2817330|2015-12-09T10:12:02.32|2015-12-09T10:23:43.18|00:11:40.46|
|曼徹斯特|4267667|2015-12-09T10:14:02.23|2015-12-09T10:28:31.72|00:14:29.49|

### <a name="get-sessions-without-session-id"></a>取得工作階段 (不使用工作階段識別碼)

現在，讓我們假設開始與結束事件沒有方便我們配對的工作階段識別碼。 但我們有工作階段執行位置的用戶端 IP 位址。 假設每個用戶端位址一次只會產生一個工作階段，我們可以將來自相同 IP 位址的每個開始事件與下一個結束事件配對。

```kusto
Events 
| where Name == "Start" 
| project City, ClientIp, StartTime = timestamp
| join  kind=inner
    (Events
    | where Name == "Stop" 
    | project StopTime = timestamp, ClientIp)
    on ClientIp
| extend duration = StopTime - StartTime 
    // Remove matches with earlier stops:
| where  duration > 0  
    // Pick out the earliest stop for each start and client:
| summarize arg_min(duration, *) by bin(StartTime,1s), ClientIp
```

聯結會將來自相同用戶端 IP 位址的開始時間與所有的停止時間配對。 因此我們先移除與較早停止時間的配對。

然後以開始時間和 IP 來分組，來取得每個工作階段的群組。 我們必須為 StartTime 參數提供`bin`一個 函數:如果我們不這樣做,Kusto 將自動使用 1 小時 bin,這將匹配一些開始時間與錯誤的停止時間。

`arg_min`在每個組中選擇持續時間最小的行,並且`*`參數通過所有其他列,儘管它為每個列名稱加上"min_"的首碼。 

:::image type="content" source="images/samples/040.png" alt-text="040"::: 

然後,我們可以添加一些代碼來計算大小方便的條柱中的持續時間。我們對條形圖稍加偏好,因此我們除以將`1s`時間跨度轉換為數位。 


      // Count the frequency of each duration:
    | summarize count() by duration=bin(min_duration/1s, 10) 
      // Cut off the long tail:
    | where duration < 300
      // Display in a bar chart:
    | sort by duration asc | render barchart 

:::image type="content" source="images/samples/050.png" alt-text="050":::

### <a name="real-example"></a>真實範例

```kusto
Logs  
| filter ActivityId == "ActivityId with Blablabla" 
| summarize max(Timestamp), min(Timestamp)  
| extend Duration = max_Timestamp - min_Timestamp 
 
wabitrace  
| filter Timestamp >= datetime(2015-01-12 11:00:00Z)  
| filter Timestamp < datetime(2015-01-12 13:00:00Z)  
| filter EventText like "NotifyHadoopApplicationJobPerformanceCounters"      
| extend Tenant = extract("tenantName=([^,]+),", 1, EventText) 
| extend Environment = extract("environmentName=([^,]+),", 1, EventText)  
| extend UnitOfWorkId = extract("unitOfWorkId=([^,]+),", 1, EventText)  
| extend TotalLaunchedMaps = extract("totalLaunchedMaps=([^,]+),", 1, EventText, typeof(real))  
| extend MapsSeconds = extract("mapsMilliseconds=([^,]+),", 1, EventText, typeof(real)) / 1000 
| extend TotalMapsSeconds = MapsSeconds  / TotalLaunchedMaps 
| filter Tenant == 'DevDiv' and Environment == 'RollupDev2'  
| filter TotalLaunchedMaps > 0 
| summarize sum(TotalMapsSeconds) by UnitOfWorkId  
| extend JobMapsSeconds = sum_TotalMapsSeconds * 1 
| project UnitOfWorkId, JobMapsSeconds 
| join ( 
wabitrace  
| filter Timestamp >= datetime(2015-01-12 11:00:00Z)  
| filter Timestamp < datetime(2015-01-12 13:00:00Z)  
| filter EventText like "NotifyHadoopApplicationJobPerformanceCounters"  
| extend Tenant = extract("tenantName=([^,]+),", 1, EventText) 
| extend Environment = extract("environmentName=([^,]+),", 1, EventText)  
| extend UnitOfWorkId = extract("unitOfWorkId=([^,]+),", 1, EventText)   
| extend TotalLaunchedReducers = extract("totalLaunchedReducers=([^,]+),", 1, EventText, typeof(real)) 
| extend ReducesSeconds = extract("reducesMilliseconds=([^,]+)", 1, EventText, typeof(real)) / 1000 
| extend TotalReducesSeconds = ReducesSeconds / TotalLaunchedReducers 
| filter Tenant == 'DevDiv' and Environment == 'RollupDev2'  
| filter TotalLaunchedReducers > 0 
| summarize sum(TotalReducesSeconds) by UnitOfWorkId  
| extend JobReducesSeconds = sum_TotalReducesSeconds * 1 
| project UnitOfWorkId, JobReducesSeconds ) 
on UnitOfWorkId 
| join ( 
wabitrace  
| filter Timestamp >= datetime(2015-01-12 11:00:00Z)  
| filter Timestamp < datetime(2015-01-12 13:00:00Z)  
| filter EventText like "NotifyHadoopApplicationJobPerformanceCounters"  
| extend Tenant = extract("tenantName=([^,]+),", 1, EventText) 
| extend Environment = extract("environmentName=([^,]+),", 1, EventText)  
| extend JobName = extract("jobName=([^,]+),", 1, EventText)  
| extend StepName = extract("stepName=([^,]+),", 1, EventText)  
| extend UnitOfWorkId = extract("unitOfWorkId=([^,]+),", 1, EventText)  
| extend LaunchTime = extract("launchTime=([^,]+),", 1, EventText, typeof(datetime))  
| extend FinishTime = extract("finishTime=([^,]+),", 1, EventText, typeof(datetime)) 
| extend TotalLaunchedMaps = extract("totalLaunchedMaps=([^,]+),", 1, EventText, typeof(real))  
| extend TotalLaunchedReducers = extract("totalLaunchedReducers=([^,]+),", 1, EventText, typeof(real)) 
| extend MapsSeconds = extract("mapsMilliseconds=([^,]+),", 1, EventText, typeof(real)) / 1000 
| extend ReducesSeconds = extract("reducesMilliseconds=([^,]+)", 1, EventText, typeof(real)) / 1000 
| extend TotalMapsSeconds = MapsSeconds  / TotalLaunchedMaps  
| extend TotalReducesSeconds = (ReducesSeconds / TotalLaunchedReducers / ReducesSeconds) * ReducesSeconds  
| extend CalculatedDuration = (TotalMapsSeconds + TotalReducesSeconds) * time(1s) 
| filter Tenant == 'DevDiv' and Environment == 'RollupDev2') 
on UnitOfWorkId 
| extend MapsFactor = TotalMapsSeconds / JobMapsSeconds 
| extend ReducesFactor = TotalReducesSeconds / JobReducesSeconds 
| extend CurrentLoad = 1536 + (768 * TotalLaunchedMaps) + (1536 * TotalLaunchedMaps) 
| extend NormalizedLoad = 1536 + (768 * TotalLaunchedMaps * MapsFactor) + (1536 * TotalLaunchedMaps * ReducesFactor) 
| summarize sum(CurrentLoad), sum(NormalizedLoad) by  JobName  
| extend SaveFactor = sum_NormalizedLoad / sum_CurrentLoad 
```

<a name="concurrent-activities"><a/>
## <a name="chart-concurrent-sessions-over-time"></a>一段時間的並行工作階段圖表

假設我們有含有活動開始及結束時間的活動資料表。  我們要查看一段時間的圖表，該圖表顯示在任意時間中有多少活動正在同時執行。

以下是我們將會呼叫 `X`的範例輸入：

|SessionId | StartTime | StopTime |
|---|---|---|
| a | 10:01:03 | 10:10:08 |
| b | 10:01:29 | 10:03:10 |
| c | 10:03:02 | 10:05:20 |

我們想要一個 1 分鐘量化的圖表，所以我們想要建立一些以 1 分鐘為間隔單位，可以為每個執行中活動執行計算的項目。

以下是中繼結果︰

```kusto
X | extend samples = range(bin(StartTime, 1m), StopTime, 1m)
```

`range` 會使用指定之間隔產生值的陣列：

|SessionId | StartTime | StopTime  | 範例|
|---|---|---|---|
| a | 10:01:33 | 10:06:31 | [10:01:00,10:02:00,...10:06:00]|
| b | 10:02:29 | 10:03:45 | [10:02:00,10:03:00]|
| c | 10:03:12 | 10:04:30 | [10:03:00,10:04:00]|

但是,我們將使用[mv-展開](./mvexpandoperator.md)來擴展這些陣列,而不是保留這些陣列:

```kusto
X | mv-expand samples = range(bin(StartTime, 1m), StopTime , 1m)
```

|SessionId | StartTime | StopTime  | 範例|
|---|---|---|---|
| a | 10:01:33 | 10:06:31 | 10:01:00|
| a | 10:01:33 | 10:06:31 | 10:02:00|
| a | 10:01:33 | 10:06:31 | 10:03:00|
| a | 10:01:33 | 10:06:31 | 10:04:00|
| a | 10:01:33 | 10:06:31 | 10:05:00|
| a | 10:01:33 | 10:06:31 | 10:06:00|
| b | 10:02:29 | 10:03:45 | 10:02:00|
| b | 10:02:29 | 10:03:45 | 10:03:00|
| c | 10:03:12 | 10:04:30 | 10:03:00|
| c | 10:03:12 | 10:04:30 | 10:04:00|

現在可以將這些資料以取樣時間群組，來計算每個活動的發生次數：

```kusto
X
| mv-expand samples = range(bin(StartTime, 1m), StopTime , 1m)
| summarize count(SessionId) by bin(todatetime(samples),1m)
```

* 我們需要datetime(),因為[mv-expand](./mvexpandoperator.md)生成動態類型的列。
* 我們需要 bin()，因為對於數值和日期而言，如果未提供 bin 函式，summarize 一律會套用具有預設間隔的 bin 函式。 


| count_SessionId | 範例|
|---|---|
| 1 | 10:01:00|
| 2 | 10:02:00|
| 3 | 10:03:00|
| 2 | 10:04:00|
| 1 | 10:05:00|
| 1 | 10:06:00|

這可以轉譯為橫條圖或時間圖表。

## <a name="introduce-null-bins-into-summarize"></a>在匯總中引入空條柱

當運算元`summarize`應用於由`datetime`列組成的組鍵時,通常將這些值"bin"到固定寬度的條柱。例如:

```kusto
let StartTime=ago(12h);
let StopTime=now()
T
| where Timestamp > StartTime and Timestamp <= StopTime 
| where ...
| summarize Count=count() by bin(Timestamp, 5m)
```

此操作將生成一個表`T`,其中每組行有一行,每個行將放入五分鐘的條柱中。 它不做的是添加「空條柱」-時間條柱值之間的`StartTime`行`StopTime`, 其中沒有相應的`T`行。 

通常,需要用這些條箱"填充"表。以下是一種方法:

```kusto
let StartTime=ago(12h);
let StopTime=now()
T
| where Timestamp > StartTime and Timestamp <= StopTime 
| summarize Count=count() by bin(Timestamp, 5m)
| where ...
| union ( // 1
  range x from 1 to 1 step 1 // 2
  | mv-expand Timestamp=range(StartTime, StopTime, 5m) to typeof(datetime) // 3
  | extend Count=0 // 4
  )
| summarize Count=sum(Count) by bin(Timestamp, 5m) // 5 
```

以下是上述查詢的分步說明: 

1. 使用運算子`union`允許我們向表添加其他行。 這些行由表達式`union`生成。
2. 使用`range`運算子生成具有單個行/列的表。
   除了用於`mv-expand`工作之外,該表不用於任何其他用途。
3. 在`mv-expand``range`函數上使用運算符創建與 和`StartTime`之間 有 5 分鐘`EndTime`條柱一樣 多的行。
4. 全部帶有`Count``0`一個 。
5. 最後,`summarize`我們使用運算子將從原始(左或外部)參數到`union`和從內部參數到它的條柱(即空 bin 行)的分組在一起。 這可確保輸出每個條柱有一行,其值為零或原始計數。  

## <a name="get-more-out-of-your-data-in-kusto-using-machine-learning"></a>使用機器學習從庫斯托的資料中取得更多 

利用機器學習演演演算法並從遙測數據中獲取有趣的見解有許多有趣的用例。 雖然這些演演演算法通常需要一個非常結構化的數據集作為輸入,但原始日誌數據通常與所需的結構和大小不匹配。 

我們的旅程從查找特定必應推理服務的錯誤率異常開始。 Logs 表具有 65B 記錄,下面的簡單查詢篩選了 250K 錯誤,並創建了利用異常檢測功能[series_decompose_anomalies](series-decompose-anomaliesfunction.md)的錯誤計數時間序列數據。 異常由 Kusto 服務檢測到,並在時間序列圖表上作為紅點突出顯示。

```kusto
Logs
| where Timestamp >= datetime(2015-08-22) and Timestamp < datetime(2015-08-23) 
| where Level == "e" and Service == "Inferences.UnusualEvents_Main" 
| summarize count() by bin(Timestamp, 5min)
| render anomalychart 
```

該服務識別了少數具有可疑錯誤率的時間存儲桶。 我使用 Kusto 放大此時間範圍,運行一個查詢,該查詢在"消息"列上聚合,試圖查找頂部錯誤。 我已經從郵件的整個堆疊跟蹤中修剪了相關部分,以便更好地適應頁面。 您可以看到,我在前八個錯誤中取得了不錯的成功,但隨後到達了錯誤的長尾,因為錯誤消息是由包含更改數據的格式字串創建的。 

```kusto
Logs
| where Timestamp >= datetime(2015-08-22 05:00) and Timestamp < datetime(2015-08-22 06:00)
| where Level == "e" and Service == "Inferences.UnusualEvents_Main"
| summarize count() by Message 
| top 10 by count_ 
| project count_, Message 
```

|count_|訊息
|---|---
|7125|方法「執行週期從暫存資料」的執行演演算法方法失敗...
|7125|推理主機服務調用失敗。系統.NullreferenceException:物件引用未設定為物件的實例...
|7124|意外推理系統錯誤。系統.NullreferenceException:物件引用未設定為物件的實例... 
|5112|意外推理系統錯誤。系統.NullreferenceException:物件引用未設置為物件的實例。
|174|推理主機服務調用失敗..系統.服務模型.通信異常:寫入管道時出錯:...
|10|方法「執行週期從暫存資料」的執行演演算法方法失敗...
|10|推理系統錯誤。微軟.Bing.Platform.推理.服務.經理.用戶臨時數據管理器例外:...
|3|推理主機服務調用失敗..系統.服務模型.通信物件故障異常:...
|1|推理系統錯誤...社交圖.BOSS.操作回應...AIS TraceId:8292FC561AC64BED8FA243808FE74EFD...
|1|推理系統錯誤...社交圖.BOSS.操作回應...AIS 追蹤 Id: 5F79F7587FF943EC9B641E02E701AFBF...

這是`reduce`操作員提供説明的地方。 操作員`reduce`識別了代碼中同一跟蹤檢測點引起的 63 個不同的錯誤,並幫助我專注於該時間視窗中的其他有意義的錯誤跟蹤。

```kusto
Logs
| where Timestamp >= datetime(2015-08-22 05:00) and Timestamp < datetime(2015-08-22 06:00)
| where Level == "e" and Service == "Inferences.UnusualEvents_Main"
| reduce by Message with threshold=0.35
| project Count, Pattern
```

|Count|模式
|---|---
|7125|方法「執行週期從暫存資料」的執行演演算法方法失敗...
|  7125|推理主機服務調用失敗。系統.NullreferenceException:物件引用未設定為物件的實例...
|  7124|意外推理系統錯誤。系統.NullreferenceException:物件引用未設定為物件的實例...
|  5112|意外推理系統錯誤。系統.NullreferenceException:物件引用未設置為物件的實例。
|  174|推理主機服務調用失敗..系統.服務模型.通信異常:寫入管道時出錯:...
|  63|推理系統錯誤。微軟.Bing.Platform.推理。\*:寫入\*寫入物件 BOSS。\*:社交圖.BOSS.Reques...
|  10|方法「執行週期從暫存資料」的執行演演算法方法失敗...
|  10|推理系統錯誤。微軟.Bing.Platform.推理.服務.經理.用戶臨時數據管理器例外:...
|  3|推理主機服務調用失敗。系統.服務模式。\*:物件,系統.服務模式.通道。\* + \* ,因為\*\*是\*...  在系統...

現在,我已對導致檢測到的異常導致的頂級錯誤有了很好的瞭解,我想瞭解這些錯誤對我整個系統的影響。 "日誌"表包含其他維度數據,如"元件"、"群集"等。新的"自動群集"外掛程式可以説明我通過簡單的查詢獲得這種見解。 在下面的示例中,我可以清楚地看到,前四個錯誤都特定於一個元件,而前三個錯誤特定於 DB4 群集,但第四個錯誤發生在所有群集中。

```kusto
Logs
| where Timestamp >= datetime(2015-08-22 05:00) and Timestamp < datetime(2015-08-22 06:00)
| where Level == "e" and Service == "Inferences.UnusualEvents_Main"
| evaluate autocluster()
```

|Count |百分比 (%)|元件|叢集|訊息
|---|---|---|---|---
|7125|26.64|推理主機服務|DB4|方法執行演演算法方法 ...
|7125|26.64|未知元件|DB4|推理主機服務呼叫失敗...
|7124|26.64|推理演算法執行器|DB4|意外推理系統錯誤...
|5112|19.11|推理演算法執行器|*|意外推理系統錯誤... 

## <a name="mapping-values-from-one-set-to-another"></a>將值從一個集映射到另一個集

常見的用例是使用值的靜態映射,這些值有助於將結果採用到更可呈現的方式。  
例如,請考慮使用下一個表。 DeviceModel 指定設備的模型,它不是一種非常方便的引用設備名稱的形式。  


|DeviceModel |Count 
|---|---
|iPhone5,1 |32 
|iPhone3,2 |432 
|iPhone7,2 |55 
|iPhone5,2 |66 

  
更好的表示形式可能是:  

|FriendlyName |Count 
|---|---
|iPhone 5 |32 
|iPhone 4 |432 
|iPhone 6 |55 
|iPhone5 |66 

以下兩種方法說明瞭如何實現這一目標。  

### <a name="mapping-using-dynamic-dictionary"></a>使用動態字典進行對應

下面的方法顯示了如何使用動態字典和動態訪問器實現映射。

```kusto
// Data set definition
let Source = datatable(DeviceModel:string, Count:long)
[
  'iPhone5,1', 32,
  'iPhone3,2', 432,
  'iPhone7,2', 55,
  'iPhone5,2', 66,
];
// Query start here
let phone_mapping = dynamic(
  {
    "iPhone5,1" : "iPhone 5",
    "iPhone3,2" : "iPhone 4",
    "iPhone7,2" : "iPhone 6",
    "iPhone5,2" : "iPhone5"
  });
Source 
| project FriendlyName = phone_mapping[DeviceModel], Count
```

|FriendlyName|Count|
|---|---|
|iPhone 5|32|
|iPhone 4|432|
|iPhone 6|55|
|iPhone5|66|



### <a name="mapping-using-static-table"></a>使用靜態表對應

下面的方法顯示了如何使用持久表和聯接運算符實現映射。
 
建立映射表(僅一次):

```kusto
.create table Devices (DeviceModel: string, FriendlyName: string) 

.ingest inline into table Devices 
    ["iPhone5,1","iPhone 5"]["iPhone3,2","iPhone 4"]["iPhone7,2","iPhone 6"]["iPhone5,2","iPhone5"]
```

裝置內容:

|DeviceModel |FriendlyName 
|---|---
|iPhone5,1 |iPhone 5 
|iPhone3,2 |iPhone 4 
|iPhone7,2 |iPhone 6 
|iPhone5,2 |iPhone5 


建立測試表相同的技巧 來源:

```kusto
.create table Source (DeviceModel: string, Count: int)

.ingest inline into table Source ["iPhone5,1",32]["iPhone3,2",432]["iPhone7,2",55]["iPhone5,2",66]
```


加入與專案:

```kusto
Devices  
| join (Source) on DeviceModel  
| project FriendlyName, Count
```

結果：

|FriendlyName |Count 
|---|---
|iPhone 5 |32 
|iPhone 4 |432 
|iPhone 6 |55 
|iPhone5 |66 


## <a name="creating-and-using-query-time-dimension-tables"></a>建立與使用查詢時間維度表

在許多情況下,希望將查詢結果與一些未存儲在資料庫中的臨時維度表聯接。 可以通過執行類似操作來定義其結果是限定為單個查詢的表的表示式:

```kusto
// Create a query-time dimension table using datatable
let DimTable = datatable(EventType:string, Code:string)
  [
    "Heavy Rain", "HR",
    "Tornado",    "T"
  ]
;
DimTable
| join StormEvents on EventType
| summarize count() by Code
```

下面是一個稍微複雜一些的例子:

```kusto
// Create a query-time dimension table using datatable
let TeamFoundationJobResult = datatable(Result:int, ResultString:string)
  [
    -1, 'None', 0, 'Succeeded', 1, 'PartiallySucceeded', 2, 'Failed',
    3, 'Stopped', 4, 'Killed', 5, 'Blocked', 6, 'ExtensionNotFound',
    7, 'Inactive', 8, 'Disabled', 9, 'JobInitializationError'
  ]
;
JobHistory
  | where PreciseTimeStamp > ago(1h)
  | where Service  != "AX"
  | where Plugin has "Analytics"
  | sort by PreciseTimeStamp desc
  | join kind=leftouter TeamFoundationJobResult on Result
  | extend ExecutionTimeSpan = totimespan(ExecutionTime)
  | project JobName, StartTime, ExecutionTimeSpan, ResultString, ResultMessage
```

## <a name="retrieving-the-latest-by-timestamp-records-per-identity"></a>檢索每個識別的最新(按時間戳)記錄

假設您有一個表,其中包含一`id`列(標識與每行關聯的實體,如使用者 ID 或 Node`timestamp`ID)和一列 (提供行的時間參考)以及其他列。 您的目標是編寫一個查詢,該查詢返回`id`列的每個值的最新 2 條記錄,其中"最新"定義為"具有最高`timestamp`值"。

這可以使用[頂部嵌套運算符](topnestedoperator.md)來完成。
首先,我們提供查詢,然後解釋它:

```kusto
datatable(id:string, timestamp:datetime, bla:string)           // (1)
  [
  "Barak",  datetime(2015-01-01), "1",
  "Barak",  datetime(2016-01-01), "2",
  "Barak",  datetime(2017-01-20), "3",
  "Donald", datetime(2017-01-20), "4",
  "Donald", datetime(2017-01-18), "5",
  "Donald", datetime(2017-01-19), "6"
  ]
| top-nested   of id        by dummy0=max(1),                  // (2)
  top-nested 2 of timestamp by dummy1=max(timestamp),          // (3)
  top-nested   of bla       by dummy2=max(1)                   // (4)
| project-away dummy0, dummy1, dummy2                          // (5)
```

注意
1. 只是`datatable`一種生成一些測試數據用於演示的方法。 在現實中,當然,你會有數據在這裡。
2. 此行實質上的意思是"返回`id`的所有不同值。
3. 然後,對於最大化`timestamp`列的前 2 條記錄、上一級別的列(此`id`處,只是 )和在此級別指定的列(此處,`timestamp`此處)。 傳回。
4. 此行為上一級別返回的每個`bla`記錄添加列的值。 如果表具有其他感興趣的列,則對於每個此類列,都會重複此行。
5. 最後,我們使用[項目離開運算符](projectawayoperator.md)刪除`top-nested`引的"額外"列。

## <a name="extending-a-table-with-some-percent-of-total-calculation"></a>使用總計算的百分比擴充表

通常,當一個人具有包含數位列的表格表達式時,需要將該列及其值與總值一起呈現給使用者。 例如,假設存在一些查詢,其值如下表:

|一些系列|薩默因特|
|----------|-------|
|Foo       |    100|
|長條圖       |    200|

您希望將此表顯示為:

|一些系列|薩默因特|Pct |
|----------|-------|----|
|Foo       |    100|33.3|
|長條圖       |    200|66.6|

為此,需要計算`SomeInt`列的總計(總和),然後將該列的每個值除以總計。 使用 為[運算子](asoperator.md)為這些結果指定一個名稱,可以執行此操作以執行任意結果:

```kusto
// The following table literal represents a long calculation
// that ends up with an anonymous tabular value:
datatable (SomeInt:int, SomeSeries:string) [
  100, "Foo",
  200, "Bar",
]
// We now give this calculation a name ("X"):
| as X
// Having this name we can refer to it in the sub-expression
// "X | summarize sum(SomeInt)":
| extend Pct = 100 * bin(todouble(SomeInt) / toscalar(X | summarize sum(SomeInt)), 0.001)
```

## <a name="performing-aggregations-over-a-sliding-window"></a>在滑動視窗執行聚合
下面的範例展示如何使用滑動視窗匯總列。 例如,讓我們以下表為例,該表包含按時間戳計算水果價格。 假設我們要使用 7 天的滑動視窗計算每個水果每天的最小、最大和總和成本。 換句話說,結果集中的每個記錄聚合過去 7 天,並且結果在分析期間包含每天的記錄。  

水果表: 

|時間戳記|水果|Price|
|---|---|---|
|2018-09-24 21:00:00.0000000|香蕉|3|
|2018-09-25 20:00:00.0000000|蘋果|9|
|2018-09-26 03:00:00.0000000|香蕉|4|
|2018-09-27 10:00:00.0000000|李子|8|
|2018-09-28 07:00:00.0000000|香蕉|6|
|2018-09-29 21:00:00.0000000|香蕉|8|
|2018-09-30 01:00:00.0000000|李子|2|
|2018-10-01 05:00:00.0000000|香蕉|0|
|2018-10-02 02:00:00.0000000|香蕉|0|
|2018-10-03 13:00:00.0000000|李子|4|
|2018-10-04 14:00:00.0000000|蘋果|8|
|2018-10-05 05:00:00.0000000|香蕉|2|
|2018-10-06 08:00:00.0000000|李子|8|
|2018-10-07 12:00:00.0000000|香蕉|0|

滑動視窗聚合查詢(說明在查詢結果下方提供): 

```kusto
let _start = datetime(2018-09-24);
let _end = _start + 13d; 
Fruits 
| extend _bin = bin_at(Timestamp, 1d, _start) // #1 
| extend _endRange = iif(_bin + 7d > _end, _end, 
                            iff( _bin + 7d - 1d < _start, _start,
                                iff( _bin + 7d - 1d < _bin, _bin,  _bin + 7d - 1d)))  // #2
| extend _range = range(_bin, _endRange, 1d) // #3
| mv-expand _range to typeof(datetime) limit 1000000 // #4
| summarize min(Price), max(Price), sum(Price) by Timestamp=bin_at(_range, 1d, _start) ,  Fruit // #5
| where Timestamp >= _start + 7d; // #6

```

|時間戳記|水果|min_Price|max_Price|sum_Price|
|---|---|---|---|---|
|2018-10-01 00:00:00.0000000|蘋果|9|9|9|
|2018-10-01 00:00:00.0000000|香蕉|0|8|18|
|2018-10-01 00:00:00.0000000|李子|2|8|10|
|2018-10-02 00:00:00.0000000|香蕉|0|8|18|
|2018-10-02 00:00:00.0000000|李子|2|8|10|
|2018-10-03 00:00:00.0000000|李子|2|8|14|
|2018-10-03 00:00:00.0000000|香蕉|0|8|14|
|2018-10-04 00:00:00.0000000|香蕉|0|8|14|
|2018-10-04 00:00:00.0000000|李子|2|4|6|
|2018-10-04 00:00:00.0000000|蘋果|8|8|8|
|2018-10-05 00:00:00.0000000|香蕉|0|8|10|
|2018-10-05 00:00:00.0000000|李子|2|4|6|
|2018-10-05 00:00:00.0000000|蘋果|8|8|8|
|2018-10-06 00:00:00.0000000|李子|2|8|14|
|2018-10-06 00:00:00.0000000|香蕉|0|2|2|
|2018-10-06 00:00:00.0000000|蘋果|8|8|8|
|2018-10-07 00:00:00.0000000|香蕉|0|2|2|
|2018-10-07 00:00:00.0000000|李子|4|8|12|
|2018-10-07 00:00:00.0000000|蘋果|8|8|8|

查詢詳細資訊: 

查詢「拉伸」(重複)輸入表中的每條記錄在 7 天內發佈其實際外觀,以便每個記錄實際出現 7 次。 因此,每天執行聚合時,聚合包括前 7 天的所有記錄。

分步說明(數位是指查詢內聯註釋中的數位):
1. 將每個記錄綁定到 1d(相對於_start)。 
2. 確定每個記錄範圍的末尾 - _bin = 7d,除非這已出 _(開始、結束)_ 範圍,在這種情況下將進行調整。 
3. 對於每個記錄,創建 7 天(時間戳)的陣列,從當前記錄日期開始。 
4. mv-展開陣列,從而將每個記錄複製為 7 條記錄,彼此間隔 1 天。 
5. 每天執行聚合函數。 由於#4,這實際上總結了_過去_7天。 
6. 最後,由於前 7d 的數據不完整(前 7 天沒有 7d 回望期),因此我們將前 7 天排除在最終結果之外(它們僅參與 2018-10-01 年的聚合)。 

## <a name="find-preceding-event"></a>尋找上一個事件
下一個範例展示如何在 2 個資料集之間查找前面的事件。  

*目的:* 給定 2 個數據集 A 和 B,對於 B 中的每個記錄,在 A 中找到其前面的事件(換句話說,arg_max記錄在 A 中仍然比 B"舊")。 例如,下面是以下範例資料集的預期輸出: 

```kusto
let A = datatable(Timestamp:datetime, Id:string, EventA:string)
[
    datetime(2019-01-01 00:00:00), "x", "Ax1",
    datetime(2019-01-01 00:00:01), "x", "Ax2",
    datetime(2019-01-01 00:00:02), "y", "Ay1",
    datetime(2019-01-01 00:00:05), "y", "Ay2",
    datetime(2019-01-01 00:00:00), "z", "Az1"
];
let B = datatable(Timestamp:datetime, Id:string, EventB:string)
[
    datetime(2019-01-01 00:00:03), "x", "B",
    datetime(2019-01-01 00:00:04), "x", "B",
    datetime(2019-01-01 00:00:04), "y", "B",
    datetime(2019-01-01 00:02:00), "z", "B"
];
A; B
```

|時間戳記|Id|事件B|
|---|---|---|
|2019-01-01 00:00:00.0000000|x|Ax1|
|2019-01-01 00:00:00.0000000|z|阿茲1|
|2019-01-01 00:00:01.0000000|x|Ax2|
|2019-01-01 00:00:02.0000000|y|Ay1|
|2019-01-01 00:00:05.0000000|y|Ay2|

</br>

|時間戳記|Id|事件A|
|---|---|---|
|2019-01-01 00:00:03.0000000|x|B|
|2019-01-01 00:00:04.0000000|x|B|
|2019-01-01 00:00:04.0000000|y|B|
|2019-01-01 00:02:00.0000000|z|B|

預期輸出： 

|Id|時間戳記|事件B|A_Timestamp|事件A|
|---|---|---|---|---|
|x|2019-01-01 00:00:03.0000000|B|2019-01-01 00:00:01.0000000|Ax2|
|x|2019-01-01 00:00:04.0000000|B|2019-01-01 00:00:01.0000000|Ax2|
|y|2019-01-01 00:00:04.0000000|B|2019-01-01 00:00:02.0000000|Ay1|
|z|2019-01-01 00:02:00.0000000|B|2019-01-01 00:00:00.0000000|阿茲1|

對於這個問題,建議有兩種不同的方法。 您應該在特定的數據集上測試這兩個數據集,以找到最適合您的數據集(它們在不同的數據集上可能執行不同)。 

### <a name="suggestion-1"></a>建議#1:
此建議按 Id 和時間戳序列化兩個資料集,然後將所有 B 事件與其之前的`arg_max`所有 A 事件進行 分組,並選取組中的所有 As。 

```kusto
A
| extend A_Timestamp = Timestamp, Kind="A"
| union (B | extend B_Timestamp = Timestamp, Kind="B")
| order by Id, Timestamp asc 
| extend t = iff(Kind == "A" and (prev(Kind) != "A" or prev(Id) != Id), 1, 0)
| extend t = row_cumsum(t)
| summarize Timestamp=make_list(Timestamp), EventB=make_list(EventB), arg_max(A_Timestamp, EventA) by t, Id
| mv-expand Timestamp to typeof(datetime), EventB to typeof(string)
| where isnotempty(EventB)
| project-away t
```

### <a name="suggestion-2"></a>建議#2:
此建議需要定義一個最大回望週期(我們允許將 A 中的記錄與 B 進行比較多少?),然後加入 Id 上的 2 個數據集和此回望週期。 聯接生成所有可能的候選項(所有 A 記錄都早於 B 並在回望期內),然後通過arg_min(時間戳 B = 時間戳 A)篩選最接近 B 的記錄。 回望週期越小,查詢的性能就越好。 

在下面的示例中,回望期設置為 1m,因此使用 Id'z 的記錄沒有相應的"A"事件(因為它的"A"較舊 2 米)。

```kusto
let _maxLookbackPeriod = 1m;  
let _internalWindowBin = _maxLookbackPeriod / 2;
let B_events = B 
    | extend ID = new_guid()
    | extend _time = bin(Timestamp, _internalWindowBin)
    | extend _range = range(_time - _internalWindowBin, _time + _maxLookbackPeriod, _internalWindowBin) 
    | mv-expand _range to typeof(datetime) 
    | extend B_Timestamp = Timestamp, _range;
let A_events = A 
    | extend _time = bin(Timestamp, _internalWindowBin)
    | extend _range = range(_time - _internalWindowBin, _time + _maxLookbackPeriod, _internalWindowBin) 
    | mv-expand _range to typeof(datetime) 
    | extend A_Timestamp = Timestamp, _range;
B_events
    | join kind=leftouter (
        A_events
) on Id, _range
| where isnull(A_Timestamp) or (A_Timestamp <= B_Timestamp and B_Timestamp <= A_Timestamp + _maxLookbackPeriod)
| extend diff = coalesce(B_Timestamp - A_Timestamp, _maxLookbackPeriod*2)
| summarize arg_min(diff, *) by ID
| project Id, B_Timestamp, A_Timestamp, EventB, EventA
```

|Id|B_Timestamp|A_Timestamp|事件B|事件A|
|---|---|---|---|---|
|x|2019-01-01 00:00:03.0000000|2019-01-01 00:00:01.0000000|B|Ax2|
|x|2019-01-01 00:00:04.0000000|2019-01-01 00:00:01.0000000|B|Ax2|
|y|2019-01-01 00:00:04.0000000|2019-01-01 00:00:02.0000000|B|Ay1|
|z|2019-01-01 00:02:00.0000000||B||