---
title: 教學課程-Azure 資料總管
description: 本文說明 Azure 資料總管中的教學課程。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/23/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 64736d944c71d84c4950dea0341089732b258f27
ms.sourcegitcommit: a10e7c6ba96bdb94d95ef23f5d1506eb8fda0041
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92058677"
---
# <a name="tutorial"></a>教學課程

::: zone pivot="azuredataexplorer"

瞭解 Kusto 查詢語言的最佳方式，就是查看一些簡單的查詢，以使用 [具有一些範例資料的資料庫](https://help.kusto.windows.net/Samples)，來取得語言的「感覺」。 本文中示範的查詢應該在該資料庫上執行。 `StormEvents`此範例資料庫中的表格提供有關在美國發生的風暴的一些資訊。

<!--
  TODO: Provide link to reference data we used originally in StormEvents
-->

<!--
  TODO: A few samples below reference non-existent tables, such as Events and Logs.
        We need to add these tables.
-->

## <a name="count-rows"></a>計算資料列數目

我們的範例資料庫有一個名為的資料表 `StormEvents` 。
為了找出有多大，我們會將其內容輸送到只計算資料列的運算子：

* *語法：* 查詢是資料來源 (通常是) 資料表名稱，並選擇性地接著一或多個管道字元和某些表格式運算子。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents | count
```

結果如下︰

|Count|
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

|StartTime|EndTime|Duration|EventType|State|
|---|---|---|---|---|
|2007-09-18 20：00：00.0000000|2007-09-19 18：00：00.0000000|22:00:00|大雨|佛羅里達|
|2007-09-20 21：57：00.0000000|2007-09-20 22：05：00.0000000|00:08:00|龍捲風|佛羅里達|
|2007-09-29 08：11：00.0000000|2007-09-29 08：11：00.0000000|00:00:00|Waterspout|大西洋南部|
|2007-12-20 07：50：00.0000000|2007-12-20 07：53：00.0000000|00:03:00|Thunderstorm Wind|密西西比|
|2007-12-30 16：00：00.0000000|2007-12-30 16：05：00.0000000|00:05:00|Thunderstorm Wind|格魯吉亞|

您可以重複使用資料行名稱，並將計算結果指派給相同的資料行。
例如︰

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

:::image type="content" source="images/tutorial/time-series-start-bin.png" alt-text="依州/省的風暴事件計數直條圖":::

## <a name="multiple-series"></a>多個系列

在 `summarize by` 子句中使用多個值，為每組值建立個別的資料列：

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents 
| where StartTime > datetime(2007-06-04) and StartTime < datetime(2007-06-10) 
| where Source in ("Source","Public","Emergency Manager","Trained Spotter","Law Enforcement")
| summarize count() by bin(StartTime, 10h), Source
```

:::image type="content" source="images/tutorial/table-count-source.png" alt-text="依州/省的風暴事件計數直條圖":::

只需將轉譯詞彙新增至上述內容： `| render timechart` 。

:::image type="content" source="images/tutorial/line-count-source.png" alt-text="依州/省的風暴事件計數直條圖":::

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

:::image type="content" source="images/tutorial/time-count-hour.png" alt-text="依州/省的風暴事件計數直條圖":::

目前 `render` 不會正確地標示持續時間，但我們可以改用 `| render columnchart` ：

:::image type="content" source="images/tutorial/column-count-hour.png" alt-text="依州/省的風暴事件計數直條圖":::

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

:::image type="content" source="images/tutorial/time-hour-state.png" alt-text="依州/省的風暴事件計數直條圖":::

除 `1h` 以將 X 軸轉換成小時數，而不是持續時間：

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| extend hour= floor( StartTime % 1d , 1h)/ 1h
| where State in ("GULF OF MEXICO","MAINE","VIRGINIA","WISCONSIN","NORTH DAKOTA","NEW JERSEY","OREGON")
| summarize event_count=count() by hour, State
| render columnchart
```

:::image type="content" source="images/tutorial/column-hour-state.png" alt-text="依州/省的風暴事件計數直條圖":::

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

:::image type="content" source="images/tutorial/join-events-la.png" alt-text="依州/省的風暴事件計數直條圖":::

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

:::image type="content" source="images/tutorial/user-session-extend.png" alt-text="依州/省的風暴事件計數直條圖":::

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

:::image type="content" source="images/tutorial/event-count-duration.png" alt-text="依州/省的風暴事件計數直條圖":::

或使用 `| render columnchart` ：

:::image type="content" source="images/tutorial/column-event-count-duration.png" alt-text="依州/省的風暴事件計數直條圖":::

## <a name="percentiles"></a>百分位數

哪些持續時間範圍涵蓋不同的風暴百分比？

使用上述查詢，但 `render` 以下列內容取代：

```kusto
| summarize percentiles(duration, 5, 20, 50, 80, 95)
```

在此情況下，我們不提供任何 `by` 子句，因此結果為單一資料列：

:::image type="content" source="images/tutorial/summarize-percentiles-duration.png" alt-text="依州/省的風暴事件計數直條圖":::

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

:::image type="content" source="images/tutorial/summarize-percentiles-state.png" alt-text="依州/省的風暴事件計數直條圖":::

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

Azure 監視器不支援這項功能

::: zone-end
