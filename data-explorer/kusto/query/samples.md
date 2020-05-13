---
title: 範例-Azure 資料總管
description: 本文說明 Azure 資料總管中的範例。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: fe44323dabb246438f18c9ab01eec0008ad4fe97
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372974"
---
# <a name="samples"></a>範例

以下是一些常見的查詢需求，以及如何使用 Kusto 查詢語言來滿足這些需求。

## <a name="display-a-column-chart"></a>顯示直條圖

投影兩個或多個資料行，並使用它們做為圖表的 x 和 y 軸：

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto 
StormEvents
| where isnotempty(EndLocation) 
| summarize event_count=count() by EndLocation
| top 10 by event_count
| render columnchart
```

* 第一個資料行形成 X 軸。 可以是數值、日期時間或字串。 
* 使用 `where` `summarize` 和 `top` 來限制您所顯示的資料量。
* 排序結果，以定義 X 軸的順序。

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
|[取消]|曼徹斯特|4267667|2015-12-09T10:27:26.29|
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

在 [`let`](./letstatement.md) 進入聯結之前，您可以使用來命名資料表的投影。
[`Project`](./projectoperator.md)是用來變更時間戳記的名稱，讓啟動和停止時間都可以出現在結果中。 它也會選取我們要在結果中看到的其他資料行。 [`join`](./joinoperator.md)符合相同活動的開始和停止專案，為每個活動建立一個資料列。 最後， `project` 會再次加入資料行以顯示活動的持續時間。


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

然後以開始時間和 IP 來分組，來取得每個工作階段的群組。 我們必須提供 StartTime 參數的函式 `bin` ：如果不是，Kusto 會自動使用1小時的 bin，這會比對一些開始時間與錯誤的停止時間。

`arg_min`挑選每個群組中具有最小持續時間的資料列， `*` 參數會通過所有其他資料行，但它會在每個資料行名稱中加上「min_」的首碼。 

:::image type="content" source="images/samples/040.png" alt-text="040"::: 

然後，我們可以新增一些程式碼，以方便調整的 bin 來計算持續時間。橫條圖有些許的喜好設定，所以我們會分割以將 `1s` 時間範圍轉換成數位。 


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

但我們不會保留這些陣列，而是使用[mv-expand](./mvexpandoperator.md)來擴充它們：

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

* 我們需要 todatetime （），因為[mv-expand](./mvexpandoperator.md)會產生動態類型的資料行。
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

## <a name="introduce-null-bins-into-summarize"></a>將 null bin 引進摘要

當運算子套用在 `summarize` 包含資料行的群組索引鍵上時 `datetime` ，通常會將這些值「bin」到固定寬度的 bin。例如：

```kusto
let StartTime=ago(12h);
let StopTime=now()
T
| where Timestamp > StartTime and Timestamp <= StopTime 
| where ...
| summarize Count=count() by bin(Timestamp, 5m)
```

這項作業會產生資料表，其中每個資料列群組的資料列 `T` 會落在五分鐘的每個 bin 中。 而不是 `StartTime` `StopTime` 在與中沒有對應資料列的之間，新增「null bin」--time bin 值的資料列 `T` 。 

通常，您會想要「填補」包含這些 bin 的資料表。以下是其中一種方法：

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

以下是上述查詢的逐步說明： 

1. 使用 `union` 運算子可讓我們將額外的資料列加入至資料表。 運算式會將這些資料列產生到 `union` 。
2. 使用 `range` 運算子產生具有單一資料列/資料行的資料表。
   除了 for 以外，不會使用資料表 `mv-expand` 來處理。
3. 在 `mv-expand` 函式上使用運算子 `range` 來建立多個資料列，因為和之間有5分鐘的 bin `StartTime` `EndTime` 。
4. 所有具有 `Count` 的 `0` 。
5. 最後，我們會使用 `summarize` 運算子，將 bin 從 `union` 內部引數（也就是 null 的 bin 資料列）的原始（左或外部）引數群組在一起。 這可確保每個 bin 的輸出都有一個資料列，其值為零或原始計數。  

## <a name="get-more-out-of-your-data-in-kusto-using-machine-learning"></a>使用 Machine Learning 充分利用 Kusto 中的資料 

有許多有趣的使用案例可運用機器學習服務演算法，並從遙測資料衍生出有趣的深入解析。 雖然這些演算法通常需要非常結構化的資料集作為其輸入，但原始記錄資料通常不會符合所需的結構和大小。 

我們的旅程從尋找特定 Bing 推斷服務錯誤率的異常開始。 記錄資料表具有65B 記錄，而下列簡單查詢會篩選250K 錯誤，並建立利用異常偵測函數的錯誤計數的時間序列資料[series_decompose_anomalies](series-decompose-anomaliesfunction.md)。 Kusto 服務會偵測到異常狀況，並在時間序列圖表上反白顯示為紅點。

```kusto
Logs
| where Timestamp >= datetime(2015-08-22) and Timestamp < datetime(2015-08-23) 
| where Level == "e" and Service == "Inferences.UnusualEvents_Main" 
| summarize count() by bin(Timestamp, 5min)
| render anomalychart 
```

服務發現幾個具有可疑錯誤率的時間值區。 我使用 Kusto 來放大這個時間範圍，執行一個查詢來匯總「訊息」資料行，試著尋找前幾個錯誤。 我已修剪訊息的整個堆疊追蹤中的相關部分，使其更適合頁面。 您可以看到，我對前八個錯誤有很好的成功，但卻到達了長的錯誤結尾，因為錯誤訊息是由包含變更資料的格式字串所建立。 

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
|7125|方法 ' RunCycleFromInterimData ' 的 ExecuteAlgorithmMethod 失敗 .。。
|7125|InferenceHostService 呼叫失敗。。NullReferenceException：物件參考未設定為物件的實例 .。。
|7124|未預期的推斷系統錯誤。。NullReferenceException：物件參考未設定為物件的實例 .。。 
|5112|未預期的推斷系統錯誤。。NullReferenceException：物件參考未設定為物件的實例。。
|174|InferenceHostService 呼叫失敗。. System.servicemodel. CommunicationException：寫入管道時發生錯誤： .。。
|10|方法 ' RunCycleFromInterimData ' 的 ExecuteAlgorithmMethod 失敗 .。。
|10|推斷系統錯誤。。UserInterimDataManagerException：......。
|3|InferenceHostService 呼叫失敗。 CommunicationObjectFaultedException： .。。
|1|推斷系統錯誤 .。。SocialGraph. OperationResponse .。。AIS TraceId： 8292FC561AC64BED8FA243808FE74EFD .。。
|1|推斷系統錯誤 .。。SocialGraph. OperationResponse .。。AIS TraceId： 5F79F7587FF943EC9B641E02E701AFBF .。。

這就是操作員提供協助的地方 `reduce` 。 `reduce`運算子識別出63程式碼中相同追蹤檢測點所產生的不同錯誤，並協助我將焦點放在該時間範圍內其他有意義的錯誤追蹤。

```kusto
Logs
| where Timestamp >= datetime(2015-08-22 05:00) and Timestamp < datetime(2015-08-22 06:00)
| where Level == "e" and Service == "Inferences.UnusualEvents_Main"
| reduce by Message with threshold=0.35
| project Count, Pattern
```

|計數|模式
|---|---
|7125|方法 ' RunCycleFromInterimData ' 的 ExecuteAlgorithmMethod 失敗 .。。
|  7125|InferenceHostService 呼叫失敗。。NullReferenceException：物件參考未設定為物件的實例 .。。
|  7124|未預期的推斷系統錯誤。。NullReferenceException：物件參考未設定為物件的實例 .。。
|  5112|未預期的推斷系統錯誤。。NullReferenceException：物件參考未設定為物件的實例。。
|  174|InferenceHostService 呼叫失敗。. System.servicemodel. CommunicationException：寫入管道時發生錯誤： .。。
|  63|推斷系統錯誤。。\*： Write \* 以寫入物件老闆。 \* ： SocialGraph. Reques.....。
|  10|方法 ' RunCycleFromInterimData ' 的 ExecuteAlgorithmMethod 失敗 .。。
|  10|推斷系統錯誤。。UserInterimDataManagerException：......。
|  3|InferenceHostService 呼叫失敗。。System.servicemodel \* ：的物件（system.servicemodel. \* + \* ） \* \* 就是 \* ... 的 .。。  在 Syst .。。

現在我已經對偵測到的異常帶來最大的錯誤觀點，我想要瞭解這些錯誤在系統上的影響。 「記錄」資料表包含其他維度資料，例如「元件」、「叢集」等等。新的 ' autocluster ' 外掛程式可協助我使用簡單的查詢來衍生該深入解析。 在下面的範例中，我可以清楚看到每個前四個錯誤都是元件特有的，而前三個錯誤則是 DB4 叢集特有的，第四個則會發生在所有叢集上。

```kusto
Logs
| where Timestamp >= datetime(2015-08-22 05:00) and Timestamp < datetime(2015-08-22 06:00)
| where Level == "e" and Service == "Inferences.UnusualEvents_Main"
| evaluate autocluster()
```

|計數 |百分比 (%)|元件|叢集|訊息
|---|---|---|---|---
|7125|26.64|InferenceHostService|DB4|ExecuteAlgorithmMethod for method ...。
|7125|26.64|未知的元件|DB4|InferenceHostService 呼叫失敗 ...。
|7124|26.64|InferenceAlgorithmExecutor|DB4|未預期的推斷系統錯誤 .。。
|5112|19.11|InferenceAlgorithmExecutor|*|未預期的推斷系統錯誤 .。。 

## <a name="mapping-values-from-one-set-to-another"></a>將值從一個集合對應到另一個

常見的使用案例是使用值的靜態對應，可協助將結果採用更方便的方式。  
例如，請考慮下表。 Devicemodel 傳遞會指定裝置的型號，這不是很方便的裝置名稱參考形式。  


|DeviceModel |計數 
|---|---
|iPhone5，1 |32 
|iPhone3，2 |432 
|iPhone7，2 |55 
|iPhone5，2 |66 

  
較佳的表示方式可能是：  

|FriendlyName |計數 
|---|---
|iPhone 5 |32 
|iPhone 4 |432 
|iPhone 6 |55 
|iPhone5 |66 

以下兩種方法會示範如何達成此目的。  

### <a name="mapping-using-dynamic-dictionary"></a>使用動態字典進行對應

下面的方法顯示如何使用動態字典和動態存取子來達成對應。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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

|FriendlyName|計數|
|---|---|
|iPhone 5|32|
|iPhone 4|432|
|iPhone 6|55|
|iPhone5|66|



### <a name="mapping-using-static-table"></a>使用靜態資料表的對應

下面的方法顯示如何使用持續性資料表和聯結運算子來達成對應。
 
建立對應表（僅限一次）：

```kusto
.create table Devices (DeviceModel: string, FriendlyName: string) 

.ingest inline into table Devices 
    ["iPhone5,1","iPhone 5"]["iPhone3,2","iPhone 4"]["iPhone7,2","iPhone 6"]["iPhone5,2","iPhone5"]
```

裝置的內容現在：

|DeviceModel |FriendlyName 
|---|---
|iPhone5，1 |iPhone 5 
|iPhone3，2 |iPhone 4 
|iPhone7，2 |iPhone 6 
|iPhone5，2 |iPhone5 


建立測試資料表來源的相同技巧：

```kusto
.create table Source (DeviceModel: string, Count: int)

.ingest inline into table Source ["iPhone5,1",32]["iPhone3,2",432]["iPhone7,2",55]["iPhone5,2",66]
```


加入和專案：

```kusto
Devices  
| join (Source) on DeviceModel  
| project FriendlyName, Count
```

結果：

|FriendlyName |計數 
|---|---
|iPhone 5 |32 
|iPhone 4 |432 
|iPhone 6 |55 
|iPhone5 |66 


## <a name="creating-and-using-query-time-dimension-tables"></a>建立和使用查詢時間維度資料表

在許多情況下，您會想要將查詢的結果與某些不會儲存在資料庫中的特定維度資料表聯結在一起。 您可以定義一個運算式，其結果為範圍為單一查詢的資料表，做法如下：

<!-- csl: https://help.kusto.windows.net/Samples -->
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

以下是稍微複雜一點的範例：

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

## <a name="retrieving-the-latest-by-timestamp-records-per-identity"></a>依每個身分識別抓取最新的（依時間戳記）記錄

假設您有一個資料表，其中包含一個資料 `id` 行（識別與每個資料列相關聯的實體，例如使用者識別碼或節點識別碼）和資料 `timestamp` 行（提供資料列的時間參考），以及其他資料行。 您的目標是撰寫一個查詢，針對資料行的每個值傳回最新的2筆記錄 `id` ，其中「最新」定義為「具有最高的值」 `timestamp` 。

這可以使用[最上層嵌套的運算子](topnestedoperator.md)來完成。
首先，我們要提供查詢，然後說明它：

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

附註
1. `datatable`只是為了示範目的而產生一些測試資料的方式。 當然，事實上，您會在這裡提供資料。
2. 這一行基本上表示「傳回所有相異值」 `id` 。
3. 然後，這一行會針對最大化資料行的前2筆記錄 `timestamp` ，傳回上一個層級（這裡只是 `id` ）和在此層級指定的資料行（在此為 `timestamp` ）。
4. 這一行會 `bla` 針對上一個層級所傳回的每個記錄，加入資料行的值。 如果資料表有其他相關的資料行，則會針對每個這類資料行重複這一行。
5. 最後，我們會使用「專案外」[運算子](projectawayoperator.md)來移除所引進的「額外」資料行 `top-nested` 。

## <a name="extending-a-table-with-some-percent-of-total-calculation"></a>以總計百分比計算的方式來擴充資料表

通常，當其中一個具有包含數值資料行的表格式運算式時，會 desireable 將該資料行連同其值一起呈現給使用者，並以總計的百分比表示。 例如，假設有一些查詢的值是下表：

|SomeSeries|SomeInt|
|----------|-------|
|Foo       |    100|
|長條圖       |    200|

而且您想要將此資料表顯示為：

|SomeSeries|SomeInt|百分比 |
|----------|-------|----|
|Foo       |    100|33.3|
|長條圖       |    200|66.6|

若要這樣做，您必須計算資料行的總計（總和） `SomeInt` ，然後將此資料行的每個值除以總計。 您可以使用[as 運算子](asoperator.md)，為這些結果提供名稱，藉此為任意結果執行這項操作：

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

## <a name="performing-aggregations-over-a-sliding-window"></a>透過滑動視窗執行匯總
下列範例顯示如何使用滑動視窗摘要資料行。 例如，讓我們採用下表，其中包含依時間戳記的水果價格。 假設我們想要使用7天的滑動視窗來計算每日水果的最小值、最大值和總和成本。 換句話說，結果集中的每一筆記錄會匯總過去7天，而結果會包含分析期間每天的記錄。  

水果資料表： 

|時間戳記|水果|Price|
|---|---|---|
|2018-09-24 21：00：00.0000000|香蕉|3|
|2018-09-25 20：00：00.0000000|蘋果|9|
|2018-09-26 03：00：00.0000000|香蕉|4|
|2018-09-27 10：00：00.0000000|Plums|8|
|2018-09-28 07：00：00.0000000|香蕉|6|
|2018-09-29 21：00：00.0000000|香蕉|8|
|2018-09-30 01：00：00.0000000|Plums|2|
|2018-10-01 05：00：00.0000000|香蕉|0|
|2018-10-02 02：00：00.0000000|香蕉|0|
|2018-10-03 13：00：00.0000000|Plums|4|
|2018-10-04 14：00：00.0000000|蘋果|8|
|2018-10-05 05：00：00.0000000|香蕉|2|
|2018-10-06 08：00：00.0000000|Plums|8|
|2018-10-07 12：00：00.0000000|香蕉|0|

滑動視窗匯總查詢（說明會在下列查詢結果中提供）： 

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
|2018-10-01 00：00：00.0000000|蘋果|9|9|9|
|2018-10-01 00：00：00.0000000|香蕉|0|8|18|
|2018-10-01 00：00：00.0000000|Plums|2|8|10|
|2018-10-02 00：00：00.0000000|香蕉|0|8|18|
|2018-10-02 00：00：00.0000000|Plums|2|8|10|
|2018-10-03 00：00：00.0000000|Plums|2|8|14|
|2018-10-03 00：00：00.0000000|香蕉|0|8|14|
|2018-10-04 00：00：00.0000000|香蕉|0|8|14|
|2018-10-04 00：00：00.0000000|Plums|2|4|6|
|2018-10-04 00：00：00.0000000|蘋果|8|8|8|
|2018-10-05 00：00：00.0000000|香蕉|0|8|10|
|2018-10-05 00：00：00.0000000|Plums|2|4|6|
|2018-10-05 00：00：00.0000000|蘋果|8|8|8|
|2018-10-06 00：00：00.0000000|Plums|2|8|14|
|2018-10-06 00：00：00.0000000|香蕉|0|2|2|
|2018-10-06 00：00：00.0000000|蘋果|8|8|8|
|2018-10-07 00：00：00.0000000|香蕉|0|2|2|
|2018-10-07 00：00：00.0000000|Plums|4|8|12|
|2018-10-07 00：00：00.0000000|蘋果|8|8|8|

查詢詳細資料： 

查詢會在7天內將輸入資料表中的每筆記錄「伸展」（重複），張貼其實際外觀，讓每筆記錄實際上出現7次。 因此，每一天執行匯總時，匯總會包含過去7天的所有記錄。

逐步說明（數位指的是查詢內嵌批註中的數位）：
1. 將每一筆記錄 Bin 到1d （相對於 _start）。 
2. 判斷每筆記錄的範圍結束-_bin + 7d，除非這超出 _（開始、結束）_ 範圍，在此情況下會進行調整。 
3. 針對每一筆記錄，建立7天（時間戳記）的陣列，從目前記錄的日期開始。 
4. mv-展開陣列，因此會將每筆記錄複製到7筆記錄，而彼此之間會有1天。 
5. 執行每天的彙總函式。 由於 #4，這實際上會匯總_過去_7 天。 
6. 最後，因為第一個7d 的資料不完整（前7天沒有回顧週期），所以我們會從最終結果中排除前7天（它們只會參與2018-10-01 的匯總）。 

## <a name="find-preceding-event"></a>尋找上一個事件
下一個範例示範如何尋找2個資料集之間的先前事件。  

*目的：* ：假設有2個資料集 a 和 b，針對 B 中的每筆記錄尋找其先前的事件（換句話說，中的 arg_max 記錄，其仍然是「較舊」，而不是 B）。 例如，以下是下列範例資料集的預期輸出： 

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

|時間戳記|Id|EventB|
|---|---|---|
|2019-01-01 00：00：00.0000000|x|Ax1|
|2019-01-01 00：00：00.0000000|z|Az1|
|2019-01-01 00：00：01.0000000|x|Ax2|
|2019-01-01 00：00：02.0000000|y|Ay1|
|2019-01-01 00：00：05.0000000|y|Ay2|

</br>

|時間戳記|Id|EventA|
|---|---|---|
|2019-01-01 00：00：03.0000000|x|B|
|2019-01-01 00：00：04.0000000|x|B|
|2019-01-01 00：00：04.0000000|y|B|
|2019-01-01 00：02：00.0000000|z|B|

預期輸出： 

|Id|時間戳記|EventB|A_Timestamp|EventA|
|---|---|---|---|---|
|x|2019-01-01 00：00：03.0000000|B|2019-01-01 00：00：01.0000000|Ax2|
|x|2019-01-01 00：00：04.0000000|B|2019-01-01 00：00：01.0000000|Ax2|
|y|2019-01-01 00：00：04.0000000|B|2019-01-01 00：00：02.0000000|Ay1|
|z|2019-01-01 00：02：00.0000000|B|2019-01-01 00：00：00.0000000|Az1|

針對此問題，建議有2種不同的方法。 您應該在您的特定資料集上進行測試，以找出最適合您的一項（在不同的資料集上執行的方式可能有所不同）。 

### <a name="suggestion-1"></a>建議 #1：
這項建議會依識別碼和時間戳記序列化這兩個資料集，然後將所有 B 事件群組在所有事件前面，並從中挑選 `arg_max` 全部，就像群組中的一樣。 

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

### <a name="suggestion-2"></a>建議 #2：
這項建議需要定義最大回顧期限（「較舊」，我們允許中的記錄與 B？進行比較），然後在識別碼和此回顧期間聯結2個資料集。 聯結會產生所有可能的候選項目（所有早于 B 且在回顧期間內的記錄），然後再 arg_min （TimestampB – TimestampA）篩選最接近的一個。 回顧期限越小，查詢預期會執行得更好。 

在下列範例中，回顧期限設定為1m，因此識別碼為 ' z ' 的記錄沒有對應的 ' A ' 事件（因為它是 ' A '，2m 較舊）。

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

|Id|B_Timestamp|A_Timestamp|EventB|EventA|
|---|---|---|---|---|
|x|2019-01-01 00：00：03.0000000|2019-01-01 00：00：01.0000000|B|Ax2|
|x|2019-01-01 00：00：04.0000000|2019-01-01 00：00：01.0000000|B|Ax2|
|y|2019-01-01 00：00：04.0000000|2019-01-01 00：00：02.0000000|B|Ay1|
|z|2019-01-01 00：02：00.0000000||B||
