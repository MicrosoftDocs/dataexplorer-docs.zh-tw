---
title: Azure 資料總管和 Azure 監視器中的查詢範例
description: 本文會針對 Azure 資料總管和 Azure 監視器來說明使用 Kusto 查詢語言的常見查詢和範例。
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
ms.openlocfilehash: 866df577fa039e8b92c31753197cf4a4fa03f139
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "95783449"
---
# <a name="samples-for-queries-for-azure-data-explorer-and-azure-monitor"></a>Azure 資料總管和 Azure 監視器的查詢範例

::: zone pivot="azuredataexplorer"

本文會指出 Azure 資料總管中的常見查詢需求，以及要如何使用 Kusto 查詢語言來滿足這些需求。

## <a name="display-a-column-chart"></a>顯示直條圖

若要投射兩個以上的資料行，然後使用資料行作為圖表的 X 軸和 Y 軸：

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto 
StormEvents
| where isnotempty(EndLocation) 
| summarize event_count=count() by EndLocation
| top 10 by event_count
| render columnchart
```

* 第一個資料行會形成 X 軸。 其可以是數字、日期時間或字串。 
* 使用 `where`、`summarize` 和 `top` 來限制您顯示的資料量。
* 將結果排序以定義 X 軸的順序。

:::image type="content" source="images/samples/color-bar-chart.png" alt-text="直條圖的螢幕擷取畫面，其中有十個彩色直條，分別描繪 10 個地點的值。":::

## <a name="get-sessions-from-start-and-stop-events"></a>從開始和停止事件取得工作階段

在事件記錄中，某些事件會標記延伸活動或工作階段的開始或結束。 

|名稱|City|SessionId|時間戳記|
|---|---|---|---|
|開始|London|2817330|2015-12-09T10:12:02.32|
|Game|London|2817330|2015-12-09T10:12:52.45|
|開始|曼徹斯特|4267667|2015-12-09T10:14:02.23|
|停止|London|2817330|2015-12-09T10:23:43.18|
|取消|曼徹斯特|4267667|2015-12-09T10:27:26.29|
|停止|曼徹斯特|4267667|2015-12-09T10:28:31.72|

每個事件都有工作階段識別碼 (`SessionId`)。 困難之處在於要如何匹配開始和停止事件與工作階段識別碼。

範例：

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

若要匹配開始和停止事件與工作階段識別碼：

1. 使用 [let](./letstatement.md) 來命名資料表的投射，以在開始聯結之前盡可能地精簡資料表。
1. 使用 [project](./projectoperator.md) 來變更時間戳記的名稱，讓開始時間和停止時間都出現在結果中。 `project` 也會選取要在結果中檢視的其他直條。 
1. 使用 [join](./joinoperator.md) 來匹配相同活動的開始和停止項目。 每個活動都會建立一個資料列。 
1. 再次使用 `project` 來新增資料行以顯示活動的持續時間。

輸出如下：

|City|SessionId|StartTime|StopTime|持續時間|
|---|---|---|---|---|
|London|2817330|2015-12-09T10:12:02.32|2015-12-09T10:23:43.18|00:11:40.46|
|曼徹斯特|4267667|2015-12-09T10:14:02.23|2015-12-09T10:28:31.72|00:14:29.49|

## <a name="get-sessions-without-using-a-session-id"></a>不使用工作階段識別碼而取得工作階段

假設開始和停止事件，沒有可方便我們執行匹配作業的工作階段識別碼。 但我們有工作階段發生所在用戶端的 IP 位址。 假設每個用戶端位址一次只會進行一個工作階段，我們可以將來自相同 IP 位址的每個開始事件與下一個停止事件進行匹配：

範例：

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

`join` 會將來自相同用戶端 IP 位址的開始時間與所有停止時間進行匹配。 範例程式碼會：

- 移除與稍早停止時間的匹配。
- 以開始時間和 IP 位址分組，來取得每個工作階段的群組。 
- 為 `StartTime` 參數提供 `bin` 函式。 如果您未執行此步驟，Kusto 會自動使用一小時的間隔，而讓某些開始時間與錯誤的停止時間進行匹配。

`arg_min` 會尋找每個群組中持續時間最小的資料列，而 `*` 參數則會穿過其他所有資料行。 

引數會在每個資料行名稱前面加上 `min_` 前置詞。 

:::image type="content" source="images/samples/start-stop-ip-address-table.png" alt-text="螢幕擷取畫面：列出結果的資料表，內有每個用戶端/開始時間組合的「開始時間」、「用戶端 IP」、「持續時間」、「城市」、「最早停止」資料行。"::: 

新增程式碼，從而以方便調整大小的間隔來計算持續時間。在此範例中，由於偏好使用長條圖，所以會除以 `1s` 以將時間範圍轉換成數字：

```
    // Count the frequency of each duration:
    | summarize count() by duration=bin(min_duration/1s, 10) 
      // Cut off the long tail:
    | where duration < 300
      // Display in a bar chart:
    | sort by duration asc | render barchart 
```

:::image type="content" source="images/samples/number-of-sessions-bar-chart.png" alt-text="直條圖的螢幕擷取畫面，其中指出工作階段數目以及指定範圍內的持續時間。":::

### <a name="full-example"></a>完整範例

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

假設您有活動資料表以及活動的開始和結束時間。 您可以顯示一個圖表來顯示一段時間內有多少個同時執行的活動。

以下是範例輸入，稱為 `X`：

|SessionId | StartTime | StopTime |
|---|---|---|
| a | 10:01:03 | 10:10:08 |
| b | 10:01:29 | 10:03:10 |
| c | 10:03:02 | 10:05:20 |

在間隔為一分鐘的圖表中，您想要計算每個一分鐘間隔內的每個執行中活動。

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

您不必保留這些陣列，而是可以使用[mv-expand](./mvexpandoperator.md) 來加以展開：

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

現在，依範例時間將結果分組，並計算每個活動的出現次數：

```kusto
X
| mv-expand samples = range(bin(StartTime, 1m), StopTime , 1m)
| summarize count(SessionId) by bin(todatetime(samples),1m)
```

* 使用 `todatetime()`，因為 [mv-expand](./mvexpandoperator.md) 會產生動態類型的資料行。
* 使用 `bin()`，因為對於數值和日期來說，如果您未提供間隔，則 `summarize` 一律會使用預設間隔來套用 `bin()` 函式。 

輸出如下：

| count_SessionId | 範例|
|---|---|
| 1 | 10:01:00|
| 2 | 10:02:00|
| 3 | 10:03:00|
| 2 | 10:04:00|
| 1 | 10:05:00|
| 1 | 10:06:00|

您可以使用長條圖或時間圖來呈現結果。

## <a name="introduce-null-bins-into-summarize"></a>在 *summarize* 中引進 Null 間隔

當 `summarize` 運算子套用到包含日期時間資料行的群組索引鍵時，請將這些值的間隔設為固定寬度的間隔：

```kusto
let StartTime=ago(12h);
let StopTime=now()
T
| where Timestamp > StartTime and Timestamp <= StopTime 
| where ...
| summarize Count=count() by bin(Timestamp, 5m)
```

這個範例會產生一個資料表，其在 `T` (落在長度為五分鐘的每個間隔內) 中的每個資料列群組都會有一個資料列。

程式碼不會執行的動作是新增「Null 間隔」，也就是在 `T` 中沒有對應資料列的 `StartTime` 和 `StopTime` 之間的時間間隔值。 使用這些間隔來「填補」資料表是不錯的方法。下面為您提供一個執行方法：

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

下面會逐步說明前述查詢： 

1. 使用 `union` 運算子在資料表中新增更多資料列。 這些資料列是由 `union` 運算式所產生的。
1. `range` 運算子會產生具有單一資料列和資料行的資料表。 除了供 `mv-expand` 使用外，資料表沒有任何其他用途。
1. `range` 函式上的 `mv-expand` 運算子會建立與 `StartTime` 和 `EndTime` 之間的五分鐘間隔數量一樣多的資料列。
1. 使用數量為 `0` 的 `Count`。
1. `summarize` 運算子會將來自 `union` 原始 (左側或外部) 引數的間隔群組在一起。 此運算子也會群組來自其內部引數的間隔 (Null 間隔資料列)。 此程序可確保輸出中每個值為零或為原始計數的間隔都有一個資料列。

## <a name="get-more-from-your-data-by-using-kusto-with-machine-learning"></a>搭配使用 Kusto 與機器學習以充分利用資料 

許多有趣的使用案例都會使用機器學習演算法，並從遙測資料衍生出有趣的見解。 這些演算法往往需要以結構嚴謹的資料集作為其輸入。 原始記錄資料通常不符合所需的結構和大小。 

一開始請先尋找特定 Bing 推斷服務錯誤率中的異常。 記錄資料表具有 650 億筆記錄。 下列基本查詢會篩選 25 萬個錯誤，然後建立使用異常偵測函式 [series_decompose_anomalies](series-decompose-anomaliesfunction.md) 的錯誤計數時間序列。 異常會由 Kusto 服務加以偵測，並於時間序列圖表上以紅點醒目提示。

```kusto
Logs
| where Timestamp >= datetime(2015-08-22) and Timestamp < datetime(2015-08-23) 
| where Level == "e" and Service == "Inferences.UnusualEvents_Main" 
| summarize count() by bin(Timestamp, 5min)
| render anomalychart 
```

服務已發現幾個有可疑錯誤率的時間值區。 請使用 Kusto 來放大此時間範圍。 然後，執行彙總在 `Message` 資料行上的查詢。 請嘗試找出最常見的錯誤。 

我們會剪出整個訊息堆疊追蹤的相關部分，以便讓結果更加適合頁面大小。 

您可以看到我們已成功發現八大錯誤。 但接下來則是一長串的錯誤，因為錯誤訊息是使用包含變化資料的格式字串加以建立的：

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
|7125|'RunCycleFromInterimData' 方法的 ExecuteAlgorithmMethod 失敗...
|7125|InferenceHostService 呼叫失敗..System.NullReferenceException:物件參考未設定為物件的執行個體...
|7124|未預期的推斷系統錯誤..System.NullReferenceException:物件參考未設定為物件的執行個體... 
|5112|未預期的推斷系統錯誤..System.NullReferenceException:物件參考未設定為物件的執行個體..
|174|InferenceHostService 呼叫失敗..System.ServiceModel.CommunicationException:寫入到管道時發生錯誤:...
|10|'RunCycleFromInterimData' 方法的 ExecuteAlgorithmMethod 失敗...
|10|推斷系統錯誤..Microsoft.Bing.Platform.Inferences.Service.Managers.UserInterimDataManagerException:...
|3|InferenceHostService 呼叫失敗..System.ServiceModel.CommunicationObjectFaultedException:...
|1|推斷系統錯誤...SocialGraph.BOSS.OperationResponse...AIS TraceId:8292FC561AC64BED8FA243808FE74EFD...
|1|推斷系統錯誤...SocialGraph.BOSS.OperationResponse...AIS TraceId:5F79F7587FF943EC9B641E02E701AFBF...

此時，使用 `reduce` 運算子會有幫助。 此運算子已發現源自程式碼中相同追蹤檢測點的 63 個不同錯誤。 `reduce` 有助於將焦點放在該時間範圍內的其他有意義錯誤追蹤。

```kusto
Logs
| where Timestamp >= datetime(2015-08-22 05:00) and Timestamp < datetime(2015-08-22 06:00)
| where Level == "e" and Service == "Inferences.UnusualEvents_Main"
| reduce by Message with threshold=0.35
| project Count, Pattern
```

|計數|模式
|---|---
|7125|'RunCycleFromInterimData' 方法的 ExecuteAlgorithmMethod 失敗...
|  7125|InferenceHostService 呼叫失敗..System.NullReferenceException:物件參考未設定為物件的執行個體...
|  7124|未預期的推斷系統錯誤..System.NullReferenceException:物件參考未設定為物件的執行個體...
|  5112|未預期的推斷系統錯誤..System.NullReferenceException:物件參考未設定為物件的執行個體...
|  174|InferenceHostService 呼叫失敗..System.ServiceModel.CommunicationException:寫入到管道時發生錯誤:...
|  63|推斷系統錯誤..Microsoft.Bing.Platform.Inferences.\*:撰寫 \* 以寫入到物件 BOSS.\*:SocialGraph.BOSS.Reques...
|  10|'RunCycleFromInterimData' 方法的 ExecuteAlgorithmMethod 失敗...
|  10|推斷系統錯誤..Microsoft.Bing.Platform.Inferences.Service.Managers.UserInterimDataManagerException:...
|  3|InferenceHostService 呼叫失敗..System.ServiceModel.\*:The object, System.ServiceModel.Channels.\*+\*, for \*\* is the \*... at Syst...

現在，您可以清楚檢視造成所偵測到異常的前幾大錯誤。

若要了解這些錯誤對整個範例系統的影響，請考量： 
* `Logs` 資料表包含其他維度資料，例如 `Component` 和 `Cluster`。
* 新的 autocluster 外掛程式可以使用簡單的查詢來協助衍生元件和叢集見解。 

在下列範例中，您可以清楚看到前四大錯誤都是元件特有的。 此外，雖然前三大錯誤是 DB4 叢集特有的，但所有叢集都有發生第四大錯誤。

```kusto
Logs
| where Timestamp >= datetime(2015-08-22 05:00) and Timestamp < datetime(2015-08-22 06:00)
| where Level == "e" and Service == "Inferences.UnusualEvents_Main"
| evaluate autocluster()
```

|計數 |百分比 (%)|元件|叢集|訊息
|---|---|---|---|---
|7125|26.64|InferenceHostService|DB4|方法的 ExecuteAlgorithmMethod...
|7125|26.64|未知的元件|DB4|InferenceHostService 呼叫失敗...
|7124|26.64|InferenceAlgorithmExecutor|DB4|未預期的推斷系統錯誤...
|5112|19.11|InferenceAlgorithmExecutor|*|未預期的推斷系統錯誤...

## <a name="map-values-from-one-set-to-another"></a>將值從某個集合對應到另一個集合

常見的查詢使用案例是值的靜態對應。 靜態對應有助於讓結果更美觀。

例如，在下一個資料表中，`DeviceModel` 會指定裝置型號。 使用裝置型號並非方便用來參考裝置名稱的形式。  

|DeviceModel |計數 
|---|---
|iPhone5,1 |32 
|iPhone3,2 |432 
|iPhone7,2 |55 
|iPhone5,2 |66 

 使用自訂名稱會更方便：  

|FriendlyName |計數 
|---|---
|iPhone 5 |32 
|iPhone 4 |432 
|iPhone 6 |55 
|iPhone5 |66 

接下來的兩個範例會示範如何從使用裝置型號變更為使用自訂名稱來識別裝置。  

### <a name="map-by-using-a-dynamic-dictionary"></a>使用動態字典來進行對應

您可以使用動態字典和動態存取子來實現對應。 例如：

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
// Dataset definition
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

### <a name="map-by-using-a-static-table"></a>使用靜態資料表來進行對應

您也可以使用永續性資料表和 `join` 運算子來實現對應。
 
1. 只建立對應資料表一次：

    ```kusto
    .create table Devices (DeviceModel: string, FriendlyName: string) 

    .ingest inline into table Devices 
        ["iPhone5,1","iPhone 5"]["iPhone3,2","iPhone 4"]["iPhone7,2","iPhone 6"]["iPhone5,2","iPhone5"]
    ```

1. 建立裝置內容的資料表：

    |DeviceModel |FriendlyName 
    |---|---
    |iPhone5,1 |iPhone 5 
    |iPhone3,2 |iPhone 4 
    |iPhone7,2 |iPhone 6 
    |iPhone5,2 |iPhone5 

1. 建立測試資料表來源：

    ```kusto
    .create table Source (DeviceModel: string, Count: int)

    .ingest inline into table Source ["iPhone5,1",32]["iPhone3,2",432]["iPhone7,2",55]["iPhone5,2",66]
    ```

1. 聯結資料表並執行專案：

   ```kusto
   Devices  
   | join (Source) on DeviceModel  
   | project FriendlyName, Count
   ```

輸出如下：

|FriendlyName |計數 
|---|---
|iPhone 5 |32 
|iPhone 4 |432 
|iPhone 6 |55 
|iPhone5 |66 


## <a name="create-and-use-query-time-dimension-tables"></a>建立和使用查詢時間維度資料表

您往往會想要將查詢結果與未儲存在資料庫中的特定維度資料表聯結在一起。 您可以定義運算式，讓其結果為範圍限定於單一查詢的資料表。 

例如：

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

## <a name="retrieve-the-latest-records-by-timestamp-per-identity"></a>擷取每個身分識別的最新記錄 (依時間戳記)

假設您有一個資料表，其中包含：
* `ID` 資料行，可識別每個資料列所關聯的實體，例如使用者識別碼或節點識別碼
* `timestamp` 資料行，可提供資料列的時間參考
* 其他資料行

您可以使用 [top-nested 運算子](topnestedoperator.md)來進行查詢，以傳回每個 `ID` 資料行值的最新兩筆記錄，其中 _最新_ 的定義是 _具有最高的 `timestamp` 值_：

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


下面會逐步說明前述查詢 (編號指的是程式碼註解中的編號)：

1. `datatable` 是為了示範而產生一些測試資料的方式。 一般來說，您會在這裡使用實際資料。
1. 這一行基本上表示 _會傳回 `id` 的所有相異值_。 
1. 接著會針對最大化下列項目的前兩大記錄傳回這一行：
     * `timestamp` 資料行
     * 前述層級的資料行 (在這裡就只是 `id`)
     * 在此層級指定的資料行 (在此為 `timestamp`)
1. 這一行會針對前述層級所傳回的每筆記錄新增 `bla` 資料行的值。 如果您對資料表內的其他資料行有興趣，則可以針對每個資料行重複這一行程式碼。
1. 最後一行程式碼會使用 [project-away 運算子](projectawayoperator.md)來移除 `top-nested` 所引進的「額外」資料行。

## <a name="extend-a-table-by-a-percentage-of-the-total-calculation"></a>以總計算的百分比來擴充資料表

包含數值資料行的表格式運算式如果伴隨著總計百分比形式的值，則會對使用者更有用。

例如，假設查詢會產生下列資料表：

|SomeSeries|SomeInt|
|----------|-------|
|Apple       |    100|
|Banana       |    200|

您想要讓資料表顯示如下：

|SomeSeries|SomeInt|Pct |
|----------|-------|----|
|Apple       |    100|33.3|
|Banana       |    200|66.6|

若要變更資料表的顯示方式，請計算 `SomeInt` 資料行的總計 (總和)，然後將此資料行的每個值除以總計。 若要獲得任意結果，請使用 [as 運算子](asoperator.md)。

例如：

```kusto
// The following table literally represents a long calculation
// that ends up with an anonymous tabular value:
datatable (SomeInt:int, SomeSeries:string) [
  100, "Apple",
  200, "Banana",
]
// We now give this calculation a name ("X"):
| as X
// Having this name we can refer to it in the sub-expression
// "X | summarize sum(SomeInt)":
| extend Pct = 100 * bin(todouble(SomeInt) / toscalar(X | summarize sum(SomeInt)), 0.001)
```

## <a name="perform-aggregations-over-a-sliding-window"></a>對滑動視窗執行彙總

下列範例顯示如何使用滑動視窗來彙總資料行。 針對查詢，請使用下表，其包含依時間戳記排列的水果價格。

使用為期七天的滑動視窗來計算每日每種水果的最小、最大和總和成本。 結果集中的每一筆記錄都會彙總過去七天，而結果則會針對分析期間內的每一天包含一筆記錄。

水果資料表：

|時間戳記|水果|價格|
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

以下是滑動視窗的彙總查詢。 請參閱查詢結果後面的說明。

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

輸出如下：

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

查詢會將輸入資料表中的每筆記錄「伸展」(重複) 於其實際出現後的七天內。 每筆記錄實際上會出現七次。 因此，每日彙總會包含過去七天的所有記錄。


下面會逐步說明前述查詢： 

1. 將每一筆記錄的間隔設定為一天 (相對於 `_start`)。 
1. 判斷每一筆記錄的範圍結尾：`_bin + 7d`，除非該值超出 `_start` 和 `_end` 的範圍，在此情況下則會進行調整。 
1. 針對每一筆記錄，建立從目前記錄當天開始的七天 (時間戳記) 陣列。 
1. 對陣列執行 `mv-expand`，從而將每一筆記錄複製成七筆記錄，每一天彼此分離。 
1. 針對每一天執行彙總函式。 由於 #4，此步驟實際上會彙總 _過去_ 七天。 
1. 前七天的資料不完整，因為前七天沒有七天的回顧期間。 前七天會從最終結果中排除。 在此範例中，前七天只會參與 2018-10-01 的彙總。

## <a name="find-the-preceding-event"></a>尋找先前的事件

下一個範例示範如何在兩個資料集之間尋找先前的事件。  

您有兩個資料集：A 和 B。對於資料集 B 中的每筆記錄，請在資料集 A 中尋找其先前的事件 (也就是 A 中仍然比 B _還舊_ 的 `arg_max` 記錄)。

以下是範例資料集： 

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
|2019-01-01 00:00:00.0000000|x|Ax1|
|2019-01-01 00:00:00.0000000|z|Az1|
|2019-01-01 00:00:01.0000000|x|Ax2|
|2019-01-01 00:00:02.0000000|y|Ay1|
|2019-01-01 00:00:05.0000000|y|Ay2|

</br>

|時間戳記|識別碼|EventA|
|---|---|---|
|2019-01-01 00:00:03.0000000|x|B|
|2019-01-01 00:00:04.0000000|x|B|
|2019-01-01 00:00:04.0000000|y|B|
|2019-01-01 00:02:00.0000000|z|B|

預期輸出： 

|識別碼|時間戳記|EventB|A_Timestamp|EventA|
|---|---|---|---|---|
|x|2019-01-01 00:00:03.0000000|B|2019-01-01 00:00:01.0000000|Ax2|
|x|2019-01-01 00:00:04.0000000|B|2019-01-01 00:00:01.0000000|Ax2|
|y|2019-01-01 00:00:04.0000000|B|2019-01-01 00:00:02.0000000|Ay1|
|z|2019-01-01 00:02:00.0000000|B|2019-01-01 00:00:00.0000000|Az1|

針對此問題，我們建議了兩種不同的方法。 您可以在特定資料集上測試這兩種方法，以找出最適合您案例的應用程式。

> [!NOTE] 
> 在不同的資料集上，每種方法可能會以不同的方式執行。

### <a name="approach-1"></a>方法 1

這個方法會依識別碼和時間戳記將這兩個資料集序列化。 然後，會將資料集 B 中的所有事件以及其所有先前的事件群組到資料集 A 中。最後，會從群組中資料集 A 內的所有事件挑選出 `arg_max`。

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

### <a name="approach-2"></a>方法 2

這個解決問題的方法需要最大的回顧期間。 此方法會查看相較於資料集 B，資料集 A 中的記錄可能會多 _舊_。然後，此方法會根據識別碼和此回顧期間來聯結兩個資料集。

`join` 會產生所有可能的候選項目，即比資料集 B 中的記錄還舊且在回顧期間內的所有資料集 A 記錄。 然後，會透過 `arg_min (TimestampB - TimestampA)` 篩選出最接近資料集 B 的記錄。 回顧期間越短，查詢結果就越好。

在下列範例中，回顧期間設定為 `1m`。 識別碼為 `z` 的記錄沒有對應的 `A` 事件，因為其 `A` 事件舊了兩分鐘。

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

|識別碼|B_Timestamp|A_Timestamp|EventB|EventA|
|---|---|---|---|---|
|x|2019-01-01 00:00:03.0000000|2019-01-01 00:00:01.0000000|B|Ax2|
|x|2019-01-01 00:00:04.0000000|2019-01-01 00:00:01.0000000|B|Ax2|
|y|2019-01-01 00:00:04.0000000|2019-01-01 00:00:02.0000000|B|Ay1|
|z|2019-01-01 00:02:00.0000000||B||


## <a name="next-steps"></a>後續步驟

- 逐步完成[關於 Kusto 查詢語言的教學課程](tutorial.md?pivots=azuredataexplorer)。

::: zone-end

::: zone pivot="azuremonitor"

本文會指出 Azure 監視器中的常見查詢需求，以及要如何使用 Kusto 查詢語言來滿足這些需求。

## <a name="string-operations"></a>字串作業

下列各節會舉例說明如何在使用 Kusto 查詢語言時使用字串。

### <a name="strings-and-how-to-escape-them"></a>字串及其逸出方式

字串值會用單引號或雙引號括住。 請在字元左邊加上反斜線 (\\) 以將字元逸出：加上 `\t` 來逸出定位字元、加上 `\n` 來逸出新行字元，以及加上 `\"` 來逸出單引號字元。

```kusto
print "this is a 'string' literal in double \" quotes"
```

```kusto
print 'this is a "string" literal in single \' quotes'
```

若要防止 "\\" 被視為逸出字元，請將 "\@" 新增為字串的前置詞：

```kusto
print @"C:\backslash\not\escaped\with @ prefix"
```


### <a name="string-comparisons"></a>字串比較

運算子       |描述                         |區分大小寫|範例 (結果為 `true`)
---------------|------------------------------------|--------------|-----------------------
`==`           |等於                              |是           |`"aBc" == "aBc"`
`!=`           |不等於                          |是           |`"abc" != "ABC"`
`=~`           |等於                              |否            |`"abc" =~ "ABC"`
`!~`           |不等於                          |否            |`"aBc" !~ "xyz"`
`has`          |右側值是左側值中的完整字詞 |否|`"North America" has "america"`
`!has`         |右側值不是左側值中的完整字詞       |否            |`"North America" !has "amer"` 
`has_cs`       |右側值是左側值中的完整字詞 |是|`"North America" has_cs "America"`
`!has_cs`      |右側值不是左側值中的完整字詞       |是            |`"North America" !has_cs "amer"` 
`hasprefix`    |右側值是左側值中的字詞前置詞         |否            |`"North America" hasprefix "ame"`
`!hasprefix`   |右側值不是左側值中的字詞前置詞     |否            |`"North America" !hasprefix "mer"` 
`hasprefix_cs`    |右側值是左側值中的字詞前置詞         |是            |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs`   |右側值不是左側值中的字詞前置詞     |是            |`"North America" !hasprefix_cs "CA"` 
`hassuffix`    |右側值是左側值中的字詞後置詞         |否            |`"North America" hassuffix "ica"`
`!hassuffix`   |右側值不是左側值中的字詞後置詞     |否            |`"North America" !hassuffix "americ"`
`hassuffix_cs`    |右側值是左側值中的字詞後置詞         |是            |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs`   |右側值不是左側值中的字詞後置詞     |是            |`"North America" !hassuffix_cs "icA"`
`contains`     |右側值會顯示為左側值的子序列  |否            |`"FabriKam" contains "BRik"`
`!contains`    |右側值不會出現在左側值中           |否            |`"Fabrikam" !contains "xyz"`
`contains_cs`   |右側值會顯示為左側值的子序列  |是           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |右側值不會出現在左側值中           |是           |`"Fabrikam" !contains_cs "Kam"`
`startswith`   |右側值是左側值的開頭子序列|否            |`"Fabrikam" startswith "fab"`
`!startswith`  |右側值不是左側值的開頭子序列|否        |`"Fabrikam" !startswith "kam"`
`startswith_cs`   |右側值是左側值的開頭子序列|是            |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`  |右側值不是左側值的開頭子序列|是        |`"Fabrikam" !startswith_cs "fab"`
`endswith`     |右側值是左側值的結尾子序列|否             |`"Fabrikam" endswith "Kam"`
`!endswith`    |右側值不是左側值的結尾子序列|否         |`"Fabrikam" !endswith "brik"`
`endswith_cs`     |右側值是左側值的結尾子序列|是             |`"Fabrikam" endswith "Kam"`
`!endswith_cs`    |右側值不是左側值的結尾子序列|是         |`"Fabrikam" !endswith "brik"`
`matches regex`|左側值包含右值值的相符項目|是           |`"Fabrikam" matches regex "b.*k"`
`in`           |等於其中一個元素       |是           |`"abc" in ("123", "345", "abc")`
`!in`          |不等於任何元素   |是           |`"bca" !in ("123", "345", "abc")`


### <a name="countof"></a>*countof*

計算子字串在字串內的出現次數。 可以匹配純文字字串，或使用規則運算式 (RegEx)。 純文字字串相符項目可能會重疊，但 RegEx 相符項目不會重疊。

```
countof(text, search [, kind])
```

- `text`：輸入字串 
- `search`：文字內要匹配的純字串或 RegEx
- `kind`：_normal_ | _regex_ (預設值：normal)。

傳回搜尋字串可在容器中匹配的次數。 純文字字串相符項目可能會重疊，但 RegEx 相符項目不會重疊。

#### <a name="plain-string-matches"></a>純文字比對

```kusto
print countof("The cat sat on the mat", "at");  //result: 3
print countof("aaa", "a");  //result: 3
print countof("aaaa", "aa");  //result: 3 (not 2!)
print countof("ababa", "ab", "normal");  //result: 2
print countof("ababa", "aba");  //result: 2
```

#### <a name="regex-matches"></a>規則運算式比對

```kusto
print countof("The cat sat on the mat", @"\b.at\b", "regex");  //result: 3
print countof("ababa", "aba", "regex");  //result: 1
print countof("abcabc", "a.c", "regex");  // result: 2
```


### <a name="extract"></a>*extract*

從特定字串取得規則運算式的相符項目。 (選擇性) 可以將擷取的字串轉換為指定類型。

```kusto
extract(regex, captureGroup, text [, typeLiteral])
```

- `regex`：規則運算式。
- `captureGroup`：正整數常數，會指出要擷取的擷取群組。 使用 0 代表整個相符項目、1 代表規則運算式中第一個 \(\) 所相符的值，2 或以上的數字代表後續的括號。
- `text` - 要搜尋的字串。
- `typeLiteral` - 選擇性的類型常值 (例如 `typeof(long)`)。 如果提供，所擷取的子字串會轉換為此類型。

傳回針對指定擷取群組 `captureGroup` 進行匹配的子字串，並選擇性地轉換為 `typeLiteral`。 如果沒有相符項目或類型轉換失敗，則傳回 Null。

下列範例會從活動訊號記錄擷取 `ComputerIP` 的最後一個八位元：

```kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| project ComputerIP, last_octet=extract("([0-9]*$)", 1, ComputerIP) 
```

下列範例會擷取最後一個八位元並將其轉換為 *real* 類型 (數字)，然後計算下一個 IP 值：

```kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| extend last_octet=extract("([0-9]*$)", 1, ComputerIP, typeof(real)) 
| extend next_ip=(last_octet+1)%255
| project ComputerIP, last_octet, next_ip
```

在下一個範例中，會搜尋字串 `Trace` 以取得 `Duration` 的定義。 相符項目會轉換為 `real` 並乘以時間常數 (1 秒)，然後將 `Duration` 轉換為類型 `timespan`。

```kusto
let Trace="A=12, B=34, Duration=567, ...";
print Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real));  //result: 567
print Duration_seconds =  extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s);  //result: 00:09:27
```


### <a name="isempty-isnotempty-notempty"></a>*isempty*、*isnotempty*、*notempty*

- 如果引數為空字串或 null，則 `isempty` 會傳回 `true` (請參閱 `isnull`)。
- 如果引數不是空字串或 null，則 `isnotempty` 會傳回 `true` (請參閱 `isnotnull`)。 別名：`notempty`。


```kusto
isempty(value)
isnotempty(value)
```

#### <a name="example"></a>範例

```kusto
print isempty("");  // result: true

print isempty("0");  // result: false

print isempty(0);  // result: false

print isempty(5);  // result: false

Heartbeat | where isnotempty(ComputerIP) | take 1  // return 1 Heartbeat record in which ComputerIP isn't empty
```


### <a name="parseurl"></a>*parseurl*

將 URL 分割成其組件 (例如通訊協定、主機和連接埠)，然後以字串形式傳回包含組件的字典物件。

```
parseurl(urlstring)
```

#### <a name="example"></a>範例

```kusto
print parseurl("http://user:pass@contoso.com/icecream/buy.aspx?a=1&b=2#tag")
```

輸出如下：

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

### <a name="replace"></a>*replace*

用另一個字串取代所有規則運算式相符項目。 

```
replace(regex, rewrite, input_text)
```

- `regex`：要據以比對的規則運算式。 其可以在 \(\) 中包含擷取群組。
- `rewrite`：針對由進行比對之規則運算式所進行之比對的取代規則運算式。 使用 \0 來代表整個相符項目、\1 來代表第一個擷取群組，\2 和以上的數字來代表後續的擷取群組。
- `input_text`：要在其中搜尋的輸入字串。

傳回以重寫的評估取代規則運算式的所有相符項目後所形成的文字。 相符項目不會重疊。

#### <a name="example"></a>範例

```kusto
SecurityEvent
| take 1
| project Activity 
| extend replaced = replace(@"(\d+) -", @"Activity ID \1: ", Activity) 
```

輸出如下：

活動                                        |已取代
------------------------------------------------|----------------------------------------------------------
4663 - 已嘗試存取物件  |活動識別碼 4663：已嘗試存取物件。


### <a name="split"></a>*分割*

根據指定的分隔符號分割特定字串，然後傳回結果子字串的陣列。

```
split(source, delimiter [, requestedIndex])
```

- `source`：將根據指定的分隔符號分割的字串。
- `delimiter`：將會用來分割來源字串的分隔符號。
- `requestedIndex`：選擇性的基底為零的索引。 若已提供，傳回的字串陣列將只保有該項目 (若存在)。


#### <a name="example"></a>範例

```kusto
print split("aaa_bbb_ccc", "_");    // result: ["aaa","bbb","ccc"]
print split("aa_bb", "_");          // result: ["aa","bb"]
print split("aaa_bbb_ccc", "_", 1); // result: ["bbb"]
print split("", "_");               // result: [""]
print split("a__b", "_");           // result: ["a","","b"]
print split("aabbcc", "bb");        // result: ["aa","cc"]
```

### <a name="strcat"></a>*strcat*

串連字串引數 (支援 1-16 個引數)。

```
strcat("string1", "string2", "string3")
```

#### <a name="example"></a>範例

```kusto
print strcat("hello", " ", "world") // result: "hello world"
```


### <a name="strlen"></a>*strlen*

傳回字串的長度。

```
strlen("text_to_evaluate")
```

#### <a name="example"></a>範例

```kusto
print strlen("hello")   // result: 5
```


### <a name="substring"></a>*substring*

從指定索引開始擷取特定來源字串中的子字串。 (選擇性) 您可以指定所要求子字串的長度。

```
substring(source, startingIndex [, length])
```

- `source`：要從中擷取子字串的來源字串。
- `startingIndex`：所要求子字串以零為基底的起始字元位置。
- `length`：您可以用來指定所要求傳回子字串長度的選擇性參數。

#### <a name="example"></a>範例

```kusto
print substring("abcdefg", 1, 2);   // result: "bc"
print substring("123456", 1);       // result: "23456"
print substring("123456", 2, 2);    // result: "34"
print substring("ABCD", 0, 2);  // result: "AB"
```


### <a name="tolower-toupper"></a>*tolower*、*toupper*

將特定字串轉換為全部小寫或全部大寫。

```
tolower("value")
toupper("value")
```

#### <a name="example"></a>範例

```kusto
print tolower("HELLO"); // result: "hello"
print toupper("hello"); // result: "HELLO"
```

## <a name="date-and-time-operations"></a>日期與時間作業

下列各節會舉例說明如何在使用 Kusto 查詢語言時使用日期和時間值。

### <a name="date-time-basics"></a>日期時間基礎

Kusto 查詢語言有兩個與日期和時間關聯的主要資料類型：`datetime` 與 `timespan`。 所有日期都以國際標準時間 (UTC) 表示。 雖然我們支援多種日期時間格式，但建議使用 ISO-8601 格式。 

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

您可以使用 `todatetime` 運算子來轉換字串，藉以建立日期時間值。 例如，若要檢閱在特定時間範圍內傳送的 VM 活動訊號，請使用 `between` 運算子來指定時間範圍：

```kusto
Heartbeat
| where TimeGenerated between(datetime("2018-06-30 22:46:42") .. datetime("2018-07-01 00:57:27"))
```

另一個常見案例是將日期時間值與現在做比較。 例如，若要查看過去兩分鐘內的所有活動訊號，您可以使用 `now` 運算子結合代表兩分鐘的時間範圍：

```kusto
Heartbeat
| where TimeGenerated > now() - 2m
```

也提供此函式的捷徑：

```kusto
Heartbeat
| where TimeGenerated > now(-2m)
```

最快且最可讀的方法是使用 `ago` 運算子：

```kusto
Heartbeat
| where TimeGenerated > ago(2m)
```

假設您不知道開始與結束時間，但知道開始時間與時間長度。 您可以重寫查詢：

```kusto
let startDatetime = todatetime("2018-06-30 20:12:42.9");
let duration = totimespan(25m);
Heartbeat
| where TimeGenerated between(startDatetime .. (startDatetime+duration) )
| extend timeFromStart = TimeGenerated - startDatetime
```

### <a name="convert-time-units"></a>轉換時間單位

您也可以使用預設時間單位以外的時間單位來表示日期時間或時間範圍值。 例如，如果您正在檢閱過去 30 分鐘內的錯誤事件，而且需要顯示事件在多久之前發生的計算結果欄，則可以使用此查詢：

```kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated 
```

`timeAgo` 資料行包含 `00:09:31.5118992` 之類的值，其格式為 hh:mm:ss.fffffff。 若您想要將這些值的格式設定為從開始時間算起的分鐘數 `number`，將該值除以 `1m` 即可：

```kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated
| extend timeAgoMinutes = timeAgo/1m 
```

### <a name="aggregations-and-bucketing-by-time-intervals"></a>依時間間隔的彙總與貯體處理

另一個常見的案例是需要在採用特定時間單位的特定時段內，取得其統計資料。 在此案例中，您可以使用 `bin` 運算子作為 `summarize` 子句的一部分。

使用下列查詢來取得過去半小時內每五分鐘發生的事件數目：

```kusto
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

```kusto
Event
| where TimeGenerated > ago(4d)
| summarize events_count=count() by startofday(TimeGenerated) 
```

輸出如下：

|timestamp|count_|
|--|--|
|2018-07-28T00:00:00.000|7,136|
|2018-07-29T00:00:00.000|12,315|
|2018-07-30T00:00:00.000|16,847|
|2018-07-31T00:00:00.000|12,616|
|2018-08-01T00:00:00.000|5,416|


### <a name="time-zones"></a>時區

因為所有日期時間值都是國際標準時間 (UTC) 表示，將這些值轉換為當地時區時非常實用。 例如，使用此計算將國際標準時間 (UTC) 轉換為太平洋標準時間 (PST) 時間：

```kusto
Event
| extend localTimestamp = TimeGenerated - 8h
```

## <a name="aggregations"></a>彙總

下列各節會舉例說明如何在使用 Kusto 查詢語言時彙總查詢的結果。

### <a name="count"></a>*計數*

計算套用任何篩選之後結果集中的列數。 下列範例會傳回過去 30 分鐘內 `Perf` 資料表中的總列數。 除非您將特定名稱指派給資料行，否則會在名為 `count_` 的資料行中傳回結果：


```kusto
Perf
| where TimeGenerated > ago(30m) 
| summarize count()
```

```kusto
Perf
| where TimeGenerated > ago(30m) 
| summarize num_of_records=count() 
```

查看一段時間的趨勢時，時間表視覺效果很實用：

```kusto
Perf 
| where TimeGenerated > ago(30m) 
| summarize count() by bin(TimeGenerated, 5m)
| render timechart
```

此範例的輸出會以五分鐘的間隔顯示 `Perf` 記錄的計數趨勢線：


:::image type="content" source="images/samples/perf-count-line-chart.png" alt-text="折線圖的螢幕擷取畫面，此圖會以五分鐘的間隔顯示 Perf 記錄的計數趨勢線。":::

### <a name="dcount-dcountif"></a>*dcount*、*dcountif*

使用 `dcount` 與 `dcountif` 來計算特定欄中的相異值。 下列查詢會評估過去一小時內傳送活動訊號的相異電腦數：

```kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcount(Computer)
```

若只要計算傳送活動訊號的 Linux 電腦數，請使用 `dcountif`：

```kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcountif(Computer, OSType=="Linux")
```

### <a name="evaluate-subgroups"></a>評估子群組

若要為您資料中的子群組執行計算或其他彙總，請使用 `by` 關鍵字。 例如，若要計算在每個國家/地區或區域傳送活動訊號的相異 Linux 電腦數目，請使用此查詢：

```kusto
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


若要分析更小的資料子群組，請將資料行名稱新增到 `by` 區段。 例如，您可以計算每個國家/地區或區域中，每種作業系統類型 (`OSType`) 的不同電腦數目：

```kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize distinct_computers=dcountif(Computer, OSType=="Linux") by RemoteIPCountry, OSType
```


### <a name="percentile"></a>百分位數

若要尋找中位數，請使用 `percentile` 函式搭配值來指定百分位數：

```kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 50) by Computer
```

您也可以指定不同的百分位數來取得每個值的彙總結果：

```kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 25, 50, 75, 90) by Computer
```

結果可能會顯示某些電腦 CPU 具有類似的中位數。 不過，雖然某些電腦穩定在中位數，但某些則會回報低很多和高很多的 CPU 值。 高低值表示電腦經歷了尖峰。

### <a name="variance"></a>變異數

若要直接評估值的變異數，請使用標準差與變異數方法：

```kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), variance(CounterValue) by Computer
```

分析 CPU 使用狀況穩定性的一個好方式是結合 `stdev` 與中位數計算：

```kusto
Perf
| where TimeGenerated > ago(130m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), percentiles(CounterValue, 50) by Computer
```

### <a name="generate-lists-and-sets"></a>產生清單和集合

您可以使用 `makelist` 依特定資料行中的值順序瀏覽樞紐資料。 例如，您可能想要探索電腦上發生的最常見排序事件。 基本上，您能依每部機器上的 `EventID` 順序來瀏覽樞紐資料： 

```kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makelist(EventID) by Computer
```

輸出如下：

|電腦|list_EventID|
|---|---|
| computer1 | [704,701,1501,1500,1085,704,704,701] |
| computer2 | [326,105,302,301,300,102] |
| ... | ... |

`makelist` 會產生資料傳遞到其中的順序清單。 若要從最舊到最新為事件排序，請在 `order` 陳述式中使用 `asc`，而非 `desc`。 

您可能會發現其適合用來建立只有相異值的清單。 此清單稱為 _集合_，您可以使用 `makeset` 命令來加以產生：

```kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makeset(EventID) by Computer
```

輸出如下：

|電腦|list_EventID|
|---|---|
| computer1 | [704,701,1501,1500,1085] |
| computer2 | [326,105,302,301,300,102] |
| ... | ... |

如同 `makelist`，`makeset` 也適用於已排序的資料。 `makeset` 命令會根據傳入的資料列順序來產生陣列。

### <a name="expand-lists"></a>展開清單

`makelist` 或 `makeset` 的反向操作是 `mv-expand`。 `mv-expand` 命令會將值清單展開為個別資料列。 其可以跨任何數目的動態資料行 (包括 JSON 與陣列資料行) 展開。 例如，您可以檢查 `Heartbeat` 資料表，以取得有從過去一小時傳送過活動訊號的電腦傳送資料的解決方案：

```kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, Solutions
```

輸出如下：

| 電腦 | 方案 | 
|--------------|----------------------|
| computer1 | 「安全性」、「更新」、「變更追蹤」 |
| computer2 | 「安全性」、「更新」 |
| computer3 | 「反惡意程式碼」、「變更追蹤」 |
| ... | ... |

使用 `mv-expand` 以在個別列中顯示每個值，而不是在逗號分隔清單中顯示：

```kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mv-expand Solutions
```

輸出如下：

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


您可以使用 `makelist` 將項目群組在一起。 在輸出中，您可以看到每個解決方案的電腦清單：

```kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mv-expand Solutions
| summarize makelist(Computer) by tostring(Solutions) 
```

輸出如下：

|方案 | list_Computer |
|--------------|----------------------|
| 「安全性」 | ["computer1", "computer2"] |
| 「更新」 | ["computer1", "computer2"] |
| 「變更追蹤」 | ["computer1", "computer3"] |
| 「反惡意程式碼」 | ["computer3"] |
| ... | ... |

### <a name="missing-bins"></a>遺漏間隔

`mv-expand` 的實用應用是為遺漏間隔填入預設值。例如，假設您正在透過探索特定電腦的活動訊號以尋找其運作時間。 您可能也想要查看活動訊號來源，這位於 `Category` 資料行。 一般來說，我們會使用基本的 `summarize` 陳述式：

```kusto
Heartbeat
| where TimeGenerated > ago(12h)
| summarize count() by Category, bin(TimeGenerated, 1h)
```

輸出如下：

| 類別 | TimeGenerated | count_ |
|--------------|----------------------|--------|
| 直接代理程式 | 2017-06-06T17:00:00Z | 15 |
| 直接代理程式 | 2017-06-06T18:00:00Z | 60 |
| 直接代理程式 | 2017-06-06T20:00:00Z | 55 |
| 直接代理程式 | 2017-06-06T21:00:00Z | 57 |
| 直接代理程式 | 2017-06-06T22:00:00Z | 60 |
| ... | ... | ... |

在輸出中，雖然與 "2017-06-06T19:00:00Z" 關聯的貯體遺失 (因為那一小時內沒有任何活動訊號資料)。 使用 `make-series` 函式來指派預設值給空白貯體。 每個類別會產生一個資料列。 輸出中包含兩個額外的陣列資料行，一個用於值，另一個用於比對時間值區：

```kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
```

輸出如下：

| 類別 | count_ | TimeGenerated |
|---|---|---|
| 直接代理程式 | [15,60,0,55,60,57,60,...] | ["2017-06-06T17:00:00.0000000Z","2017-06-06T18:00:00.0000000Z","2017-06-06T19:00:00.0000000Z","2017-06-06T20:00:00.0000000Z","2017-06-06T21:00:00.0000000Z",...] |
| ... | ... | ... |

一如預期，*count_* 陣列的第三個元素是 0。 _TimeGenerated_ 陣列具有相符的時間戳記 "2017-06-06T19:00:00.0000000Z"。 但此陣列格式難以閱讀。 使用 `mv-expand` 來展開陣列，並產生與 `summarize`所產生之格式輸出相同的格式輸出：

```kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
| mv-expand TimeGenerated, count_
| project Category, TimeGenerated, count_
```

輸出如下：

| 類別 | TimeGenerated | count_ |
|--------------|----------------------|--------|
| 直接代理程式 | 2017-06-06T17:00:00Z | 15 |
| 直接代理程式 | 2017-06-06T18:00:00Z | 60 |
| 直接代理程式 | 2017-06-06T19:00:00Z | 0 |
| 直接代理程式 | 2017-06-06T20:00:00Z | 55 |
| 直接代理程式 | 2017-06-06T21:00:00Z | 57 |
| 直接代理程式 | 2017-06-06T22:00:00Z | 60 |
| ... | ... | ... |



### <a name="narrow-results-to-a-set-of-elements-let-makeset-toscalar-in"></a>將結果縮小為一組元素：*let*、*makeset*、*toscalar*、*in*

常見案例是根據一組條件選取特定實體的名稱，然後將不同的資料集篩選為該實體集合。 例如，您可以尋找已知缺少更新的電腦，並找出這些電腦的 IP 位址。

以下是範例：

```kusto
let ComputersNeedingUpdate = toscalar(
    Update
    | summarize makeset(Computer)
    | project set_Computer
);
WindowsFirewall
| where Computer in (ComputersNeedingUpdate)
```

## <a name="joins"></a>聯結

您可以使用聯結在相同的查詢中分析多個資料表的資料。 聯結會透過比對所指定資料行的值來合併兩個資料集的資料列。

以下是範例：

```kusto
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

在此範例中，第一個資料集會篩選所有登入事件。 該資料集與會篩選所有登出事件的第二個資料集聯結。 預計的資料行是 `Computer`、`Account`、`TargetLogonId` 和 `TimeGenerated`。 兩個資料集會以共用資料行 `TargetLogonId` 彼此關聯。 輸出是每個關聯的單一記錄，其同時包含登入與登出時間。

如果這兩個資料集擁有的資料行同名，則會為右側資料集的資料行提供索引編號。 在此範例中，結果會顯示值來自左側資料表的 `TargetLogonId`，以及值來自右側資料表的 `TargetLogonId1`。 在此案例中，第二個 `TargetLogonId1` 資料行會使用 `project-away` 運算子移除。

> [!NOTE]
> 若要改進效能，請使用 `project` 運算子，只保留聯結資料集的相關資料行。


使用下列語法來聯結兩個資料集，其中已聯結的索引鍵在兩個資料表有不同的名稱：

```
Table1
| join ( Table2 ) 
on $left.key1 == $right.key2
```

### <a name="lookup-tables"></a>查閱資料表

聯結的常見用法是使用 `datatable` 來進行靜態值對應。 使用 `datatable` 有助於讓結果更美觀。 例如，您可以為每個事件識別碼加上事件名稱以讓安全性事件資料更豐富：

```kusto
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

輸出如下：

| eventName | count_ |
|:---|:---|
| 物件的控制代碼已關閉 | 290,995 |
| 已要求物件的控制代碼 | 154,157 |
| 已嘗試複製物件的控制代碼 | 144,305 |
| 已嘗試存取物件 | 123,669 |
| 密碼編譯作業 | 153,495 |
| 金鑰檔作業 | 153,496 |

## <a name="json-and-data-structures"></a>JSON 與資料結構

巢狀物件是在機碼值組的陣列或對應中包含其他物件的物件。 這些物件會以 JSON 字串表示。 本節說明如何使用 JSON 來擷取資料及分析巢狀物件。

### <a name="work-with-json-strings"></a>處理 JSON 字串

使用 `extractjson` 來存取已知路徑中的特定 JSON 元素。 此功能需要使用下列慣例的路徑運算式：

- 使用 _$_ 來代表根資料夾。
- 使用括弧或點標記法來代表索引與元素，如下列範例中所示。


使用括弧來代表索引，並使用點來分隔元素：

```kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report
| extend status = extractjson("$.hosts[0].status", hosts_report)
```

這個範例很類似，但其只會使用括弧標記法：

```kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report 
| extend status = extractjson("$['hosts'][0]['status']", hosts_report)
```

若只有一個元素，則只能使用點標記法：

```kusto
let hosts_report=dynamic({"location":"North_DC", "status":"running", "rate":5});
print hosts_report 
| extend status = hosts_report.status
```


### <a name="parsejson"></a>*parsejson*

以動態物件的形式來存取 JSON 結構中的多個元素是最簡單的方式。 使用 `parsejson` 將文字資料轉換為動態物件。 將 JSON 轉換為動態類型後，即可使用其他函式來分析資料。

```kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend status0=hosts_object.hosts[0].status, rate1=hosts_object.hosts[1].rate
```

### <a name="arraylength"></a>*arraylength*

使用 `arraylength` 來計算陣列中的元素數目：

```kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend hosts_num=arraylength(hosts_object.hosts)
```

### <a name="mv-expand"></a>*mv-expand*

使用 `mv-expand` 將物件的屬性分成數列：

```kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| mv-expand hosts_object.hosts[0]
```

:::image type="content" source="images/samples/mvexpand-rows.png" alt-text="螢幕擷取畫面：顯示有位置、狀態和速率值的 hosts_0。":::

### <a name="buildschema"></a>*buildschema*

使用 `buildschema` 來取得認可物件所有值的結構描述：

```kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```

結果是 JSON 格式的結構描述：

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

此結構描述會說明物件欄位的名稱與其對應的資料類型。 

巢狀物件可能有不同的結構描述，如下列範例中所示：

```kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"status":"stopped", "rate":"3", "range":100}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```

## <a name="charts"></a>圖表

下列各節會舉例說明如何在使用 Kusto 查詢語言時使用圖表。

### <a name="chart-the-results"></a>繪製結果的圖表

一開始請先檢閱過去一小時內每個作業系統的電腦數目：

```kusto
Heartbeat
| where TimeGenerated > ago(1h)
| summarize count(Computer) by OSType  
```

根據預設，結果會顯示成資料表︰

:::image type="content" source="images/samples/query-results-table.png" alt-text="以資料表顯示查詢結果的螢幕擷取畫面。":::

如需更有用的檢視，請選取 [圖表]，然後選取 [圓形圖] 選項來將結果視覺化：

:::image type="content" source="images/samples/query-results-pie-chart.png" alt-text="以圓形圖顯示查詢結果的螢幕擷取畫面。":::

### <a name="timecharts"></a>時間表

顯示一小時間隔中的處理器時間平均值、第 50 個百分位數與第 95 個百分位數。 

下列查詢會產生多個數列。 在結果中，您可以選擇要在時間圖中顯示的數列。

```kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
```

選取 [折線圖] 圖表顯示選項：

:::image type="content" source="images/samples/multiple-series-line-chart.png" alt-text="顯示多數列折線圖的螢幕擷取畫面。":::

#### <a name="reference-line"></a>參考線

參考線可協助您輕鬆地識別計量是否超過特定閾值。 若要將線條新增到圖表中，請新增常數資料行來擴充資料集：

```kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
| extend Threshold = 20
```

:::image type="content" source="images/samples/multiple-series-threshold-line-chart.png" alt-text="螢幕擷取畫面：顯示具有閾值參考線的多數列折線圖。":::


### <a name="multiple-dimensions"></a>多維度

`summarize` 的 `by` 子句中的多個運算式會在結果中建立多個資料列。 每個值的組合都會建立一個資料列。

```kusto
SecurityEvent
| where TimeGenerated > ago(1d)
| summarize count() by tostring(EventID), AccountType, bin(TimeGenerated, 1h)
```

當您以圖表方式檢視結果時，圖表會使用來自 `by` 子句的第一欄。 下列範例顯示使用 `EventID` 值建立的堆疊直條圖。 維度必須是 `string` 類型。 在此範例中，`EventID` 值會轉換成 `string`：

:::image type="content" source="images/samples/select-column-chart-type-eventid.png" alt-text="螢幕擷取畫面：顯示以 EventID 資料行為基礎的長條圖。":::

您可以選取資料行名稱的下拉箭號，以切換不同資料行：

:::image type="content" source="images/samples/select-column-chart-type-accounttype.png" alt-text="螢幕擷取畫面：顯示以 AccountType 資料行為基礎的長條圖，並可看見資料行選取器。":::

## <a name="smart-analytics"></a>智慧分析

本節包含的範例會使用 Azure Log Analytics 中的智慧分析功能來分析使用者活動。 您可以使用這些範例來分析 Azure Application Insights 監視的應用程式，或使用這些查詢中的概念對其他資料進行類似的分析。 

### <a name="cohorts-analytics"></a>世代分析

世代分析追蹤特定使用者群組 (稱為 _世代_) 的活動。 世代分析會嘗試藉由測量傳回使用者的比率來測量服務的吸引力。 使用者會依其第一次使用服務的時間進行分組。 在分析世代時，我們預期在第一個追蹤期間發現活動減少。 每個世代都是以第一次觀察其成員的當週為標題。

下列範例會分析使用者第一次使用服務後的五週內完成的活動數目：

```kusto
let startDate = startofweek(bin(datetime(2017-01-20T00:00:00Z), 1d));
let week = range Cohort from startDate to datetime(2017-03-01T00:00:00Z) step 7d;
// For each user, we find the first and last timestamp of activity
let FirstAndLastUserActivity = (end:datetime) 
{
    customEvents
    | where customDimensions["sourceapp"]=="ai-loganalyticsui-prod"
    // Check 30 days back to see first time activity.
    | where timestamp > startDate - 30d
    | where timestamp < end      
    | summarize min=min(timestamp), max=max(timestamp) by user_AuthenticatedId
};
let DistinctUsers = (cohortPeriod:datetime, evaluatePeriod:datetime) {
    toscalar (
    FirstAndLastUserActivity(evaluatePeriod)
    // Find members of the cohort: only users that were observed in this period for the first time.
    | where min >= cohortPeriod and min < cohortPeriod + 7d  
    // Pick only the members that were active during the evaluated period or after.
    | where max > evaluatePeriod - 7d
    | summarize dcount(user_AuthenticatedId)) 
};
week 
| where Cohort == startDate
// Finally, calculate the desired metric for each cohort. In this sample, we calculate distinct users but you can change
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

輸出如下：

:::image type="content" source="images/samples/cohorts-table.png" alt-text="螢幕擷取畫面：顯示以活動為基礎的世代資料表。":::

### <a name="rolling-monthly-active-users-and-user-stickiness"></a>每月累積作用中使用者和使用者黏著度

下列範例會搭配使用時間序列分析與 [series_fir](/azure/kusto/query/series-firfunction) 函式。 您可以使用 `series_fir` 函式來計算滑動視窗。 受監視的範例應用程式是線上商店，透過自訂的事件追蹤使用者的活動。 此查詢會追蹤兩種類型的使用者活動：`AddToCart` 和 `Checkout`。 其對於作用中使用者的定義是在特定一天完成至少一次簽出的使用者。

```kusto
let endtime = endofday(datetime(2017-03-01T00:00:00Z));
let window = 60d;
let starttime = endtime-window;
let interval = 1d;
let user_bins_to_analyze = 28;
// Create an array of filters coefficients for series_fir(). A list of '1' in our case will produce a simple sum.
let moving_sum_filter = toscalar(range x from 1 to user_bins_to_analyze step 1 | extend v=1 | summarize makelist(v)); 
// Level of engagement. Users will be counted as engaged if they completed at least this number of activities.
let min_activity = 1;
customEvents
| where timestamp > starttime  
| where customDimensions["sourceapp"] == "ai-loganalyticsui-prod"
// We want to analyze users who actually checked out in our website.
| where (name == "Checkout") and user_AuthenticatedId <> ""
// Create a series of activities per user.
| make-series UserClicks=count() default=0 on timestamp 
    in range(starttime, endtime-1s, interval) by user_AuthenticatedId
// Create a new column that contains a sliding sum. 
// Passing 'false' as the last parameter to series_fir() prevents normalization of the calculation by the size of the window.
// For each time bin in the *RollingUserClicks* column, the value is the aggregation of the user activities in the 
// 28 days that preceded the bin. For example, if a user was active once on 2016-12-31 and then inactive throughout 
// January, then the value will be 1 between 2016-12-31 -> 2017-01-28 and then 0s. 
| extend RollingUserClicks=series_fir(UserClicks, moving_sum_filter, false)
// Use the zip() operator to pack the timestamp with the user activities per time bin.
| project User_AuthenticatedId=user_AuthenticatedId , RollingUserClicksByDay=zip(timestamp, RollingUserClicks)
// Transpose the table and create a separate row for each combination of user and time bin (1 day).
| mv-expand RollingUserClicksByDay
| extend Timestamp=todatetime(RollingUserClicksByDay[0])
// Mark the users that qualify according to min_activity.
| extend RollingActiveUsersByDay=iff(toint(RollingUserClicksByDay[1]) >= min_activity, 1, 0)
// And finally, count the number of users per time bin.
| summarize sum(RollingActiveUsersByDay) by Timestamp
// First 28 days contain partial data, so we filter them out.
| where Timestamp > starttime + 28d
// Render as timechart.
| render timechart
```

輸出如下：

:::image type="content" source="images/samples/rolling-monthly-active-users-chart.png" alt-text="圖表的螢幕擷取畫面，逐日顯示一個月中的滾動作用中使用者。":::

下列範例會將上述查詢轉換成可重複使用的函式。 然後，此範例會使用查詢來計算滾動使用者的黏著度。 根據定義，此查詢中的作用中使用者是指在特定一天內至少完成一次簽出的使用者。

```kusto
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
    | mv-expand RollingUserClicksByDay
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

輸出如下：

:::image type="content" source="images/samples/user-stickiness-chart.png" alt-text="顯示一段時間內使用者黏著度的圖表螢幕擷取畫面。":::

### <a name="regression-analysis"></a>迴歸分析

此範例示範如何僅根據應用程式追蹤記錄，針對服務中斷建立自動化的偵測器。 偵測器會搜尋應用程式中相對少量錯誤和警告追蹤的異常突然增加。

兩種技術用來根據追蹤記錄資料評估服務狀態：

- 使用 [make-series](/azure/kusto/query/make-seriesoperator) 將半結構化文字追蹤記錄轉換為表示正追蹤行和負追蹤行之間比率的計量。
- 使用 [series_fit_2lines](/azure/kusto/query/series-fit-2linesfunction) 和 [series_fit_line](/azure/kusto/query/series-fit-linefunction) 進行進階步驟跳躍偵測 (方法是搭配使用時間序列分析與雙線線性迴歸)。

```kusto
let startDate = startofday(datetime("2017-02-01"));
let endDate = startofday(datetime("2017-02-07"));
let minRsquare = 0.8;  // Tune the sensitivity of the detection sensor. Values close to 1 indicate very low sensitivity.
// Count all Good (Verbose + Info) and Bad (Error + Fatal + Warning) traces, per day.
traces
| where timestamp > startDate and timestamp < endDate
| summarize 
    Verbose = countif(severityLevel == 0),
    Info = countif(severityLevel == 1), 
    Warning = countif(severityLevel == 2),
    Error = countif(severityLevel == 3),
    Fatal = countif(severityLevel == 4) by bin(timestamp, 1d)
| extend Bad = (Error + Fatal + Warning), Good = (Verbose + Info)
// Determine the ratio of bad traces, from the total.
| extend Ratio = (todouble(Bad) / todouble(Good + Bad))*10000
| project timestamp , Ratio
// Create a time series.
| make-series RatioSeries=any(Ratio) default=0 on timestamp in range(startDate , endDate -1d, 1d) by 'TraceSeverity' 
// Apply a 2-line regression to the time series.
| extend (RSquare2, SplitIdx, Variance2,RVariance2,LineFit2)=series_fit_2lines(RatioSeries)
// Find out if our 2-line is trending up or down.
| extend (Slope,Interception,RSquare,Variance,RVariance,LineFit)=series_fit_line(LineFit2)
// Check whether the line fit reaches the threshold, and if the spike represents an increase (rather than a decrease).
| project PatternMatch = iff(RSquare2 > minRsquare and Slope>0, "Spike detected", "No Match")
```

## <a name="next-steps"></a>後續步驟

- 逐步完成[關於 Kusto 查詢語言的教學課程](tutorial.md?pivots=azuremonitor)。


::: zone-end

