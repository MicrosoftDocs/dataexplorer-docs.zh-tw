---
title: 教學課程-Azure 資料總管
description: 本文說明 Azure 資料總管中的教學課程。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/08/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 8a3c0b058b2c1cf5023ce0069a7dd938fce5caec
ms.sourcegitcommit: 4c7f20dfd59fb5b5b1adfbbcbc9b7da07df5e479
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2020
ms.locfileid: "95324732"
---
# <a name="tutorial"></a>教學課程

::: zone pivot="azuredataexplorer"

瞭解 Kusto 查詢語言的最佳方式，就是查看一些簡單的查詢，以使用 [具有一些範例資料的資料庫](https://help.kusto.windows.net/Samples)，來取得語言的「感覺」。 本文中示範的查詢應該在該資料庫上執行。 `StormEvents`此範例資料庫中的表格提供有關在美國發生的風暴的一些資訊。

## <a name="count-rows"></a>計算資料列數目

我們的範例資料庫有一個名為的資料表 `StormEvents` 。
為了找出有多大，我們會將其內容輸送到只計算資料列的運算子：

* *語法：* 查詢是資料來源 (通常是) 資料表名稱，並選擇性地接著一或多個管道字元和某些表格式運算子。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents | count
```

結果如下︰

|計數|
|-----|
|59066|
    
[count 運算子](./countoperator.md)。

## <a name="project-select-a-subset-of-columns"></a>專案：選取資料行的子集

使用 [project](./projectoperator.md) 只挑選您想要的資料行。 請參閱下列使用 [project](./projectoperator.md) 和 [take](./takeoperator.md) 運算子的範例。

## <a name="where-filtering-by-a-boolean-expression"></a>where：依布林運算式篩選

讓我們 `flood` 在 `California` 2007 年2月1日查看：

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| where StartTime > datetime(2007-02-01) and StartTime < datetime(2007-03-01)
| where EventType == 'Flood' and State == 'CALIFORNIA'
| project StartTime, EndTime , State , EventType , EpisodeNarrative
```

|StartTime|EndTime|State|EventType|EpisodeNarrative|
|---|---|---|---|---|
|2007-02-19 00：00：00.0000000|2007-02-19 08：00：00.0000000|加州|Flood|在南 San Joaquin 的正面系統中，移動了一小段時間，在19日的早期早上時，會有一小段高達西歐的國家/地區間距。 在接近 Taft 的州高速公路166之間回報輕微氾濫。|

## <a name="take-show-me-n-rows"></a>take：顯示 n 個數據列

讓我們看看一些資料 - 範例 5 資料列中的內容為何？

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| take 5
| project  StartTime, EndTime, EventType, State, EventNarrative  
```

|StartTime|EndTime|EventType|State|EventNarrative|
|---|---|---|---|---|
|2007-09-18 20：00：00.0000000|2007-09-19 18：00：00.0000000|大雨|佛羅里達|在近水樓臺 Volusia 國家/地區的每個部分，在24小時的期間內，最多會有9英寸的 rain。|
|2007-09-20 21：57：00.0000000|2007-09-20 22：05：00.0000000|龍捲風|佛羅里達|在北西部 Crooked Lake 的 Eustis 中，有一次龍捲風觸及。 當您在 Eustis 移動北美洲時，會快速更至 EF1 強度。 此播放軌的長度為兩英里以下，最大寬度為300碼。  龍捲風終結7家庭。 20個家庭收到重大損害，而81家中回報了輕微的損毀。 在 $6200000 設定的嚴重傷害和財產損毀。|
|2007-09-29 08：11：00.0000000|2007-09-29 08：11：00.0000000|Waterspout|大西洋南部|在墨爾本海灘的大西洋東南部中形成的 waterspout，並短暫地移往支援腳步。|
|2007-12-20 07：50：00.0000000|2007-12-20 07：53：00.0000000|Thunderstorm Wind|密西西比|許多大型樹狀結構都有一些電源線的下降。 在東部 Adams 縣中發生損毀。|
|2007-12-30 16：00：00.0000000|2007-12-30 16：05：00.0000000|Thunderstorm Wind|格魯吉亞|縣市分派報告了數個樹狀結構，並在接近州道路206的 Quincey Batten 迴圈下進行。 已預估移除樹狀結構的成本。|

但是 [，請以](./takeoperator.md) 沒有特定順序的方式來顯示資料表中的資料列，讓我們加以排序。
* [limit](./takeoperator.md) 是 [take](./takeoperator.md) 的別名，而且會有相同的效果。

## <a name="sort-and-top"></a>排序和頂端

* *語法：* 某些運算子具有由關鍵字（例如）引進的參數 `by` 。
* `desc` = 遞減順序，`asc` = 遞增。

顯示前 n 個資料列 (依特定資料行排序)︰

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| top 5 by StartTime desc
| project  StartTime, EndTime, EventType, State, EventNarrative  
```

|StartTime|EndTime|EventType|State|EventNarrative|
|---|---|---|---|---|
|2007-12-31 22：30：00.0000000|2007-12-31 23：59：00.0000000|冬季風暴|密西根|這個繁重的雪活動會延續到新年的提早早上。|
|2007-12-31 22：30：00.0000000|2007-12-31 23：59：00.0000000|冬季風暴|密西根|這個繁重的雪活動會延續到新年的提早早上。|
|2007-12-31 22：30：00.0000000|2007-12-31 23：59：00.0000000|冬季風暴|密西根|這個繁重的雪活動會延續到新年的提早早上。|
|2007-12-31 23：53：00.0000000|2007-12-31 23：53：00.0000000|高風|加州|在 Ventura 縣（山脈）中回報到 58 >mph 的北部至東北部接近尾聲 gusting。|
|2007-12-31 23：53：00.0000000|2007-12-31 23：53：00.0000000|高風|加州|暖彈簧 RAWS 感應器回報北邊接近尾聲 gusting 至 58 >mph。|

您可以使用 [sort](./sortoperator.md) 和 [take](./takeoperator.md) 運算子來達成相同的目的

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| sort by StartTime desc
| take 5
| project  StartTime, EndTime, EventType, EventNarrative
```

## <a name="extend-compute-derived-columns"></a>擴充：計算衍生的資料行

藉由計算每個資料列中的值來建立新的資料行：

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| limit 5
| extend Duration = EndTime - StartTime 
| project StartTime, EndTime, Duration, EventType, State
```

|StartTime|EndTime|持續時間|EventType|State|
|---|---|---|---|---|
|2007-09-18 20：00：00.0000000|2007-09-19 18：00：00.0000000|22:00:00|大雨|佛羅里達|
|2007-09-20 21：57：00.0000000|2007-09-20 22：05：00.0000000|00:08:00|龍捲風|佛羅里達|
|2007-09-29 08：11：00.0000000|2007-09-29 08：11：00.0000000|00:00:00|Waterspout|大西洋南部|
|2007-12-20 07：50：00.0000000|2007-12-20 07：53：00.0000000|00:03:00|Thunderstorm Wind|密西西比|
|2007-12-30 16：00：00.0000000|2007-12-30 16：05：00.0000000|00:05:00|Thunderstorm Wind|格魯吉亞|

您可以重複使用資料行名稱，並將計算結果指派給相同的資料行。
例如：

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print x=1
| extend x = x + 1, y = x
| extend x = x + 1
```

|x|y|
|---|---|
|3|1|

純量[運算式](./scalar-data-types/index.md)可包含所有一般運算子， (`+` 、 `-` 、 `*` 、 `/` 、 `%`) ，而且有一系列有用的函式。

## <a name="summarize-aggregate-groups-of-rows"></a>摘要：匯總資料列群組

計算每個國家/地區的事件數目：

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| summarize event_count = count() by State
```

[摘要](./summarizeoperator.md) 群組在子句中具有相同值的資料列 `by` ，然後使用彙總函式 (例如 `count`) ，將每個群組合並成單一資料列。 因此在此情況下，每個狀態都有一個資料列，以及該狀態的資料列計數的資料行。

其中有一個 [彙總函式](./summarizeoperator.md#list-of-aggregation-functions)範圍，您可以在一個摘要運算子中使用其中幾個來產生數個計算資料行。 例如，我們可以取得每個狀態的風暴計數，也可以取得每個狀態的唯一風暴類型總和，  
然後，我們可以使用 [top](./topoperator.md) 來取得最常受到影響的狀態：

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents 
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State
| top 5 by StormCount desc
```

|State|StormCount|TypeOfStorms|
|---|---|---|
|德克薩斯州|4701|27|
|堪薩斯|3166|21|
|愛荷華州|2337|19|
|伊利諾州|2022|23|
|密蘇里州|2016|20|

summarize 的結果有：

* `by`中具名的每個資料行；
* 每個計算運算式的資料行;
* 每一組 `by` 值的一個資料列。

## <a name="summarize-by-scalar-values"></a>依純量值彙總

您可以在子句中使用純量 (數值、時間或間隔) 值 `by` ，但您會想要將值放入 bin 中。  
[Bin ( # B1](./binfunction.md)函數適用于：

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| where StartTime > datetime(2007-02-14) and StartTime < datetime(2007-02-21)
| summarize event_count = count() by bin(StartTime, 1d)
```

這可將所有時間戳記縮減為1天的間隔：

|StartTime|event_count|
|---|---|
|2007-02-14 00：00：00.0000000|180|
|2007-02-15 00：00：00.0000000|66|
|2007-02-16 00：00：00.0000000|164|
|2007-02-17 00：00：00.0000000|103|
|2007-02-18 00：00：00.0000000|22|
|2007-02-19 00：00：00.0000000|52|
|2007-02-20 00：00：00.0000000|60|

[Bin ( # B1](./binfunction.md)與許多語言的[Floor ( # B3](./floorfunction.md)函數相同。 它只會將每個值縮減為您所提供之模數的最接近倍數，讓 [摘要](./summarizeoperator.md) 可以將資料列指派給群組。

## <a name="render-display-a-chart-or-table"></a>Render：顯示圖表或資料表

投影兩個數據行，並使用它們做為圖表的 x 和 y 軸：

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents 
| summarize event_count=count(), mid = avg(BeginLat) by State 
| sort by mid
| where event_count > 1800
| project State, event_count
| render columnchart
```

:::image type="content" source="images/tutorial/event-counts-state.png" alt-text="依州/省的風暴事件計數直條圖":::

雖然我們已 `mid` 在專案作業中移除，但如果我們想要讓圖表以該順序顯示國家/地區，仍然需要它。

嚴格來說，「轉譯」是用戶端的一項功能，而不是查詢語言的一部分。 同樣地，它也已整合至語言，而且非常適合用來構思您的結果。


## <a name="timecharts"></a>時間表

回到數值箱，讓我們來顯示時間序列：

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| summarize event_count=count() by bin(StartTime, 1d)
| render timechart
```

:::image type="content" source="images/tutorial/time-series-start-bin.png" alt-text="依時間分類收納的折線圖事件":::

## <a name="multiple-series"></a>多個系列

在 `summarize by` 子句中使用多個值，為每組值建立個別的資料列：

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents 
| where StartTime > datetime(2007-06-04) and StartTime < datetime(2007-06-10) 
| where Source in ("Source","Public","Emergency Manager","Trained Spotter","Law Enforcement")
| summarize count() by bin(StartTime, 10h), Source
```

:::image type="content" source="images/tutorial/table-count-source.png" alt-text="依來源的資料表計數":::

只需將轉譯詞彙新增至上述內容： `| render timechart` 。

:::image type="content" source="images/tutorial/line-count-source.png" alt-text="依來源的折線圖計數":::

請注意， `render timechart` 使用第一個資料行做為 X 軸，然後將其他資料行顯示為個別的線條。

## <a name="daily-average-cycle"></a>每日平均週期

活動在平均當日有何不同？

依時間模數一天計算事件數目，分類收納為小時。 請注意，我們使用 `floor` 的不是 bin：

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| extend hour = floor(StartTime % 1d , 1h)
| summarize event_count=count() by hour
| sort by hour asc
| render timechart
```

:::image type="content" source="images/tutorial/time-count-hour.png" alt-text="時間圖表計數（依小時）":::

目前 `render` 不會正確地標示持續時間，但我們可以改用 `| render columnchart` ：

:::image type="content" source="images/tutorial/column-count-hour.png" alt-text="依小時的直條圖計數":::

## <a name="compare-multiple-daily-series"></a>比較多個每日系列

活動在不同狀態的當日時間有何不同？

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| extend hour= floor( StartTime % 1d , 1h)
| where State in ("GULF OF MEXICO","MAINE","VIRGINIA","WISCONSIN","NORTH DAKOTA","NEW JERSEY","OREGON")
| summarize event_count=count() by hour, State
| render timechart
```

:::image type="content" source="images/tutorial/time-hour-state.png" alt-text="依小時和州的時間圖表":::

除 `1h` 以將 X 軸轉換成小時數，而不是持續時間：

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| extend hour= floor( StartTime % 1d , 1h)/ 1h
| where State in ("GULF OF MEXICO","MAINE","VIRGINIA","WISCONSIN","NORTH DAKOTA","NEW JERSEY","OREGON")
| summarize event_count=count() by hour, State
| render columnchart
```

:::image type="content" source="images/tutorial/column-hour-state.png" alt-text="依小時和州的直條圖":::

## <a name="join"></a>Join

如何尋找兩個指定的 EventTypes 在兩個狀態中發生的狀態？

您可以使用第一個事件1和第二個事件1，然後在狀態上聯結兩個集合。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| where EventType == "Lightning"
| join (
    StormEvents 
    | where EventType == "Avalanche"
) on State  
| distinct State
```

:::image type="content" source="images/tutorial/join-events-la.png" alt-text="聯結活動閃電和大量":::

## <a name="user-session-example-of-join"></a>Join 的使用者會話範例

本節不使用 `StormEvents` 資料表。

假設您的資料包含每個使用者會話的開頭和結束記號的事件，每個會話都有唯一的識別碼。 

每個使用者會話會持續多久？

藉由使用 `extend` 提供這兩個時間戳記的別名，您就可以計算會話的持續時間。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
Events
| where eventName == "session_started"
| project start_time = timestamp, stop_time, country, session_id
| join ( Events
    | where eventName == "session_ended"
    | project stop_time = timestamp, session_id
    ) on session_id
| extend duration = stop_time - start_time
| project start_time, stop_time, country, duration
| take 10
```

:::image type="content" source="images/tutorial/user-session-extend.png" alt-text="使用者會話延伸":::

在執行聯結之前，使用 `project` 只選取我們需要的資料行是相當好的做法。
在相同的子句中，我們會將時間戳記資料行重新命名。

## <a name="plot-a-distribution"></a>繪製分佈圖

有多少個風暴有不同的長度？

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| extend  duration = EndTime - StartTime
| where duration > 0s
| where duration < 3h
| summarize event_count = count()
    by bin(duration, 5m)
| sort by duration asc
| render timechart
```

:::image type="content" source="images/tutorial/event-count-duration.png" alt-text="依持續時間時間圖表的事件計數":::

或使用 `| render columnchart` ：

:::image type="content" source="images/tutorial/column-event-count-duration.png" alt-text="依持續時間時間圖表的直條圖事件計數":::

## <a name="percentiles"></a>百分位數

哪些持續時間範圍涵蓋不同的風暴百分比？

使用上述查詢，但 `render` 以下列內容取代：

```kusto
| summarize percentiles(duration, 5, 20, 50, 80, 95)
```

在此情況下，我們不提供任何 `by` 子句，因此結果為單一資料列：

:::image type="content" source="images/tutorial/summarize-percentiles-duration.png" alt-text="依持續時間的資料表摘要百分位數":::

我們可以從中看到︰

* 5% 的風暴持續時間小於 5m;
* 50% 的風暴最後小於 1h 25m;
* 5% 的風暴最後至少是下半年50m。

若要針對每個狀態取得個別的明細，我們只需要透過兩個摘要運算子來分開顯示 state 資料行：

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| extend  duration = EndTime - StartTime
| where duration > 0s
| where duration < 3h
| summarize event_count = count()
    by bin(duration, 5m), State
| sort by duration asc
| summarize percentiles(duration, 5, 20, 50, 80, 95) by State
```

:::image type="content" source="images/tutorial/summarize-percentiles-state.png" alt-text="資料表摘要百分位數持續時間（依狀態）":::


## <a name="let-assign-a-result-to-a-variable"></a>Let︰將結果指派給變數

使用「 [讓](./letstatement.md) 」分隔上述「聯結」範例中的查詢運算式部分。 結果不變：

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let LightningStorms = 
    StormEvents
    | where EventType == "Lightning";
let AvalancheStorms = 
    StormEvents
    | where EventType == "Avalanche";
LightningStorms 
| join (AvalancheStorms) on State
| distinct State
```
> [!TIP]
> 在 Kusto Explorer 用戶端中，請勿在這幾個部分之間放置空白行。 務必執行所有一切。

## <a name="combining-data-from-several-databases-in-a-query"></a>在查詢中結合數個資料庫的資料

查看 [跨資料庫查詢](./cross-cluster-or-database-queries.md) 以取得詳細討論

當您撰寫樣式的查詢時：

```kusto
Logs | where ...
```

名為 Logs 的資料表必須在您的預設資料庫中。 如果您想要從另一個資料庫存取資料表，請使用下列語法：

```kusto
database("db").Table
```

因此，如果您有名稱為 [ *診斷* ] 和 [ *遙測* ] 的資料庫，而您想要讓某些資料相互關聯，您可以撰寫 (假設 *診斷* 是您的預設資料庫) 

```kusto
Logs | join database("Telemetry").Metrics on Request MachineId | ...
```

或者，如果您的預設資料庫是 *遙測*

```kusto
union Requests, database("Diagnostics").Logs | ...
```
    
上述所有資料庫都假設兩個資料庫都位於您目前所連接的叢集中。 假設 *遙測* 資料庫屬於另一個名為 *TelemetryCluster.kusto.windows.net* 的叢集，然後存取您需要的

```kusto
Logs | join cluster("TelemetryCluster").database("Telemetry").Metrics on Request MachineId | ...
```

> [!NOTE]
> 指定叢集時，資料庫是強制的




::: zone-end

::: zone pivot="azuremonitor"

瞭解 Kusto 查詢語言的最佳方式是查看一些簡單的查詢，以取得語言的「操作」。 這些查詢類似于 Azure 資料總管教學課程中所使用的查詢，但它們會使用 Log Analytics 工作區中通用資料表的資料。 

使用 Log Analytics 執行這些查詢，這是 Azure 入口網站中的一項工具，可以使用 Azure 監視器中的記錄資料來寫入記錄查詢並評估其結果。 如果您不熟悉 Log analytics，您可以在 [Log analytics 教學](/azure/azure-monitor/log-query/log-analytics-tutorial)課程中完成教學課程。

此處的所有查詢都會使用 [Log Analytics 示範環境](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade)。 您可以使用自己的環境，但您可能沒有在這裡使用的一些資料表。 由於示範環境中的資料不是靜態的，因此查詢的結果可能會與此處顯示的結果稍有不同。


## <a name="count-rows"></a>計算資料列數目
[InsightsMetrics](/azure/azure-monitor/reference/tables/insightsmetrics) 包含深入解析所收集的效能資料，例如適用於 VM 的 Azure 監視器和容器的 Azure 監視器。 為了找出有多大，我們會將其內容輸送到只計算資料列的運算子：

查詢是資料來源 (通常是) 資料表名稱，並選擇性地接著一或多個管道字元和某些表格式運算子。 在此情況下，會傳回 InsightsMetrics 資料表中的所有記錄，然後傳送至 [count 運算子](./countoperator.md) 運算子來輸出結果，因為它是查詢中的最後一個命令。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
InsightsMetrics | count
```

結果如下︰

|計數|
|-----|
|1263191|
    



## <a name="where-filtering-by-a-boolean-expression"></a>where：依布林運算式篩選
[AzureActivity](/azure/azure-monitor/reference/tables/azureactivity) 具有來自 azure 活動記錄的專案，可讓您深入瞭解 azure 中發生的任何訂用帳戶層級或管理群組層級事件。 讓我們僅查看 `Critical` 特定周內的專案。


[Where](/azure/data-explorer/kusto/query/whereoperator)運算子在 KQL 中很常見，而且會將資料表篩選為符合指定準則的資料列。 此範例使用多個命令。 此查詢會先抓取資料表的所有記錄，然後只篩選時間範圍內記錄的資料，然後針對具有層級的記錄篩選這些結果 `Critical` 。

> [!NOTE]
> 除了在查詢中使用資料行指定篩選 `TimeGenerated` ，您可以在 Log Analytics 中指定時間範圍。 如需詳細資訊，請參閱 [Azure 監視器 Log Analytics 中的記錄查詢範圍和時間範圍](/azure/azure-monitor/log-query/scope)。

```kusto
AzureActivity
| where TimeGenerated > datetime(10-01-2020) and TimeGenerated < datetime(10-07-2020)
| where Level == 'Critical'
```

[![Where 篩選範例的結果](images/tutorial/am-results-where.png)](images/tutorial/am-results-where.png#lightbox)


## <a name="project-select-a-subset-of-columns"></a>專案：選取資料行的子集

使用 [project](./projectoperator.md) 只挑選您想要的資料行。 以上述範例為基礎，讓我們將輸出限制為特定資料行。

```kusto
AzureActivity
| where TimeGenerated > datetime(10-01-2020) and TimeGenerated < datetime(10-07-2020)
| where Level == 'Critical'
| project TimeGenerated, Level, OperationNameValue, ResourceGroup, _ResourceId
```

[![專案範例的結果](images/tutorial/am-results-project.png)](images/tutorial/am-results-project.png#lightbox)


## <a name="take-show-me-n-rows"></a>take：顯示 n 個數據列
[NetworkMonitoring](/azure/azure-monitor/reference/tables/networkmonitoring) 具有 Azure 虛擬網路的監視資料。 讓我們使用 [take](./takeoperator.md) 運算子來查看該資料表中的5個範例資料列。 [Take 會](./takeoperator.md)顯示資料表中特定數目的資料列，而不是特定的順序。

```kusto
NetworkMonitoring
| take 10
| project TimeGenerated, Computer, SourceNetwork, DestinationNetwork, HighLatency, LowLatency
```

[![Take 範例的結果](images/tutorial/am-results-take.png)](images/tutorial/am-results-take.png#lightbox)

## <a name="sort-and-top"></a>排序和頂端
我們可以透過第一次依時間排序來傳回最新的5筆記錄，而不是隨機記錄。

```kusto
NetworkMonitoring
| sort by TimeGenerated desc
| take 5
| project TimeGenerated, Computer, SourceNetwork, DestinationNetwork, HighLatency, LowLatency
```

您可以改為使用 [top](./topoperator.md) 運算子來取得此確切行為。 

```kusto
NetworkMonitoring
| top 5 by TimeGenerated desc
| project TimeGenerated, Computer, SourceNetwork, DestinationNetwork, HighLatency, LowLatency
```

[![Top 範例的結果](images/tutorial/am-results-top.png)](images/tutorial/am-results-top.png#lightbox)


## <a name="extend-compute-derived-columns"></a>擴充：計算衍生的資料行
[擴充](./projectoperator.md)運算子類似于[專案](./projectoperator.md)，不同之處在于它會加入至資料行集合，而不是取代它們。 您也可以使用這兩個運算子，根據每個資料列上的計算來建立新的資料行。

效能 [資料表有](/azure/azure-monitor/reference/tables/perf) 從執行 Log Analytics 代理程式的虛擬機器收集的效能資料。 

```kusto
Perf
| where ObjectName == "LogicalDisk" and CounterName == "Free Megabytes"
| project TimeGenerated, Computer, FreeMegabytes = CounterValue
| extend FreeGigabytes = FreeMegabytes / 1000
```

[![擴充範例的結果](images/tutorial/am-results-extend.png)](images/tutorial/am-results-extend.png#lightbox)


## <a name="summarize-aggregate-groups-of-rows"></a>摘要：匯總資料列群組
[摘要](./summarizeoperator.md)運算子會將在子句中具有相同值的資料列群組在一起 `by` ，然後使用彙總函式（例如） `count` 將每個群組合並成單一資料列。 其中有一個 [彙總函式](./summarizeoperator.md#list-of-aggregation-functions)範圍，您可以在一個摘要運算子中使用其中幾個來產生數個計算資料行。 

[SecurityEvent](/azure/azure-monitor/reference/tables/securityevent)會保存在受監視電腦上啟動的安全性事件，例如登入和處理常式。 我們可以計算每一部電腦上發生的每個層級的事件數目。 在此範例中，每一部電腦和層級組合都會有一個資料列，以及事件計數的資料行。

```kusto
SecurityEvent
| summarize count() by Computer, Level
```

[![摘要計數範例的結果](images/tutorial/am-results-summarize-count.png)](images/tutorial/am-results-summarize-count.png#lightbox)


## <a name="summarize-by-scalar-values"></a>依純量值彙總
您可以透過純量值（例如數位和時間值）來匯總，但您應該使用 [bin ( # B1 ](./binfunction.md) 函數將資料列分組為不同的資料集。 例如，如果您匯總了 `TimeGenerated` ，就會取得幾乎每個時間值的資料列。 `bin()` 將這些值合併為小時或天。

[InsightsMetrics](/azure/azure-monitor/reference/tables/insightsmetrics) 包含深入解析所收集的效能資料，例如適用於 VM 的 Azure 監視器和容器的 Azure 監視器。 下列查詢會顯示多部電腦的每小時平均處理器使用率。

```kusto
InsightsMetrics
| where Computer startswith "DC"
| where Namespace  == "Processor" and Name == "UtilizationPercentage"
| summarize avg(Val) by Computer, bin(TimeGenerated, 1h)
```


[![總結平均範例的結果](images/tutorial/am-results-summarize-avg.png)](images/tutorial/am-results-summarize-avg.png#lightbox)



## <a name="render-display-a-chart-or-table"></a>Render：顯示圖表或資料表
[Render](./renderoperator.md?pivots=azuremonitor)運算子會指定如何轉譯查詢的輸出。 根據預設，Log Analytics 會將輸出轉譯為數據表，而且您可以在執行查詢之後選取不同的圖表類型。 `render`運算子適用于在通常偏好特定圖表類型的查詢中包含。

下列範例使用顯示單一電腦的每小時平均處理器使用率，並將輸出轉譯為時間圖表。

```kusto
InsightsMetrics
| where Computer == "DC00.NA.contosohotels.com"
| where Namespace  == "Processor" and Name == "UtilizationPercentage"
| summarize avg(Val) by Computer, bin(TimeGenerated, 1h)
| render timechart
```

[![轉譯範例的結果](images/tutorial/am-results-render.png)](images/tutorial/am-results-render.png#lightbox)



## <a name="multiple-series"></a>多個系列
如果子句中有多個值 `summarize by` ，則圖表會顯示每一組值的個別數列：

```kusto
InsightsMetrics
| where Computer startswith "DC"
| where Namespace  == "Processor" and Name == "UtilizationPercentage"
| summarize avg(Val) by Computer, bin(TimeGenerated, 1h)
| render timechart
```


[![使用多個數列範例轉譯的結果](images/tutorial/am-results-render-multiple.png)](images/tutorial/am-results-render-multiple.png#lightbox)

## <a name="join-data-from-two-tables"></a>從兩個數據表聯結資料
如果您需要從單一查詢的兩個數據表中取出資料，該怎麼辦？ [Join](/azure/data-explorer/kusto/query/joinoperator?pivots=azuremonitor)運算子可讓您將多個資料表中的資料列合併成單一結果集。 每個資料表都必須有一個具有相符值的資料行，以便讓聯結瞭解要比對的資料列。

[VMComputer](/azure/azure-monitor/reference/tables/vmcomputer) 是適用於 VM 的 Azure 監視器用來儲存其所監視之虛擬機器詳細資料的資料表。 [InsightsMetrics](/azure/azure-monitor/reference/tables/insightsmetrics) 會保存從這些虛擬機器收集的效能資料。 在 *InsightsMetrics* 中收集的一個值是可用記憶體，但無法使用記憶體的百分比。 為了計算百分比，我們需要 *VMComputer* 中每部虛擬機器的實體記憶體。

下列範例查詢會使用聯結來執行這項計算。 [Distinct](/azure/data-explorer/kusto/query/joinoperator)會與 *VMComputer* 搭配使用，因為每一部電腦都會定期收集詳細資料，以便在該資料表中為每個電腦建立多個資料列。 這兩個數據表會使用 [ *電腦* ] 資料行來聯結。 這表示會在結果集中建立一個資料列，其中包含 *InsightsMetrics* 中每個資料列的資料行，且 *電腦* 中的值與 *VMComputer* 中 [*電腦*] 資料行的值相符。

```kusto
VMComputer
| distinct Computer, PhysicalMemoryMB
| join kind=inner (
    InsightsMetrics
    | where Namespace == "Memory" and Name == "AvailableMB"
    | project TimeGenerated, Computer, AvailableMemoryMB = Val
) on Computer
| project TimeGenerated, Computer, PercentMemory = AvailableMemoryMB / PhysicalMemoryMB * 100
```

[![聯結範例的結果](images/tutorial/am-results-join.png)](images/tutorial/am-results-join.png#lightbox)


## <a name="let-assign-a-result-to-a-variable"></a>Let︰將結果指派給變數
使用 [let 讓](./letstatement.md) 查詢更容易閱讀及管理。 這個運算子可讓您將查詢結果指派給稍後可以使用的變數。 上述範例中的相同查詢可以重寫為下列。

 
```kusto
let PhysicalComputer = VMComputer
    | distinct Computer, PhysicalMemoryMB;
    let AvailableMemory = 
InsightsMetrics
    | where Namespace == "Memory" and Name == "AvailableMB"
    | project TimeGenerated, Computer, AvailableMemoryMB = Val;
PhysicalComputer
| join kind=inner (AvailableMemory) on Computer
| project TimeGenerated, Computer, PercentMemory = AvailableMemoryMB / PhysicalMemoryMB * 100
```

[![Let 範例的結果](images/tutorial/am-results-let.png)](images/tutorial/am-results-let.png#lightbox)

## <a name="next-steps"></a>後續步驟

- [查看 Kusto 查詢語言的程式碼範例](samples.md?pivots=azuremonitor)。


::: zone-end
