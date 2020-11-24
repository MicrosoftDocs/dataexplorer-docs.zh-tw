---
title: 範例-Azure 資料總管
description: 本文說明 Azure 資料總管中的範例。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/08/2020
ms.localizationpriority: high
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: b448f4249c777d9b9d61e58dad993f3da1817fda
ms.sourcegitcommit: 4e811d2f50d41c6e220b4ab1009bb81be08e7d84
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95512463"
---
# <a name="samples"></a>範例

::: zone pivot="azuredataexplorer"

以下是一些常見的查詢需求，以及如何使用 Kusto 查詢語言來滿足這些需求。

## <a name="display-a-column-chart"></a>顯示直條圖

投影兩個或多個資料行，並使用它們做為圖表的 x 和 y 軸。

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto 
StormEvents
| where isnotempty(EndLocation) 
| summarize event_count=count() by EndLocation
| top 10 by event_count
| render columnchart
```

* 第一個資料行形成 X 軸。 可以是數值、日期時間或字串。 
* 使用 `where` 、 `summarize` 和 `top` 來限制您所顯示的資料量。
* 排序結果來定義 X 軸的順序。

:::image type="content" source="images/samples/060.png" alt-text="直條圖的螢幕擷取畫面。Y 軸的範圍是從0到50左右。十個彩色資料行描繪10個位置的個別值。":::

## <a name="get-sessions-from-start-and-stop-events"></a>從開始和停止事件取得工作階段

假設您有事件記錄檔。 某些事件會標示擴充活動或會話的開始或結束。 

|名稱|City|SessionId|時間戳記|
|---|---|---|---|
|Start|London|2817330|2015-12-09T10:12:02.32|
|Game|London|2817330|2015-12-09T10:12:52.45|
|Start|曼徹斯特|4267667|2015-12-09T10:14:02.23|
|停止|London|2817330|2015-12-09T10:23:43.18|
|取消|曼徹斯特|4267667|2015-12-09T10:27:26.29|
|停止|曼徹斯特|4267667|2015-12-09T10:28:31.72|

每個事件都有 SessionId。 問題是要與相同識別碼的開始和停止事件相符。

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

1. 您 [`let`](./letstatement.md) 可以使用來命名資料表的投射，此資料表在進入聯結之前，會盡可能精簡下。
1. 使用 [`Project`](./projectoperator.md) 變更時間戳記的名稱，讓開始和停止時間可以出現在結果中。 
   它也會選取要在結果中看到的其他資料行。 
1. 使用 [`join`](./joinoperator.md) 來比對相同活動的開始和停止專案，為每個活動建立一個資料列。 
1. 最後， `project` 會再次加入資料行以顯示活動的持續時間。


|City|SessionId|StartTime|StopTime|持續時間|
|---|---|---|---|---|
|London|2817330|2015-12-09T10:12:02.32|2015-12-09T10:23:43.18|00:11:40.46|
|曼徹斯特|4267667|2015-12-09T10:14:02.23|2015-12-09T10:28:31.72|00:14:29.49|

### <a name="get-sessions-without-session-id"></a>取得會話，沒有會話識別碼

假設 [開始] 和 [停止] 事件不會有可比對的會話識別碼。 但我們有工作階段執行位置的用戶端 IP 位址。 假設每個用戶端位址一次只會產生一個工作階段，我們可以將來自相同 IP 位址的每個開始事件與下一個結束事件配對。

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

聯結會將來自相同用戶端 IP 位址的開始時間與所有的停止時間配對。 
1. 移除與先前停止時間相符的專案。
1. 依開始時間和 IP 分組，以取得每個會話的群組。 
1. 提供 `bin` StartTime 參數的函數。 如果您沒有這樣做，Kusto 會自動使用1小時的 bin，以符合某些開始時間與錯誤的停止時間。

`arg_min` 挑選每個群組中最小持續時間的資料列，而 `*` 參數會傳遞所有其他資料行。 引數首碼 "min_" 至每個資料行名稱。 

:::image type="content" source="images/samples/040.png" alt-text="列出結果的表格，其中包含開始時間的資料行、用戶端 I P、持續時間、城市，以及每個用戶端/開始時間組合的最早停止。"::: 

新增程式碼，以方便調整大小的容器計算持續時間。在此範例中，因為橫條圖的喜好設定，請除以 `1s` 以將時間範圍轉換為數字。 

```
    // Count the frequency of each duration:
    | summarize count() by duration=bin(min_duration/1s, 10) 
      // Cut off the long tail:
    | where duration < 300
      // Display in a bar chart:
    | sort by duration asc | render barchart 
```

:::image type="content" source="images/samples/050.png" alt-text="描述指定範圍內持續時間的會話數目的直條圖。超過400個會話持續10秒鐘。小於100是290秒。":::

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

## <a name="chart-concurrent-sessions-over-time"></a>一段時間的並行工作階段圖表

假設您的活動資料表具有其開始和結束時間。 顯示一段時間的圖表，顯示在任何時間內同時執行的活動數目。

以下是範例輸入，稱為 `X` 。

|SessionId | StartTime | StopTime |
|---|---|---|
| a | 10:01:03 | 10:10:08 |
| b | 10:01:29 | 10:03:10 |
| c | 10:03:02 | 10:05:20 |

若為1分鐘的圖層中的圖表，請建立一個會在每個1m 間隔的位置，每個執行中的活動都有一個計數。

以下是中繼結果。

```kusto
X | extend samples = range(bin(StartTime, 1m), StopTime, 1m)
```

`range` 依指定的間隔產生值的陣列。

|SessionId | StartTime | StopTime  | 範例|
|---|---|---|---|
| a | 10:01:33 | 10:06:31 | [10:01:00,10:02:00,...10:06:00]|
| b | 10:02:29 | 10:03:45 | [10:02:00,10:03:00]|
| c | 10:03:12 | 10:04:30 | [10:03:00,10:04:00]|

使用 [mv](./mvexpandoperator.md)展開，而不是保留這些陣列。

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

現在依取樣時間將這些專案分組，以計算每個活動的出現次數。

```kusto
X
| mv-expand samples = range(bin(StartTime, 1m), StopTime , 1m)
| summarize count(SessionId) by bin(todatetime(samples),1m)
```

* 使用 todatetime ( # A1 的原因是， [mv 展開](./mvexpandoperator.md) 會產生動態類型的資料行。
* 使用 bin ( # A1，因為對於數值和日期，如果您未提供 bin 函式，則 [摘要] 一律會套用預設間隔的 bin 函數。 


| count_SessionId | 範例|
|---|---|
| 1 | 10:01:00|
| 2 | 10:02:00|
| 3 | 10:03:00|
| 2 | 10:04:00|
| 1 | 10:05:00|
| 1 | 10:06:00|

您可以將結果轉譯為橫條圖或時間圖。

## <a name="introduce-null-bins-into-summarize"></a>將 null 分類引入摘要

將 `summarize` 運算子套用至包含資料行的群組索引鍵時 `datetime` ，請將這些值「bin」至固定寬度的 bin。

```kusto
let StartTime=ago(12h);
let StopTime=now()
T
| where Timestamp > StartTime and Timestamp <= StopTime 
| where ...
| summarize Count=count() by bin(Timestamp, 5m)
```

上述範例會產生資料表，其中每個資料列群組都有一個資料列 `T` ，這些資料列落在五分鐘的每個資料列中。 它不會新增「null bin」-- `StartTime` 在與之間沒有對應資料列的 time bin 值資料列 `StopTime` `T` 。 

最好使用這些 bin 來「填補」資料表。以下是其中一種做法。

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

1. `union`運算子可讓您在資料表中加入其他資料列。 這些資料列是由運算式所產生 `union` 。
1. `range`運算子會產生具有單一資料列/資料行的資料表。
   資料表不會用於以外的任何其他 `mv-expand` 作業。
1. `mv-expand`函數的運算子 `range` 會建立和之間有5分鐘的資料列數目 `StartTime` `EndTime` 。
1. 使用 `Count` 的 `0` 。
1. 運算子會將 `summarize` 來自原始 (左方或外部) 引數的 bin 群組在一起 `union` 。 運算子也會將內部引數分類 (null bin 資料列) 。 此程式可確保每個 bin 的輸出都有一個資料列，其值為零或原始計數。  

## <a name="get-more-out-of-your-data-in-kusto-with-machine-learning"></a>利用 Machine Learning 在 Kusto 中充分利用您的資料 

有許多有趣的使用案例，可利用機器學習演算法，並衍生出遙測資料的有趣見解。 這些演算法通常需要非常結構化的資料集做為輸入。 原始記錄資料通常不會符合所需的結構和大小。 

首先，尋找特定 Bing 推斷服務錯誤率的異常。 Logs 資料表有65B 記錄。 下列簡單查詢會篩選250K 錯誤，並建立使用異常偵測函式 [series_decompose_anomalies](series-decompose-anomaliesfunction.md)之錯誤計數的時間序列資料。 Kusto 服務會偵測到異常狀況，並在時間序列圖上以紅色點反白顯示。

```kusto
Logs
| where Timestamp >= datetime(2015-08-22) and Timestamp < datetime(2015-08-23) 
| where Level == "e" and Service == "Inferences.UnusualEvents_Main" 
| summarize count() by bin(Timestamp, 5min)
| render anomalychart 
```

服務識別出幾個具有可疑錯誤率的時間值區。 使用 Kusto 來放大此時間範圍，然後執行在 ' Message ' 資料行上匯總的查詢。 嘗試找出最常見的錯誤。 

訊息整個堆疊追蹤的相關部分會被修剪，使其更符合頁面的大小。 

您可以看到前八個錯誤的成功識別。 但是，由於錯誤訊息是由包含變更資料的格式字串所建立，因此會出現一系列長串的錯誤。 

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
|7125|InferenceHostService 呼叫 failed..System。NullReferenceException：物件參考未設定為物件的實例 .。。
|7124|未預期的推斷系統 error..System。NullReferenceException：物件參考未設定為物件的實例 .。。 
|5112|未預期的推斷系統 error..System。NullReferenceException：物件參考未設定為物件的實例。
|174|InferenceHostService call failed..System. CommunicationException：寫入管道時發生錯誤： .。。
|10|方法 ' RunCycleFromInterimData ' 的 ExecuteAlgorithmMethod 失敗 .。。
|10|推斷系統錯誤。UserInterimDataManagerException：......。
|3|InferenceHostService call failed..System. System.servicemodel.communicationobjectfaultedexception： .。。
|1|推斷系統錯誤 .。。SocialGraph。 OperationResponse .。。AIS TraceId： 8292FC561AC64BED8FA243808FE74EFD .。。
|1|推斷系統錯誤 .。。SocialGraph。 OperationResponse .。。AIS TraceId： 5F79F7587FF943EC9B641E02E701AFBF .。。

這是 `reduce` 操作員可協助的地方。 操作員識別出63不同的錯誤，這些錯誤是由程式碼中的相同追蹤檢測點所產生，並且有助於將焦點放在該時間範圍內的其他有意義的錯誤追蹤。

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
|  7125|InferenceHostService 呼叫 failed..System。NullReferenceException：物件參考未設定為物件的實例 .。。
|  7124|未預期的推斷系統 error..System。NullReferenceException：物件參考未設定為物件的實例 .。。
|  5112|未預期的推斷系統 error..System。NullReferenceException：物件參考未設定為物件的實例。
|  174|InferenceHostService call failed..System. CommunicationException：寫入管道時發生錯誤： .。。
|  63|推斷系統錯誤。\*SocialGraph： write， \* 寫入物件老闆. \* ：. Reques .... （.）
|  10|方法 ' RunCycleFromInterimData ' 的 ExecuteAlgorithmMethod 失敗 .。。
|  10|推斷系統錯誤。UserInterimDataManagerException：......。
|  3|InferenceHostService 呼叫 failed..System。System.servicemodel. \* ：物件（system.servicemodel.... \* + \* ） \* \* 是 \* .。。  在 Syst .。。

現在您可以充分瞭解對偵測到的異常造成的最大錯誤。

若要瞭解這些錯誤對於跨範例系統的影響： 
* ' Logs ' 資料表包含額外的維度資料，例如 ' Component '、' Cluster ' 等等。
* 新的 ' autocluster ' 外掛程式可協助您使用簡單的查詢來衍生該深入解析。 
* 在下列範例中，您可以清楚地看到每個前四個錯誤都是元件特有的。 此外，雖然前三個錯誤是 DB4 叢集特有的，但第四個錯誤會發生在所有叢集。

```kusto
Logs
| where Timestamp >= datetime(2015-08-22 05:00) and Timestamp < datetime(2015-08-22 06:00)
| where Level == "e" and Service == "Inferences.UnusualEvents_Main"
| evaluate autocluster()
```

|計數 |百分比 (%)|元件|叢集|訊息
|---|---|---|---|---
|7125|26.64|InferenceHostService|DB4|方法的 ExecuteAlgorithmMethod .。。
|7125|26.64|未知的元件|DB4|InferenceHostService 呼叫失敗 .。。
|7124|26.64|InferenceAlgorithmExecutor|DB4|未預期的推斷系統錯誤 .。。
|5112|19.11|InferenceAlgorithmExecutor|*|未預期的推斷系統錯誤 .。。 

## <a name="map-values-from-one-set-to-another"></a>將值從一個集合對應到另一個集合

常見的使用案例是值的靜態對應，有助於讓結果更美觀。
例如，請考慮下一個表格。 
`DeviceModel` 指定裝置的模型，這並不是很方便參考裝置名稱的形式。  


|DeviceModel |計數 
|---|---
|iPhone5，1 |32 
|iPhone3，2 |432 
|iPhone7，2 |55 
|iPhone5，2 |66 

  
以下是較佳的標記法。  

|FriendlyName |計數 
|---|---
|iPhone 5 |32 
|iPhone 4 |432 
|iPhone 6 |55 
|iPhone5 |66 

下列兩種方法會示範如何達成標記法。  

### <a name="mapping-using-dynamic-dictionary"></a>使用動態字典進行對應

此方法會顯示與動態字典和動態存取子的對應。

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

### <a name="map-using-static-table"></a>使用靜態資料表對應

此方法會顯示與持續性資料表和聯結運算子的對應。
 
建立對應表，只要一次即可。

```kusto
.create table Devices (DeviceModel: string, FriendlyName: string) 

.ingest inline into table Devices 
    ["iPhone5,1","iPhone 5"]["iPhone3,2","iPhone 4"]["iPhone7,2","iPhone 6"]["iPhone5,2","iPhone5"]
```

立即的裝置內容。

|DeviceModel |FriendlyName 
|---|---
|iPhone5，1 |iPhone 5 
|iPhone3，2 |iPhone 4 
|iPhone7，2 |iPhone 6 
|iPhone5，2 |iPhone5 

使用相同的技巧來建立測試資料表來源。

```kusto
.create table Source (DeviceModel: string, Count: int)

.ingest inline into table Source ["iPhone5,1",32]["iPhone3,2",432]["iPhone7,2",55]["iPhone5,2",66]
```

加入和專案。

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


## <a name="create-and-use-query-time-dimension-tables"></a>建立和使用查詢階段維度資料表

您通常會想要將查詢的結果與未儲存在資料庫中的某些特定維度資料表聯結。 您可以定義運算式，其結果為範圍設定為單一查詢的資料表。 例如：

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

以下是稍微複雜一點的範例。

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

## <a name="retrieve-the-latest-records-by-timestamp-per-identity"></a>依每個身分識別的時間戳記) 抓取 (的最新記錄

假設您有一個資料表，其中包含：
* `ID`識別與每個資料列相關聯之實體的資料行，例如使用者識別碼或節點識別碼。
* `timestamp`提供資料列時間參考的資料行
* 其他資料行

此查詢會傳回資料行之每個值的最新兩筆記錄 `ID` ，其中「最新」定義為「具有最大值」， `timestamp` 可使用 [最上層嵌套運算子](topnestedoperator.md)來建立。

例如：

```kusto
datatable(id:string, timestamp:datetime, bla:string)           // #1
  [
  "Barak",  datetime(2015-01-01), "1",
  "Barak",  datetime(2016-01-01), "2",
  "Barak",  datetime(2017-01-20), "3",
  "Donald", datetime(2017-01-20), "4",
  "Donald", datetime(2017-01-18), "5",
  "Donald", datetime(2017-01-19), "6"
  ]
| top-nested   of id        by dummy0=max(1),                  // #2
  top-nested 2 of timestamp by dummy1=max(timestamp),          // #3
  top-nested   of bla       by dummy2=max(1)                   // #4
| project-away dummy0, dummy1, dummy2                          // #5
```

以下的附注編號是指程式碼範例中最右邊的數位。

1. `datatable`是一種產生一些測試資料的方式，以供示範之用。 一般來說，您會在這裡使用真正的資料。
1. 這一行基本上表示「傳回所有相異值」 `id` 。
1. 這一行接著會傳回最大化的前兩筆記錄：
     * 資料 `timestamp` 行
     * 先前層級 (的資料行，只是 `id`) 
     * 在此層級指定的資料行 (這裡， `timestamp`) 
1. 這行會 `bla` 針對前一個層級所傳回的每個記錄，加入資料行的值。 如果資料表有其他感興趣的資料行，您可以針對每個這類資料行重複這一行。
1. 這最後一行使用 [專案離開運算子](projectawayoperator.md) 來移除所引進的「額外」資料行 `top-nested` 。

## <a name="extend-a-table-with-some-percent-of-total-calculation"></a>使用總計計算百分比來擴充資料表

包含數值資料行的表格式運算式在伴隨時會更有用，而且其值會以總計的百分比表示。 例如，假設有一個會產生下表的查詢：

|SomeSeries|SomeInt|
|----------|-------|
|Foo       |    100|
|橫條圖       |    200|

如果您想要將此資料表顯示為：

|SomeSeries|SomeInt|百分比 |
|----------|-------|----|
|Foo       |    100|33.3|
|橫條圖       |    200|66.6|

然後，您必須計算資料行的總 (總和) `SomeInt` ，然後將此資料行的每個值除以總計。 針對任意的結果，請使用 [as 運算子](asoperator.md)。

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

## <a name="perform-aggregations-over-a-sliding-window"></a>在滑動視窗上執行匯總

下列範例顯示如何使用滑動視窗摘要資料行。
使用下表，其中包含依時間戳記的水果價格。 使用七天的滑動時間範圍，計算每日水果的最小值、最大值和總和成本。 結果集中的每一筆記錄都會匯總前七天，且結果會在分析期間包含每天的一筆記錄。  

水果資料表：

|時間戳記|水果|價格|
|---|---|---|
|2018-09-24 21：00：00.0000000|香蕉|3|
|2018-09-25 20：00：00.0000000|蘋果|9|
|2018-09-26 03：00：00.0000000|香蕉|4|
|2018-09-27 10：00：00.0000000|李子|8|
|2018-09-28 07：00：00.0000000|香蕉|6|
|2018-09-29 21：00：00.0000000|香蕉|8|
|2018-09-30 01：00：00.0000000|李子|2|
|2018-10-01 05：00：00.0000000|香蕉|0|
|2018-10-02 02：00：00.0000000|香蕉|0|
|2018-10-03 13：00：00.0000000|李子|4|
|2018-10-04 14：00：00.0000000|蘋果|8|
|2018-10-05 05：00：00.0000000|香蕉|2|
|2018-10-06 08：00：00.0000000|李子|8|
|2018-10-07 12：00：00.0000000|香蕉|0|

滑動視窗彙總查詢。
以下是查詢結果的說明：

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
|2018-10-01 00：00：00.0000000|李子|2|8|10|
|2018-10-02 00：00：00.0000000|香蕉|0|8|18|
|2018-10-02 00：00：00.0000000|李子|2|8|10|
|2018-10-03 00：00：00.0000000|李子|2|8|14|
|2018-10-03 00：00：00.0000000|香蕉|0|8|14|
|2018-10-04 00：00：00.0000000|香蕉|0|8|14|
|2018-10-04 00：00：00.0000000|李子|2|4|6|
|2018-10-04 00：00：00.0000000|蘋果|8|8|8|
|2018-10-05 00：00：00.0000000|香蕉|0|8|10|
|2018-10-05 00：00：00.0000000|李子|2|4|6|
|2018-10-05 00：00：00.0000000|蘋果|8|8|8|
|2018-10-06 00：00：00.0000000|李子|2|8|14|
|2018-10-06 00：00：00.0000000|香蕉|0|2|2|
|2018-10-06 00：00：00.0000000|蘋果|8|8|8|
|2018-10-07 00：00：00.0000000|香蕉|0|2|2|
|2018-10-07 00：00：00.0000000|李子|4|8|12|
|2018-10-07 00：00：00.0000000|蘋果|8|8|8|

查詢詳細資料：

查詢「伸展」 (重複) 輸入資料表中的每一筆記錄在其實際外觀後的七天內。 每一筆記錄實際上都會出現七次。
如此一來，每日匯總就會包含過去七天的所有記錄。

以下逐步解說編號是指程式碼範例中的數位，最右邊：
1. 將每一筆記錄分類至一天， (相對於 _start) 。 
2. 判斷每一筆記錄的範圍結尾-_bin + 7d，除非這超出 _(開始、結束)_ 範圍，在此情況下會進行調整。 
3. 針對每一筆記錄，建立七天的陣列 (時間戳記) ，從目前的記錄天開始。 
4. mv-展開陣列，藉此將每一筆記錄複製到七筆記錄，彼此相隔1天。 
5. 每天執行彙總函式。 由於 #4，這實際上會匯總 _過去_ 七天。 
6. 前七天的資料不完整。 前七天沒有任何7d 回顧期限。 前七天會從最終結果中排除。 在此範例中，他們只會參與2018-10-01 的匯總。

## <a name="find-preceding-event"></a>尋找先前的活動
下一個範例示範如何在2個資料集之間尋找先前的事件。  

*目的：*：有兩個資料集： A 和 B。針對 B 中的每筆記錄，在 (中找到其先前的事件，也就是中的 arg_max 記錄，該記錄仍「早于」 B) 。 以下是下列範例資料集的預期輸出。 

```kusto
let A = datatable(Timestamp:datetime, ID:string, EventA:string)
[
    datetime(2019-01-01 00:00:00), "x", "Ax1",
    datetime(2019-01-01 00:00:01), "x", "Ax2",
    datetime(2019-01-01 00:00:02), "y", "Ay1",
    datetime(2019-01-01 00:00:05), "y", "Ay2",
    datetime(2019-01-01 00:00:00), "z", "Az1"
];
let B = datatable(Timestamp:datetime, ID:string, EventB:string)
[
    datetime(2019-01-01 00:00:03), "x", "B",
    datetime(2019-01-01 00:00:04), "x", "B",
    datetime(2019-01-01 00:00:04), "y", "B",
    datetime(2019-01-01 00:02:00), "z", "B"
];
A; B
```

|時間戳記|識別碼|EventB|
|---|---|---|
|2019-01-01 00：00：00.0000000|x|Ax1|
|2019-01-01 00：00：00.0000000|z|Az1|
|2019-01-01 00：00：01.0000000|x|Ax2|
|2019-01-01 00：00：02.0000000|y|Ay1|
|2019-01-01 00：00：05.0000000|y|Ay2|

</br>

|時間戳記|識別碼|EventA|
|---|---|---|
|2019-01-01 00：00：03.0000000|x|B|
|2019-01-01 00：00：04.0000000|x|B|
|2019-01-01 00：00：04.0000000|y|B|
|2019-01-01 00：02：00.0000000|z|B|

預期輸出： 

|識別碼|時間戳記|EventB|A_Timestamp|EventA|
|---|---|---|---|---|
|x|2019-01-01 00：00：03.0000000|B|2019-01-01 00：00：01.0000000|Ax2|
|x|2019-01-01 00：00：04.0000000|B|2019-01-01 00：00：01.0000000|Ax2|
|y|2019-01-01 00：00：04.0000000|B|2019-01-01 00：00：02.0000000|Ay1|
|z|2019-01-01 00：02：00.0000000|B|2019-01-01 00：00：00.0000000|Az1|

這個問題有兩種不同的方法建議。 您應在特定資料集上進行測試，以找出最適合您的資料集。

> [!NOTE] 
> 每個方法在不同的資料集上可能會以不同的方式執行。

### <a name="suggestion-1"></a>建議 #1

這項建議會依識別碼和時間戳記序列化這兩個資料集，然後將所有 B 事件全部群組在事件前面，然後挑選 `arg_max` 所有的，如同群組中一樣。

```kusto
A
| extend A_Timestamp = Timestamp, Kind="A"
| union (B | extend B_Timestamp = Timestamp, Kind="B")
| order by ID, Timestamp asc 
| extend t = iff(Kind == "A" and (prev(Kind) != "A" or prev(Id) != ID), 1, 0)
| extend t = row_cumsum(t)
| summarize Timestamp=make_list(Timestamp), EventB=make_list(EventB), arg_max(A_Timestamp, EventA) by t, ID
| mv-expand Timestamp to typeof(datetime), EventB to typeof(string)
| where isnotempty(EventB)
| project-away t
```

### <a name="suggestion-2"></a>建議 #2

這項建議需要最大回顧期限 (在與 B 相較之下，可能會有多少「舊」記錄。方法接著會聯結識別碼和這個回顧期間的兩個資料集。 聯結會產生所有可能的候選項目、所有早于 B 且在回顧期限內的記錄，然後將最接近 B 的一筆記錄篩選 arg_min (TimestampB – TimestampA) 。 回顧期限越短，查詢結果就越好。

在下列範例中，回顧期限設定為1m，且識別碼為 ' z ' 的記錄沒有對應的 ' A ' 事件，因為2m 的 ' A ' 是較舊的。

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
) on ID, _range
| where isnull(A_Timestamp) or (A_Timestamp <= B_Timestamp and B_Timestamp <= A_Timestamp + _maxLookbackPeriod)
| extend diff = coalesce(B_Timestamp - A_Timestamp, _maxLookbackPeriod*2)
| summarize arg_min(diff, *) by ID
| project ID, B_Timestamp, A_Timestamp, EventB, EventA
```

|Id|B_Timestamp|A_Timestamp|EventB|EventA|
|---|---|---|---|---|
|x|2019-01-01 00：00：03.0000000|2019-01-01 00：00：01.0000000|B|Ax2|
|x|2019-01-01 00：00：04.0000000|2019-01-01 00：00：01.0000000|B|Ax2|
|y|2019-01-01 00：00：04.0000000|2019-01-01 00：00：02.0000000|B|Ay1|
|z|2019-01-01 00：02：00.0000000||B||

::: zone-end

::: zone pivot="azuremonitor"

## <a name="string-operations"></a>字串作業
下列各節提供在 Kusto 查詢語言中使用字串的範例。

### <a name="strings-and-escaping-them"></a>字串與字串逸出
字串值是被單引號字元或雙引號字元括住。 反斜線 (\\) 用來將字元換行到後面的字元，例如 \t for tab、\n 表示分行符號和 \" 引號字元本身。

```Kusto
print "this is a 'string' literal in double \" quotes"
```

```Kusto
print 'this is a "string" literal in single \' quotes'
```

若要防止 "\\" 被視為逸出字元，請將 "\@" 新增為字串的前置詞：

```Kusto
print @"C:\backslash\not\escaped\with @ prefix"
```


### <a name="string-comparisons"></a>字串比較

運算子       |描述                         |區分大小寫|範例 (結果為 `true`)
---------------|------------------------------------|--------------|-----------------------
`==`           |等於                              |是           |`"aBc" == "aBc"`
`!=`           |不等於                          |是           |`"abc" != "ABC"`
`=~`           |等於                              |否            |`"abc" =~ "ABC"`
`!~`           |不等於                          |否            |`"aBc" !~ "xyz"`
`has`          |右側是左側中的完整詞彙 |否|`"North America" has "america"`
`!has`         |右側不是左側中的完整詞彙       |否            |`"North America" !has "amer"` 
`has_cs`       |右側是左側中的完整詞彙 |是|`"North America" has_cs "America"`
`!has_cs`      |右側不是左側中的完整詞彙       |是            |`"North America" !has_cs "amer"` 
`hasprefix`    |右側是左側中的詞彙前置詞         |否            |`"North America" hasprefix "ame"`
`!hasprefix`   |右側不是左側中的詞彙前置詞     |否            |`"North America" !hasprefix "mer"` 
`hasprefix_cs`    |右側是左側中的詞彙前置詞         |是            |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs`   |右側不是左側中的詞彙前置詞     |是            |`"North America" !hasprefix_cs "CA"` 
`hassuffix`    |右側是左側中的詞彙後置詞         |否            |`"North America" hassuffix "ica"`
`!hassuffix`   |右側不是左側中的詞彙後置詞     |否            |`"North America" !hassuffix "americ"`
`hassuffix_cs`    |右側是左側中的詞彙後置詞         |是            |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs`   |右側不是左側中的詞彙後置詞     |是            |`"North America" !hassuffix_cs "icA"`
`contains`     |右側是左側的子項目  |否            |`"FabriKam" contains "BRik"`
`!contains`    |u.4hk4右側未出現在左側中           |否            |`"Fabrikam" !contains "xyz"`
`contains_cs`   |右側是左側的子項目  |是           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |u.4hk4右側未出現在左側中           |是           |`"Fabrikam" !contains_cs "Kam"`
`startswith`   |右側是左側的初始項目|否            |`"Fabrikam" startswith "fab"`
`!startswith`  |右側不是左側的初始項目|否        |`"Fabrikam" !startswith "kam"`
`startswith_cs`   |右側是左側的初始項目|是            |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`  |右側不是左側的初始項目|是        |`"Fabrikam" !startswith_cs "fab"`
`endswith`     |右側是左側的結尾項目|否             |`"Fabrikam" endswith "Kam"`
`!endswith`    |右側不是左側的結尾項目|否         |`"Fabrikam" !endswith "brik"`
`endswith_cs`     |右側是左側的結尾項目|是             |`"Fabrikam" endswith "Kam"`
`!endswith_cs`    |右側不是左側的結尾項目|是         |`"Fabrikam" !endswith "brik"`
`matches regex`|左側包含右側的相符項        |是           |`"Fabrikam" matches regex "b.*k"`
`in`           |等於其中一個元素       |是           |`"abc" in ("123", "345", "abc")`
`!in`          |不等於任何元素   |是           |`"bca" !in ("123", "345", "abc")`


### <a name="countof"></a>countof

計算子字串在字串中的出現次數。 可以比對純文字或使用規則運算式。 純文字字串比對可能會重疊，而規則運算式比對則不會。

```
countof(text, search [, kind])
```

- `text` - 輸入字串 
- `search` - 要比對內部文字的純文字或規則運算式。
- `kind` - _正常_  | _RegEx_ (預設：正常) 。

傳回可在容器中比對搜尋字串的次數。 純文字字串比對可能會重疊，而規則運算式比對則不會。

#### <a name="plain-string-matches"></a>純文字比對

```Kusto
print countof("The cat sat on the mat", "at");  //result: 3
print countof("aaa", "a");  //result: 3
print countof("aaaa", "aa");  //result: 3 (not 2!)
print countof("ababa", "ab", "normal");  //result: 2
print countof("ababa", "aba");  //result: 2
```

#### <a name="regex-matches"></a>規則運算式比對

```Kusto
print countof("The cat sat on the mat", @"\b.at\b", "regex");  //result: 3
print countof("ababa", "aba", "regex");  //result: 1
print countof("abcabc", "a.c", "regex");  // result: 2
```


### <a name="extract"></a>extract

從給定字串取得規則運算式的相符項目。 也可選擇性地將解壓縮的子字串轉換成指定的類型。

```Kusto
extract(regex, captureGroup, text [, typeLiteral])
```

- `regex` - 規則運算式。
- `captureGroup` - 指出要擷取之擷取群組的正整數常數。 0 代表整個比對、1 代表規則運算式中第一個 '('括號')' 所比對的值，2 或以上的數字代表後續的括號。
- `text` - 要搜尋的字串。
- `typeLiteral` - 選擇性的型別常值 (例如 typeof(long))。 如果提供，所擷取的子字串會轉換為此類型。

傳回符合指定之 capture 群組 captureGroup 的子字串，選擇性地轉換成 typeLiteral。
如果沒有相符項目或型別轉換失敗，則傳回 Null。


下列範例會從活動訊號記錄擷取 *ComputerIP* 的最後一個八位元：
```Kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| project ComputerIP, last_octet=extract("([0-9]*$)", 1, ComputerIP) 
```

下列範例會擷取最後一個八位元並將它轉換為 *real* 型別 (數字) 並計算下一個 IP 值
```Kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| extend last_octet=extract("([0-9]*$)", 1, ComputerIP, typeof(real)) 
| extend next_ip=(last_octet+1)%255
| project ComputerIP, last_octet, next_ip
```

在下面的範例中，會搜尋字串 *Trace* 以尋找「時間長度」定義。 相符項目會轉換為 *real* 並乘以時間常數 (1 秒) *，這會將「時間長度」轉換為型別時間戳記*。
```Kusto
let Trace="A=12, B=34, Duration=567, ...";
print Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real));  //result: 567
print Duration_seconds =  extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s);  //result: 00:09:27
```


### <a name="isempty-isnotempty-notempty"></a>isempty、isnotempty、notempty

- 若引數是空字串或 Null，*isempty* 會傳回 true (另請參閱 *isnull*)。
- 若引數不是空字串或 Null，*isnotempty* 會傳回 true (另請參閱 *isnotnull*)。 別名：*notempty*.


```Kusto
isempty(value)
isnotempty(value)
```

### <a name="examples"></a>範例

```Kusto
print isempty("");  // result: true

print isempty("0");  // result: false

print isempty(0);  // result: false

print isempty(5);  // result: false

Heartbeat | where isnotempty(ComputerIP) | take 1  // return 1 Heartbeat record in which ComputerIP isn't empty
```


### <a name="parseurl"></a>parseurl

將 URL 分割為多個部分 (通訊協定、主機、連接埠等)，並傳回包含字串部分的字典物件。

```
parseurl(urlstring)
```

#### <a name="examples"></a>範例

```Kusto
print parseurl("http://user:pass@contoso.com/icecream/buy.aspx?a=1&b=2#tag")
```

結果將會是：
```
{
    "Scheme" : "http",
    "Host" : "contoso.com",
    "Port" : "80",
    "Path" : "/icecream/buy.aspx",
    "Username" : "user",
    "Password" : "pass",
    "Query Parameters" : {"a":"1","b":"2"},
    "Fragment" : "tag"
}
```


### <a name="replace"></a>取代

用另一個字串取代所有規則運算式相符項目。 

```
replace(regex, rewrite, input_text)
```

- `regex` - 要據以比對的規則運算式。 它可以在 '('括號')' 中包含擷取群組。
- `rewrite` - 針對由進行比對之規則運算式所進行之比對的取代規則運算式。 使用 \0 來代表整個相符項目、\1 來代表第一個擷取群組，\2 和以上的數字來代表後續的擷取群組。
- `input_text` - 要在其中搜尋的輸入字串。

傳回將所有符合的 RegEx 取代為重寫評估之後的文字。 相符項目不會重疊。


```Kusto
SecurityEvent
| take 1
| project Activity 
| extend replaced = replace(@"(\d+) -", @"Activity ID \1: ", Activity) 
```

可以具有下列結果：

活動                                        |取代後
------------------------------------------------|----------------------------------------------------------
4663 - 已嘗試存取物件  |活動識別碼 4663：已嘗試存取物件。


### <a name="split"></a>split

根據指定的分隔符號分割給定字串，並傳回結果子字串的陣列。

```
split(source, delimiter [, requestedIndex])
```

- `source` - 將根據指定的分隔符號分割的字串。
- `delimiter` - 將會用來分割來源字串的分隔符號。
- `requestedIndex` - 選擇性的基底為零的索引。 若已提供，傳回的字串陣列將只保有該項目 (若存在)。


#### <a name="examples"></a>範例

```Kusto
print split("aaa_bbb_ccc", "_");    // result: ["aaa","bbb","ccc"]
print split("aa_bb", "_");          // result: ["aa","bb"]
print split("aaa_bbb_ccc", "_", 1); // result: ["bbb"]
print split("", "_");               // result: [""]
print split("a__b", "_");           // result: ["a","","b"]
print split("aabbcc", "bb");        // result: ["aa","cc"]
```

### <a name="strcat"></a>strcat

串連字串引數 (支援 1-16 個引數)。

```
strcat("string1", "string2", "string3")
```

#### <a name="examples"></a>範例
```Kusto
print strcat("hello", " ", "world") // result: "hello world"
```


### <a name="strlen"></a>strlen

傳回字串的長度。

```
strlen("text_to_evaluate")
```

#### <a name="examples"></a>範例
```Kusto
print strlen("hello")   // result: 5
```


### <a name="substring"></a>substring

從指定索引開始擷取給定來源字串中的子字串。 (選擇性) 您可以指定所要求子字串的長度。

```
substring(source, startingIndex [, length])
```

- `source` - 要從中擷取子字串的來源字串。
- `startingIndex` - 所要求子字串以零為基底的起始字元位置。
- `length` - 可用來指定所要求傳回子字串長度的選擇性參數。

#### <a name="examples"></a>範例
```Kusto
print substring("abcdefg", 1, 2);   // result: "bc"
print substring("123456", 1);       // result: "23456"
print substring("123456", 2, 2);    // result: "34"
print substring("ABCD", 0, 2);  // result: "AB"
```


### <a name="tolower-toupper"></a>tolower、toupper

將給定字串轉換為全小寫或全大寫。

```
tolower("value")
toupper("value")
```

#### <a name="examples"></a>範例
```Kusto
print tolower("HELLO"); // result: "hello"
print toupper("hello"); // result: "HELLO"
```

## <a name="date-and-time-operations"></a>日期與時間作業
下列各節會提供範例，說明如何使用 Kusto 查詢語言的日期和時間值。

### <a name="date-time-basics"></a>日期時間基礎
Kusto 查詢語言有兩個與日期和時間關聯的主要資料類型：「日期時間」與「時間範圍」。 所有日期都以國際標準時間 (UTC) 表示。 雖然我們支援多種日期時間格式，但建議使用 ISO8601 格式。 

時間範圍是以十進位數加上時間單位來表示：

|縮寫   | 時間單位    |
|:---|:---|
|d           | day          |
|h           | hour         |
|m           | minute       |
|s           | second       |
|ms          | 毫秒  |
|微秒 | 微秒  |
|tick        | 奈秒   |

您可以透過使用 `todatetime` 運算子轉換字串來建立。 例如，若要檢閱在特定時間範圍內傳送的 VM 活動訊號，請使用 `between` 運算子來指定時間範圍。

```Kusto
Heartbeat
| where TimeGenerated between(datetime("2018-06-30 22:46:42") .. datetime("2018-07-01 00:57:27"))
```

另一個常見案例是將日期時間與現在做比較。 例如，若要查看過去兩分鐘內的所有活動訊號，您可以使用 `now` 運算子結合代表兩分鐘的時間範圍：

```Kusto
Heartbeat
| where TimeGenerated > now() - 2m
```

也提供此函式的捷徑：
```Kusto
Heartbeat
| where TimeGenerated > now(-2m)
```

然而，最快且最可讀的方法是使用 `ago` 運算子：
```Kusto
Heartbeat
| where TimeGenerated > ago(2m)
```

假設您不知道開始與結束時間，但知道開始時間與時間長度。 您可以將查詢重寫為下面這樣：

```Kusto
let startDatetime = todatetime("2018-06-30 20:12:42.9");
let duration = totimespan(25m);
Heartbeat
| where TimeGenerated between(startDatetime .. (startDatetime+duration) )
| extend timeFromStart = TimeGenerated - startDatetime
```

### <a name="converting-time-units"></a>轉換時間單位
您也可以使用預設時間單位以外的時間單位來表示日期時間或時間範圍。 例如，如果您正在檢閱過去 30 分鐘內的錯誤事件，而且需要顯示事件在多久之前發生的計算結果欄：

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated 
```

此 `timeAgo` 資料行保留如下的值： "00：09： 31.5118992"，表示這些值的格式為 hh： mm： ss. fffffff。 若您想要將這些值的格式設定為從開始時間算起的分鐘數 `numver`，將該值除以「1 分鐘」即可：

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated
| extend timeAgoMinutes = timeAgo/1m 
```


### <a name="aggregations-and-bucketing-by-time-intervals"></a>依時間間隔的彙總與貯體處理
另一個常見的案例是以特定時間粒紋取得一段特定間期間統計資料的需求。 針對此案例，可以使用 `bin` 運算子做為摘要子句的一部分。

使用下列查詢來取得過去半小時內每 5 分鐘發生的事件數目：

```Kusto
Event
| where TimeGenerated > ago(30m)
| summarize events_count=count() by bin(TimeGenerated, 5m) 
```

此查詢會產生下列表格：  

|TimeGenerated(UTC)|events_count|
|--|--|
|2018-08-01T09:30:00.000|54|
|2018-08-01T09:35:00.000|41|
|2018-08-01T09:40:00.000|42|
|2018-08-01T09:45:00.000|41|
|2018-08-01T09:50:00.000|41|
|2018-08-01T09:55:00.000|16|

建立結果貯體的另一個方式是使用函式，例如 `startofday`：

```Kusto
Event
| where TimeGenerated > ago(4d)
| summarize events_count=count() by startofday(TimeGenerated) 
```

此查詢會產生下列結果：

|timestamp|count_|
|--|--|
|2018-07-28T00:00:00.000|7,136|
|2018-07-29T00:00:00.000|12,315|
|2018-07-30T00:00:00.000|16,847|
|2018-07-31T00:00:00.000|12,616|
|2018-08-01T00:00:00.000|5,416|


### <a name="time-zones"></a>時區
因為所有日期時間值都是國際標準時間 (UTC) 表示，將這些值轉換為當地時區時非常實用。 例如，使用此計算將國際標準時間 (UTC) 轉換為太平洋標準時間 (PST) 時間：

```Kusto
Event
| extend localTimestamp = TimeGenerated - 8h
```

## <a name="aggregations"></a>彙總
下列各節提供使用 Kusto 查詢語言匯總查詢結果的範例。

### <a name="count"></a>count
計算套用任何篩選之後結果集中的列數。 下列範例會傳回過去 30 分鐘內 _Perf_ 表中的總列數。 除非您指定特定欄名，否則結果會以名為 *count_* 的欄傳回：


```Kusto
Perf
| where TimeGenerated > ago(30m) 
| summarize count()
```

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| summarize num_of_records=count() 
```

查看一段時間的趨勢時，時間表視覺效果很實用：

```Kusto
Perf 
| where TimeGenerated > ago(30m) 
| summarize count() by bin(TimeGenerated, 5m)
| render timechart
```

來自此範例的輸出會顯示 5 分鐘間隔的效能記錄計數趨勢線：

![計算趨勢](images/samples/count-trend.png)


### <a name="dcount-dcountif"></a>dcount, dcountif
使用 `dcount` 與 `dcountif` 來計算特定欄中的相異值。 下列查詢會評估過去一小時內傳送活動訊號的相異電腦數：

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcount(Computer)
```

若只要計算傳送活動訊號的 Linux 電腦數，請使用 `dcountif`：

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcountif(Computer, OSType=="Linux")
```

### <a name="evaluating-subgroups"></a>評估子群組
若要為您資料中的子群組執行計算或其他彙總，請使用 `by` 關鍵字。 例如，若要計算每個國家/地區中傳送之心跳的相異 Linux 電腦數目：

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize distinct_computers=dcountif(Computer, OSType=="Linux") by RemoteIPCountry
```

|RemoteIPCountry  | distinct_computers  |
------------------|---------------------|
|美國    | 19                  |
|加拿大           | 3                   |
|愛爾蘭          | 0                   |
|英國   | 0                   |
|荷蘭      | 2                   |


若要分析更小的資料子群組，請將額外欄名加到 `by` 區段。 例如，您可能會想要計算每個國家/地區每個 OSType 的相異電腦數：

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize distinct_computers=dcountif(Computer, OSType=="Linux") by RemoteIPCountry, OSType
```


### <a name="percentile"></a>百分位數
若要尋找中位數，請使用 `percentile` 函式搭配值來指定百分位數：

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 50) by Computer
```

您也可以指定不同的百分位數來取得每個值的彙總結果：

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 25, 50, 75, 90) by Computer
```

這可能會顯示某些電腦 CPU 有相同的中位數，但某些電腦 CPU 都持續接近中位數，而其他電腦則回報低很多與高很多的 CPU 值，表示它們遇到峰值。

### <a name="variance"></a>變異數
若要直接評估值的變異數，請使用標準差與變異數方法：

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), variance(CounterValue) by Computer
```

分析 CPU 使用狀況穩定性的一個好方式是結合 stdev 與中位數計算：

```Kusto
Perf
| where TimeGenerated > ago(130m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), percentiles(CounterValue, 50) by Computer
```

### <a name="generating-lists-and-sets"></a>產生清單與集合
您可以使用 `makelist` 依特定欄中的值順序瀏覽樞紐資料。 例如，您可能想要探索您的機器上發生的最長見訂購事件。 基本上，您能依每部機器上的事件識別碼順序來瀏覽樞紐資料。 

```Kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makelist(EventID) by Computer
```

|電腦|list_EventID|
|---|---|
| computer1 | [704,701,1501,1500,1085,704,704,701] |
| computer2 | [326,105,302,301,300,102] |
| ... | ... |

`makelist` 會產生資料傳遞到其中的順序清單。 若要從最舊到最新為事件排序，請在順序陳述式中使用 `asc`，而非 `desc`. 

建立只包含相異值的清單也很實用。 這稱為「集合」，而且可以使用 `makeset` 來產生：

```Kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makeset(EventID) by Computer
```

|電腦|list_EventID|
|---|---|
| computer1 | [704,701,1501,1500,1085] |
| computer2 | [326,105,302,301,300,102] |
| ... | ... |

就像 `makelist` 一樣，`makeset` 也適用於已排序的資料，而且金會根據傳遞到其中的列順序來產生陣列。

### <a name="expanding-lists"></a>展開清單
`makelist` 或 `makeset` 的反向作業是 `mvexpand`，它會展開值清單以分隔列。 它可以跨任何數目的動態欄 (包括 JSON 與陣列) 展開。 例如，您可以檢查「活動訊號」表格以了解過去一小時內從已傳送活動訊號之電腦傳送資料的解決方案：

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, Solutions
```

| 電腦 | 方案 | 
|--------------|----------------------|
| computer1 | 「安全性」、「更新」、「變更追蹤」 |
| computer2 | 「安全性」、「更新」 |
| computer3 | 「反惡意程式碼」、「變更追蹤」 |
| ... | ... |

使用 `mvexpand` 以在個別列中顯示每個值，而不是顯示逗號分隔清單：

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mvexpand Solutions
```

| 電腦 | 方案 | 
|--------------|----------------------|
| computer1 | 「安全性」 |
| computer1 | 「更新」 |
| computer1 | 「變更追蹤」 |
| computer2 | 「安全性」 |
| computer2 | 「更新」 |
| computer3 | 「反惡意程式碼」 |
| computer3 | 「變更追蹤」 |
| ... | ... |


接著，您可以再次使用 `makelist` 以將項目組成群組，而且這次您會看到每個解決方案的電腦清單：

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mvexpand Solutions
| summarize makelist(Computer) by tostring(Solutions) 
```

|方案 | list_Computer |
|--------------|----------------------|
| 「安全性」 | ["computer1", "computer2"] |
| 「更新」 | ["computer1", "computer2"] |
| 「變更追蹤」 | ["computer1", "computer3"] |
| 「反惡意程式碼」 | ["computer3"] |
| ... | ... |

### <a name="handling-missing-bins"></a>處理遺失的間隔
有用的應用程式 `mvexpand` 是需要在中填入遺漏的 bin 的預設值。例如，假設您要尋找特定機器的執行時間，請流覽其心跳。 您可能也想要查看活動訊號來源，這位於「類別」欄。 一般而言，我們會使用簡單的 summarize 陳述式，如下所示：

```Kusto
Heartbeat
| where TimeGenerated > ago(12h)
| summarize count() by Category, bin(TimeGenerated, 1h)
```

| 類別 | TimeGenerated | count_ |
|--------------|----------------------|--------|
| 直接代理程式 | 2017-06-06T17:00:00Z | 15 |
| 直接代理程式 | 2017-06-06T18:00:00Z | 60 |
| 直接代理程式 | 2017-06-06T20:00:00Z | 55 |
| 直接代理程式 | 2017-06-06T21:00:00Z | 57 |
| 直接代理程式 | 2017-06-06T22:00:00Z | 60 |
| ... | ... | ... |

在這些結果中，雖然與 "2017-06-06T19:00:00Z" 關聯的貯體遺失 (因為那一小時內沒有任何活動訊號資料)。 使用 `make-series` 函式來指派預設值給空白貯體。 這將會為每個類別產生一列，每一列都有四個額外的陣列欄，一個適用於值，而一個適用於比較時間貯體：

```Kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
```

| 類別 | count_ | TimeGenerated |
|---|---|---|
| 直接代理程式 | [15,60,0,55,60,57,60,...] | ["2017-06-06T17:00:00.0000000Z","2017-06-06T18:00:00.0000000Z","2017-06-06T19:00:00.0000000Z","2017-06-06T20:00:00.0000000Z","2017-06-06T21:00:00.0000000Z",...] |
| ... | ... | ... |

*count_* 陣列的第三個元素如預期是 0，而且 _TimeGenerated_ 陣列中有符合的時間戳記項目 "2017-06-06T19:00:00.0000000Z"。 但此陣列格式難以閱讀。 使用 `mvexpand` 來展開陣列，並產生與 `summarize`所產生之格式輸出相同的格式輸出：

```Kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
| mvexpand TimeGenerated, count_
| project Category, TimeGenerated, count_
```

| 類別 | TimeGenerated | count_ |
|--------------|----------------------|--------|
| 直接代理程式 | 2017-06-06T17:00:00Z | 15 |
| 直接代理程式 | 2017-06-06T18:00:00Z | 60 |
| 直接代理程式 | 2017-06-06T19:00:00Z | 0 |
| 直接代理程式 | 2017-06-06T20:00:00Z | 55 |
| 直接代理程式 | 2017-06-06T21:00:00Z | 57 |
| 直接代理程式 | 2017-06-06T22:00:00Z | 60 |
| ... | ... | ... |



### <a name="narrowing-results-to-a-set-of-elements-let-makeset-toscalar-in"></a>將結果縮小為元素集合：`let`、`makeset`、`toscalar`、`in`
常見案例是根據一組條件選取某些特定實體的名稱，然後將不同的資料集篩選為該實體集合。 例如，您可以尋找已知缺少更新的電腦，並找出這些電腦的 IP：


```Kusto
let ComputersNeedingUpdate = toscalar(
    Update
    | summarize makeset(Computer)
    | project set_Computer
);
WindowsFirewall
| where Computer in (ComputersNeedingUpdate)
```

## <a name="joins"></a>聯結
聯結可讓您在相同的查詢中分析多個資料表的資料。 它們會透過比對所指定資料行的值來合併兩個資料集的資料列。


```Kusto
SecurityEvent 
| where EventID == 4624     // sign-in events
| project Computer, Account, TargetLogonId, LogonTime=TimeGenerated
| join kind= inner (
    SecurityEvent 
    | where EventID == 4634     // sign-out events
    | project TargetLogonId, LogoffTime=TimeGenerated
) on TargetLogonId
| extend Duration = LogoffTime-LogonTime
| project-away TargetLogonId1 
| top 10 by Duration desc
```

在此範例中，第一個資料集會篩選所有登入事件。 這與會篩選所有登出事件的第二個資料集聯結。 投影的資料行是 _Computer_、_Account_、_TargetLogonId_ 與 _TimeGenerated_。 兩個資料集會以共用資料行 _TargetLogonId_ 彼此關聯。 輸出是每個關聯的單一記錄，它同時包含登入與登出時間。

若兩個資料集都有具有相同名稱的資料行，系統將會為右側資料集的資料行指定索引編號，因此在此範例中，結果會顯示具有來自左側資料表之值的 _TargetLogonId_，以及具有來自右側資料表之值的 _TargetLogonId1_ 。 在此案例中，第二個 _TargetLogonId1_ 資料行會使用 `project-away` 運算子移除。

> [!NOTE]
> 若要改進效能，請使用 `project` 運算子，只保留聯結資料集的相關資料行。


使用下列語法來聯結兩個資料集，而且聯結的索引鍵在兩個資料表有不同的名稱：
```
Table1
| join ( Table2 ) 
on $left.key1 == $right.key2
```

### <a name="lookup-tables"></a>查閱資料表
聯結的其中一個常用用途是使用值的靜態對應 (使用有助於將結果轉換為更易讀之格式的 `datatable`)。 例如，為每個事件識別碼加上事件名稱以讓安全性事件資料更豐富。

```Kusto
let DimTable = datatable(EventID:int, eventName:string)
  [
    4625, "Account activity",
    4688, "Process action",
    4634, "Account activity",
    4658, "The handle to an object was closed",
    4656, "A handle to an object was requested",
    4690, "An attempt was made to duplicate a handle to an object",
    4663, "An attempt was made to access an object",
    5061, "Cryptographic operation",
    5058, "Key file operation"
  ];
SecurityEvent
| join kind = inner
 DimTable on EventID
| summarize count() by eventName
```

| eventName | count_ |
|:---|:---|
| 物件的控制碼已關閉 | 290995 |
| 已要求物件的控制碼 | 154157 |
| 已嘗試複製物件的控制碼 | 144305 |
| 嘗試存取物件 | 123669 |
| 密碼編譯作業 | 153495 |
| 金鑰檔操作 | 153496 |

## <a name="json-and-data-structures"></a>JSON 與資料結構

巢狀物件是在陣列或機碼值組中包含其他物件的物件。 這些物件是以 JSON 字串來表示。 本節說明如何使用 JSON 來取出資料和分析嵌套物件。

### <a name="working-with-json-strings"></a>處理 JSON 字串
使用 `extractjson` 來存取已知路徑中的特定 JSON 元素。 此功能需要使用下列慣例的路徑運算式。

- _$_ 參考根資料夾
- 使用括弧或點標記法來代表索引與元素，如下列範例中所示。


使用括弧來代表索引，並使用點來分隔元素：

```Kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report
| extend status = extractjson("$.hosts[0].status", hosts_report)
```

這與只使用括弧標記法的結果相同：

```Kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report 
| extend status = extractjson("$['hosts'][0]['status']", hosts_report)
```

若只有一個元素，您可以只使用點標記法：

```Kusto
let hosts_report=dynamic({"location":"North_DC", "status":"running", "rate":5});
print hosts_report 
| extend status = hosts_report.status
```


### <a name="parsejson"></a>parsejson
若要存取您 json 結構中的多個元素，以動態物件方式來存取比較簡單。 使用 `parsejson` 將文字資料轉換為動態物件。 一旦轉換為動態型別，就能使用額外函式來分析資料。

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend status0=hosts_object.hosts[0].status, rate1=hosts_object.hosts[1].rate
```



### <a name="arraylength"></a>arraylength
使用 `arraylength` 來計算陣列中的元素數目：

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend hosts_num=arraylength(hosts_object.hosts)
```

### <a name="mvexpand"></a>mvexpand
使用 `mvexpand` 將物件的屬性分成數列。

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| mvexpand hosts_object.hosts[0]
```

![螢幕擷取畫面顯示 hosts_0，其中包含位置、狀態和比率的值。](images/samples/mvexpand.png)

### <a name="buildschema"></a>buildschema
使用 `buildschema` 來取得認可物件所有值的結構描述：

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```

輸出是 JSON 格式的結構描述：
```json
{
    "hosts":
    {
        "indexer":
        {
            "location": "string",
            "rate": "int",
            "status": "string"
        }
    }
}
```
此輸出描述物件欄位的名稱與其對應的資料類型。 

巢狀物件可能有不同的結構描述，如下列範例中所示：

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"status":"stopped", "rate":"3", "range":100}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```

## <a name="charts"></a>圖表
下列各節提供在 Kusto 查詢語言中使用圖表的範例。

### <a name="chart-the-results"></a>繪製結果圖表
從檢閱過去一小時內每個作業系統類型中有多少部電腦開始：

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| summarize count(Computer) by OSType  
```

根據預設，結果會顯示成資料表︰

![Table](images/samples/table-display.png)

若要取的更好的檢視，請選取 [圖表]，然後選擇 [圓形圖] 選項以將結果視覺化：

![圓形圖](images/samples/charts-and-diagrams-pie.png)


### <a name="timecharts"></a>時間表
顯示 1 小時間隔中的處理器時間平均值、第 50 個百分位數與第 95 個百分位數。 查詢會產生多個查詢，而且您可以接著選取要在時間表上顯示哪些數列：

```Kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
```

選取 **折線圖** 顯示選項：

![折線圖](images/samples/charts-and-diagrams-multiple-series.png)

#### <a name="reference-line"></a>參考線

參考線可協助您輕鬆地識別計量是否超過特定閾值。 若要將線條加到圖表中，請以常數欄延伸資料集：

```Kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
| extend Threshold = 20
```

![參考線](images/samples/charts-and-diagrams-multiple-series-threshold.png)

### <a name="multiple-dimensions"></a>多維度
`summarize` 之 `by` 子句中的多個運算式會在結果中建立多列，每個值組合都各有一列。

```Kusto
SecurityEvent
| where TimeGenerated > ago(1d)
| summarize count() by tostring(EventID), AccountType, bin(TimeGenerated, 1h)
```

當您以圖表方式檢視結果時，它會使用來自 `by` 子句的第一欄。 下列範例顯示使用 _EventID_ 顯示堆疊直條圖。 維度類型必須是 `string`，因此此範例中的 _EventID_ 會被轉換為字串。 

![橫條圖 EventID](images/samples/charts-and-diagrams-multiple-dimension-1.png)

您可以選取欄名下拉式清單以進行切換。 

![橫條圖 AccountType](images/samples/charts-and-diagrams-multiple-dimension-2.png)

## <a name="smart-analytics"></a>智慧分析
本節包含在 Log Analytics 中使用智慧分析功能執行使用者活動分析的範例。 您可以使用這些範例來分析 Application Insights 監視的應用程式，或使用這些查詢中的概念對其他資料進行類似的分析。 

### <a name="cohorts-analytics"></a>世代分析

世代分析追蹤特定使用者群組 (稱為世代) 的活動。 它會嘗試藉由測量傳回使用者的比率來測量服務的吸引力。 使用者會依他們第一次使用服務的時間進行分組。 在分析世代時，我們預期在第一個追蹤期間發現活動減少。 每個世代都是以第一次觀察其成員的當週為標題。

下列範例會分析使用者在第一次使用該服務後的 5 週內執行的活動數目。

```Kusto
let startDate = startofweek(bin(datetime(2017-01-20T00:00:00Z), 1d));
let week = range Cohort from startDate to datetime(2017-03-01T00:00:00Z) step 7d;
// For each user we find the first and last timestamp of activity
let FirstAndLastUserActivity = (end:datetime) 
{
    customEvents
    | where customDimensions["sourceapp"]=="ai-loganalyticsui-prod"
    // Check 30 days back to see first time activity
    | where timestamp > startDate - 30d
    | where timestamp < end      
    | summarize min=min(timestamp), max=max(timestamp) by user_AuthenticatedId
};
let DistinctUsers = (cohortPeriod:datetime, evaluatePeriod:datetime) {
    toscalar (
    FirstAndLastUserActivity(evaluatePeriod)
    // Find members of the cohort: only users that were observed in this period for the first time
    | where min >= cohortPeriod and min < cohortPeriod + 7d  
    // Pick only the members that were active during the evaluated period or after
    | where max > evaluatePeriod - 7d
    | summarize dcount(user_AuthenticatedId)) 
};
week 
| where Cohort == startDate
// Finally, calculate the desired metric for each cohort. In this sample we calculate distinct users but you can change
// this to any other metric that would measure the engagement of the cohort members.
| extend 
    r0 = DistinctUsers(startDate, startDate+7d),
    r1 = DistinctUsers(startDate, startDate+14d),
    r2 = DistinctUsers(startDate, startDate+21d),
    r3 = DistinctUsers(startDate, startDate+28d),
    r4 = DistinctUsers(startDate, startDate+35d)
| union (week | where Cohort == startDate + 7d 
| extend 
    r0 = DistinctUsers(startDate+7d, startDate+14d),
    r1 = DistinctUsers(startDate+7d, startDate+21d),
    r2 = DistinctUsers(startDate+7d, startDate+28d),
    r3 = DistinctUsers(startDate+7d, startDate+35d) )
| union (week | where Cohort == startDate + 14d 
| extend 
    r0 = DistinctUsers(startDate+14d, startDate+21d),
    r1 = DistinctUsers(startDate+14d, startDate+28d),
    r2 = DistinctUsers(startDate+14d, startDate+35d) )
| union (week | where Cohort == startDate + 21d 
| extend 
    r0 = DistinctUsers(startDate+21d, startDate+28d),
    r1 = DistinctUsers(startDate+21d, startDate+35d) ) 
| union (week | where Cohort == startDate + 28d 
| extend 
    r0 = DistinctUsers (startDate+28d, startDate+35d) )
// Calculate the retention percentage for each cohort by weeks
| project Cohort, r0, r1, r2, r3, r4,
          p0 = r0/r0*100,
          p1 = todouble(r1)/todouble (r0)*100,
          p2 = todouble(r2)/todouble(r0)*100,
          p3 = todouble(r3)/todouble(r0)*100,
          p4 = todouble(r4)/todouble(r0)*100 
| sort by Cohort asc
```
此範例會導致下列輸出。

:::image type="content" source="images/samples/cohorts.png" alt-text="世代分析輸出":::

### <a name="rolling-monthly-active-users-and-user-stickiness"></a>每月累積作用中使用者和使用者黏著度
下列範例會使用 [series_fir](/azure/kusto/query/series-firfunction) 函數進行時間序列分析，該函數允許您執行滑動視窗計算。 受監視的範例應用程式是線上商店，透過自訂的事件追蹤使用者的活動。 該查詢會追蹤兩種類型的使用者活動，_AddToCart_ 和 _Checkout_，並將 _作用中使用者_ 定義為在指定日期內至少執行一次結帳的使用者。



```Kusto
let endtime = endofday(datetime(2017-03-01T00:00:00Z));
let window = 60d;
let starttime = endtime-window;
let interval = 1d;
let user_bins_to_analyze = 28;
// Create an array of filters coefficients for series_fir(). A list of '1' in our case will produce a simple sum.
let moving_sum_filter = toscalar(range x from 1 to user_bins_to_analyze step 1 | extend v=1 | summarize makelist(v)); 
// Level of engagement. Users will be counted as engaged if they performed at least this number of activities.
let min_activity = 1;
customEvents
| where timestamp > starttime  
| where customDimensions["sourceapp"] == "ai-loganalyticsui-prod"
// We want to analyze users who actually checked-out in our web site
| where (name == "Checkout") and user_AuthenticatedId <> ""
// Create a series of activities per user
| make-series UserClicks=count() default=0 on timestamp 
    in range(starttime, endtime-1s, interval) by user_AuthenticatedId
// Create a new column containing a sliding sum. 
// Passing 'false' as the last parameter to series_fir() prevents normalization of the calculation by the size of the window.
// For each time bin in the *RollingUserClicks* column, the value is the aggregation of the user activities in the 
// 28 days that preceded the bin. For example, if a user was active once on 2016-12-31 and then inactive throughout 
// January, then the value will be 1 between 2016-12-31 -> 2017-01-28 and then 0s. 
| extend RollingUserClicks=series_fir(UserClicks, moving_sum_filter, false)
// Use the zip() operator to pack the timestamp with the user activities per time bin
| project User_AuthenticatedId=user_AuthenticatedId , RollingUserClicksByDay=zip(timestamp, RollingUserClicks)
// Transpose the table and create a separate row for each combination of user and time bin (1 day)
| mvexpand RollingUserClicksByDay
| extend Timestamp=todatetime(RollingUserClicksByDay[0])
// Mark the users that qualify according to min_activity
| extend RollingActiveUsersByDay=iff(toint(RollingUserClicksByDay[1]) >= min_activity, 1, 0)
// And finally, count the number of users per time bin.
| summarize sum(RollingActiveUsersByDay) by Timestamp
// First 28 days contain partial data, so we filter them out.
| where Timestamp > starttime + 28d
// render as timechart
| render timechart
```

此範例會導致下列輸出。

:::image type="content" source="images/samples/rolling-mau.png" alt-text="每月累積使用者輸出":::

下列範例會將上述查詢轉換成可重複使用的函式，並使用它來計算輪流使用者的貼上。 此查詢中的作用中使用者僅定義為在指定日期至少執行一次結帳的使用者。

``` Kusto
let rollingDcount = (sliding_window_size: int, event_name:string)
{
    let endtime = endofday(datetime(2017-03-01T00:00:00Z));
    let window = 90d;
    let starttime = endtime-window;
    let interval = 1d;
    let moving_sum_filter = toscalar(range x from 1 to sliding_window_size step 1 | extend v=1| summarize makelist(v));    
    let min_activity = 1;
    customEvents
    | where timestamp > starttime
    | where customDimensions["sourceapp"]=="ai-loganalyticsui-prod"
    | where (name == event_name)
    | where user_AuthenticatedId <> ""
    | make-series UserClicks=count() default=0 on timestamp 
        in range(starttime, endtime-1s, interval) by user_AuthenticatedId
    | extend RollingUserClicks=fir(UserClicks, moving_sum_filter, false)
    | project User_AuthenticatedId=user_AuthenticatedId , RollingUserClicksByDay=zip(timestamp, RollingUserClicks)
    | mvexpand RollingUserClicksByDay
    | extend Timestamp=todatetime(RollingUserClicksByDay[0])
    | extend RollingActiveUsersByDay=iff(toint(RollingUserClicksByDay[1]) >= min_activity, 1, 0)
    | summarize sum(RollingActiveUsersByDay) by Timestamp
    | where Timestamp > starttime + 28d
};
// Use the moving_sum_filter with bin size of 28 to count MAU.
rollingDcount(28, "Checkout")
| join
(
    // Use the moving_sum_filter with bin size of 1 to count DAU.
    rollingDcount(1, "Checkout")
)
on Timestamp
| project sum_RollingActiveUsersByDay1 *1.0 / sum_RollingActiveUsersByDay, Timestamp
| render timechart
```

此範例會導致下列輸出。

:::image type="content" source="images/samples/user-stickiness.png" alt-text="使用者黏著度輸出":::

### <a name="regression-analysis"></a>迴歸分析
此範例示範如何僅根據應用程式追蹤記錄，針對服務中斷建立自動化的偵測器。 偵測器會搜尋應用程式中相對少量錯誤和警告追蹤的異常突然增加。

兩種技術用來根據追蹤記錄資料評估服務狀態：

- 使用 [make-series](/azure/kusto/query/make-seriesoperator) 將半結構化文字追蹤記錄轉換為表示正追蹤行和負追蹤行之間比率的計量。
- 使用 [series_fit_2lines](/azure/kusto/query/series-fit-2linesfunction) 和 [series_fit_line](/azure/kusto/query/series-fit-linefunction) 進行進階步驟跳躍偵測 (使用時間序列分析和 2 行線性迴歸)。

``` Kusto
let startDate = startofday(datetime("2017-02-01"));
let endDate = startofday(datetime("2017-02-07"));
let minRsquare = 0.8;  // Tune the sensitivity of the detection sensor. Values close to 1 indicate very low sensitivity.
// Count all Good (Verbose + Info) and Bad (Error + Fatal + Warning) traces, per day
traces
| where timestamp > startDate and timestamp < endDate
| summarize 
    Verbose = countif(severityLevel == 0),
    Info = countif(severityLevel == 1), 
    Warning = countif(severityLevel == 2),
    Error = countif(severityLevel == 3),
    Fatal = countif(severityLevel == 4) by bin(timestamp, 1d)
| extend Bad = (Error + Fatal + Warning), Good = (Verbose + Info)
// Determine the ratio of bad traces, from the total
| extend Ratio = (todouble(Bad) / todouble(Good + Bad))*10000
| project timestamp , Ratio
// Create a time series
| make-series RatioSeries=any(Ratio) default=0 on timestamp in range(startDate , endDate -1d, 1d) by 'TraceSeverity' 
// Apply a 2-line regression to the time series
| extend (RSquare2, SplitIdx, Variance2,RVariance2,LineFit2)=series_fit_2lines(RatioSeries)
// Find out if our 2-line is trending up or down
| extend (Slope,Interception,RSquare,Variance,RVariance,LineFit)=series_fit_line(LineFit2)
// Check whether the line fit reaches the threshold, and if the spike represents an increase (rather than a decrease)
| project PatternMatch = iff(RSquare2 > minRsquare and Slope>0, "Spike detected", "No Match")
```


## <a name="next-steps"></a>後續步驟

- 逐步解說[Kusto 查詢語言的教學](tutorial.md?pivots=azuremonitor)課程。


::: zone-end

