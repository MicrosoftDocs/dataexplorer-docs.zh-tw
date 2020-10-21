---
title: 範例-Azure 資料總管
description: 本文說明 Azure 資料總管中的範例。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0db8c472ed3b23a1bf46f8fce9cbd38b0ca960b3
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251028"
---
# <a name="samples"></a>範例

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


|City|SessionId|StartTime|StopTime|Duration|
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

:::image type="content" source="images/samples/040.png" alt-text="直條圖的螢幕擷取畫面。Y 軸的範圍是從0到50左右。十個彩色資料行描繪10個位置的個別值。"::: 

新增程式碼，以方便調整大小的容器計算持續時間。在此範例中，因為橫條圖的喜好設定，請除以 `1s` 以將時間範圍轉換為數字。 

```
    // Count the frequency of each duration:
    | summarize count() by duration=bin(min_duration/1s, 10) 
      // Cut off the long tail:
    | where duration < 300
      // Display in a bar chart:
    | sort by duration asc | render barchart 
```

:::image type="content" source="images/samples/050.png" alt-text="直條圖的螢幕擷取畫面。Y 軸的範圍是從0到50左右。十個彩色資料行描繪10個位置的個別值。":::

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
2. 判斷每一筆記錄的範圍結尾-_bin + 7d，除非這超出 _ (開始、結束) _ 範圍，在此情況下會進行調整。 
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
