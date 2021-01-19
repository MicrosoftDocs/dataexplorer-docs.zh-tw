---
title: 教學課程：Azure 資料總管和 Azure 監視器中的 Kusto 查詢
description: 本教學課程說明如何在 Kusto 查詢語言中使用查詢，以符合 Azure 資料總管和 Azure 監視器中的常見查詢需求。
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
ms.openlocfilehash: 930936bd9839730ccb0c438cc96a67334ea7ac71
ms.sourcegitcommit: 64b7b320875950dfee8eb1a23d36aa95e27d7297
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98207800"
---
# <a name="tutorial-use-kusto-queries-in-azure-data-explorer-and-azure-monitor"></a>教學課程：在 Azure 資料總管和 Azure 監視器中使用 Kusto 查詢

::: zone pivot="azuredataexplorer"

若要了解 Kusto 查詢語言，最佳方式便是查看一些基本查詢，以獲得對於該語言的「感覺」。 建議您使用[資料庫與一些範例資料](https://help.kusto.windows.net/Samples)。 本教學課程中示範的查詢應該在該資料庫上執行。 範例資料庫中的 `StormEvents` 資料表會針對美國所發生的風暴提供一些資訊。

## <a name="count-rows"></a>計算資料列數目

我們的範例資料庫有一個稱為 `StormEvents` 的資料表。 為了了解該資料表有多大，我們會透過管道將其內容傳送至只會計算資料表中資料列數目的運算子。 

*語法注意事項*：查詢是一種資料來源 (通常是資料表名稱)，後面並選擇性地接著一組或多組管道字元和某些表格式運算子。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents | count
```

輸出如下：

|計數|
|-----|
|59066|
    
如需詳細資訊，請參閱 [count 運算子](./countoperator.md)。

## <a name="select-a-subset-of-columns-project"></a>選取資料行的子集：*project*

使用 [project](./projectoperator.md) 即可只挑選您想要的資料行。 請參閱下列範例，其同時使用 [project](./projectoperator.md) 和 [take](./takeoperator.md) 運算子。

## <a name="filter-by-boolean-expression-where"></a>依布林運算式篩選：*where*

讓我們只看 2007 年 2 月 `California` 的 `flood` 事件：

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| where StartTime > datetime(2007-02-01) and StartTime < datetime(2007-03-01)
| where EventType == 'Flood' and State == 'CALIFORNIA'
| project StartTime, EndTime , State , EventType , EpisodeNarrative
```

輸出如下：

|StartTime|EndTime|州|EventType|EpisodeNarrative|
|---|---|---|---|---|
|2007-02-19 00:00:00.0000000|2007-02-19 08:00:00.0000000|加利福尼亞州|Flood|過境 Southern San Joaquin Valley 的鋒面系統於 19 日凌晨的幾個小時內，為 Kern County 西部區域帶來短暫的暴雨。 據報導，State Highway 166 靠近 Taft 的地方發生了輕微水患。|

## <a name="show-n-rows-take"></a>顯示 *n* 個資料列：*take*

讓我們看一些資料。 有五個資料列的隨機樣本中有何內容？

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| take 5
| project  StartTime, EndTime, EventType, State, EventNarrative  
```

輸出如下：

|StartTime|EndTime|EventType|州|EventNarrative|
|---|---|---|---|---|
|2007-09-18 20:00:00.0000000|2007-09-19 18:00:00.0000000|暴雨|佛羅里達州|Volusia County沿海地區在 24 小時內降下了多達 9 英寸的雨量。|
|2007-09-20 21:57:00.0000000|2007-09-20 22:05:00.0000000|龍捲風|佛羅里達州|龍捲風襲擊了 West Crooked Lake 最北端的 Town of Eustis。 龍捲風沿北北西方向穿過 Eustis 時，強度很快就增強到 EF1 等級。 其行進軌跡只有不到兩英哩的長度，最大寬度則為 300 碼。  龍捲風摧毀了 7 間民房。 27 間民房嚴重受損，81 間民房輕微受損。 沒有重大傷亡，財產損失估計為 620 萬美元。|
|2007-09-29 08:11:00.0000000|2007-09-29 08:11:00.0000000|水龍捲|大西洋南部|Melbourne Beach 的大西洋東南部形成了水龍捲，並短暫地向岸邊襲來。|
|2007-12-20 07:50:00.0000000|2007-12-20 07:53:00.0000000|Thunderstorm Wind|密西西比州|許多大樹被吹倒，有些電力線掉落。 Adams County 東部區域發生災害。|
|2007-12-30 16:00:00.0000000|2007-12-30 16:05:00.0000000|Thunderstorm Wind|喬治亞州|該郡電訊報導，Quincey Batten Loop 靠近 State Road 206 沿線有幾顆樹被吹倒。 移樹的費用已進行估計。|

但是 [take](./takeoperator.md) 不會以特定順序顯示資料表中的資料列，因此讓我們加以排序。 ([limit](./takeoperator.md) 是 [take](./takeoperator.md) 的別名，且具有相同的效果)。

## <a name="order-results-sort-top"></a>為結果排序：*sort*、*top*

* *語法注意事項*：有些運算子具有透過關鍵字 (如 `by`) 引進的參數。
* 在下列範例中，`desc` 會以遞減順序來為結果排序，`asc` 則會以遞增順序來為結果排序。

顯示前 *n* 個資料列 (依特定資料行排序)︰

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| top 5 by StartTime desc
| project  StartTime, EndTime, EventType, State, EventNarrative  
```

輸出如下：

|StartTime|EndTime|EventType|州|EventNarrative|
|---|---|---|---|---|
|2007-12-31 22:30:00.0000000|2007-12-31 23:59:00.0000000|暴風雪|密西根|這個大雪事件持續到新年清晨時分。|
|2007-12-31 22:30:00.0000000|2007-12-31 23:59:00.0000000|暴風雪|密西根|這個大雪事件持續到新年清晨時分。|
|2007-12-31 22:30:00.0000000|2007-12-31 23:59:00.0000000|暴風雪|密西根|這個大雪事件持續到新年清晨時分。|
|2007-12-31 23:53:00.0000000|2007-12-31 23:53:00.0000000|強風|加利福尼亞州|據報導，Ventura County 山區由北方到東北方向吹起了一陣風速大約 58 mph 的強風。|
|2007-12-31 23:53:00.0000000|2007-12-31 23:53:00.0000000|強風|加利福尼亞州|Warm Springs RAWS 感應器回報有風速 58 mph 的北風吹來。|

您可以先使用 [sort](./sortoperator.md) 再使用 [take](./takeoperator.md) 來達到相同的結果：

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| sort by StartTime desc
| take 5
| project  StartTime, EndTime, EventType, EventNarrative
```

## <a name="compute-derived-columns-extend"></a>計算衍生的資料行：*extend*

藉由計算每個資料列中的值來建立新的資料行：

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| limit 5
| extend Duration = EndTime - StartTime 
| project StartTime, EndTime, Duration, EventType, State
```

輸出如下：

|StartTime|EndTime|持續時間|EventType|州|
|---|---|---|---|---|
|2007-09-18 20:00:00.0000000|2007-09-19 18:00:00.0000000|22:00:00|暴雨|佛羅里達州|
|2007-09-20 21:57:00.0000000|2007-09-20 22:05:00.0000000|00:08:00|龍捲風|佛羅里達州|
|2007-09-29 08:11:00.0000000|2007-09-29 08:11:00.0000000|00:00:00|水龍捲|大西洋南部|
|2007-12-20 07:50:00.0000000|2007-12-20 07:53:00.0000000|00:03:00|Thunderstorm Wind|密西西比州|
|2007-12-30 16:00:00.0000000|2007-12-30 16:05:00.0000000|00:05:00|Thunderstorm Wind|喬治亞州|

您可以重複使用資料行名稱，並將計算結果指派給相同的資料行。

範例：

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print x=1
| extend x = x + 1, y = x
| extend x = x + 1
```

輸出如下：

|x|y|
|---|---|
|3|1|

[純量運算式](./scalar-data-types/index.md)可以包含所有常見運算子 (`+`、`-`、`*`、`/`、`%`)，並可使用一系列的實用函式。

## <a name="aggregate-groups-of-rows-summarize"></a>彙總資料列群組：*summarize*

計算每個狀態中發生的事件數目：

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| summarize event_count = count() by State
```

[summarize](./summarizeoperator.md) 會將在 `by` 子句中具有相同值的資料列群組在一起，然後使用彙總函式 (例如 `count`) 將每個群組合併至單一資料列。 在這種狀況下，每個狀態都會有一個資料列，且會有一個資料行顯示該狀態中的資料列計數。

有一系列的[彙總函式](./summarizeoperator.md#list-of-aggregation-functions)可以使用。 您可以在一個 `summarize` 運算子中使用數個彙總函式來產生數個計算資料行。 例如，我們可以取得各州的風暴計數，以及各州唯一類型風暴的總和。 然後，我們可以使用 [top](./topoperator.md) 來取得最常受到風暴影響的州：

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents 
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State
| top 5 by StormCount desc
```

輸出如下：

|州|StormCount|TypeOfStorms|
|---|---|---|
|德克薩斯州|4701|27|
|堪薩斯州|3166|21|
|愛荷華州|2337|19|
|伊利諾州|2022|23|
|密蘇里州|2016|20|

在 `summarize` 運算子的結果中：

* 每個資料行都會以 `by` 來指名。
* 每個已計算的運算式都會有一個資料行。
* 每一組 `by` 值都有一個資料列。

## <a name="summarize-by-scalar-values"></a>依純量值彙總

您可以在 `by` 子句中使用純量 (數值、時間或間隔) 值，但您會想要使用 [bin()](./binfunction.md) 函式將值放入間隔中：

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| where StartTime > datetime(2007-02-14) and StartTime < datetime(2007-02-21)
| summarize event_count = count() by bin(StartTime, 1d)
```

此查詢會將所有時間戳記縮減為一天的間隔：

|StartTime|event_count|
|---|---|
|2007-02-14 00:00:00.0000000|180|
|2007-02-15 00:00:00.0000000|66|
|2007-02-16 00:00:00.0000000|164|
|2007-02-17 00:00:00.0000000|103|
|2007-02-18 00:00:00.0000000|22|
|2007-02-19 00:00:00.0000000|52|
|2007-02-20 00:00:00.0000000|60|

在許多語言中，[bin()](./binfunction.md) 和 [floor()](./floorfunction.md) 函式是相同的。 其只會將每個值減少至所提供模數的最接近倍數，讓 [summarize](./summarizeoperator.md) 可以將資料列指派給群組。

<a name="displaychartortable"></a>
## <a name="display-a-chart-or-table-render"></a>顯示圖表或資料表：*render*

您可以投影兩個資料行，並將其作為圖表的 X 軸和 Y 軸：

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents 
| summarize event_count=count(), mid = avg(BeginLat) by State 
| sort by mid
| where event_count > 1800
| project State, event_count
| render columnchart
```

:::image type="content" source="images/tutorial/event-counts-state.png" alt-text="螢幕擷取畫面：依州別顯示風暴事件計數的直條圖。":::

雖然我們已在 `project` 作業中移除 `mid`，但如果我們想要讓圖表依該順序顯示狀態，則仍需要用到。

嚴格來說，`render` 是用戶端的一項功能，而不是查詢語言的一部分。 不過，其已整合到該語言中，並且適合用來構想您的結果。


## <a name="timecharts"></a>時間表

回到數值間隔，讓我們顯示一個時間序列：

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| summarize event_count=count() by bin(StartTime, 1d)
| render timechart
```

:::image type="content" source="images/tutorial/time-series-start-bin.png" alt-text="螢幕擷取畫面：依時間設定間隔的事件折線圖。":::

## <a name="multiple-series"></a>多個系列

在 `summarize by` 子句中使用多個值，為每組值建立個別的資料列：

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents 
| where StartTime > datetime(2007-06-04) and StartTime < datetime(2007-06-10) 
| where Source in ("Source","Public","Emergency Manager","Trained Spotter","Law Enforcement")
| summarize count() by bin(StartTime, 10h), Source
```

:::image type="content" source="images/tutorial/table-count-source.png" alt-text="依來源顯示資料表計數的螢幕擷取畫面。":::

只需將 `render` 字詞新增至上述範例即可：`| render timechart`。

:::image type="content" source="images/tutorial/line-count-source.png" alt-text="螢幕擷取畫面：依來源顯示折線圖計數。":::

請注意，`render timechart` 會使用第一個資料行作為 X 軸，然後將其他資料行顯示為個別線條。

## <a name="daily-average-cycle"></a>每日平均週期

活動在平均一天的時間內如何變化？

依時間模數一天計算事件 (間隔設為數小時)。 在這裡，我們使用 `floor`，而不是 `bin`：

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| extend hour = floor(StartTime % 1d , 1h)
| summarize event_count=count() by hour
| sort by hour asc
| render timechart
```

:::image type="content" source="images/tutorial/time-count-hour.png" alt-text="依小時顯示時間圖計數的螢幕擷取畫面。":::

目前，`render` 不會正確地標示持續時間，但我們可以改為使用 `| render columnchart`：

:::image type="content" source="images/tutorial/column-count-hour.png" alt-text="依小時顯示直條圖計數的螢幕擷取畫面。":::

## <a name="compare-multiple-daily-series"></a>比較多個每日系列

不同州別的活動在一天當中如何變化？

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| extend hour= floor( StartTime % 1d , 1h)
| where State in ("GULF OF MEXICO","MAINE","VIRGINIA","WISCONSIN","NORTH DAKOTA","NEW JERSEY","OREGON")
| summarize event_count=count() by hour, State
| render timechart
```

:::image type="content" source="images/tutorial/time-hour-state.png" alt-text="螢幕擷取畫面：依小時和州別的時間圖。":::

除以 `1h` 以將 X 軸轉換成小時數，而不是持續時間：

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| extend hour= floor( StartTime % 1d , 1h)/ 1h
| where State in ("GULF OF MEXICO","MAINE","VIRGINIA","WISCONSIN","NORTH DAKOTA","NEW JERSEY","OREGON")
| summarize event_count=count() by hour, State
| render columnchart
```

:::image type="content" source="images/tutorial/column-hour-state.png" alt-text="依小時和州別顯示直條圖的螢幕擷取畫面。":::

## <a name="join-data-types"></a>聯結資料類型

要如何找到兩個特定的事件類型，以及各類型在哪個州別發生？

您可以使用第一個 `EventType` 和第二個 `EventType` 來提取風暴事件，然後在 `State` 上聯結這兩個集合：

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

:::image type="content" source="images/tutorial/join-events-lightning-avalanche.png" alt-text="螢幕擷取畫面：顯示閃電和雪崩事件聯結的螢幕擷取畫面。":::

## <a name="user-session-example-of-join"></a>*join* 的使用者工作階段範例

本節不會使用 `StormEvents` 資料表。

假設您的資料所包含的事件，會使用每個工作階段的唯一識別碼來標記每個使用者工作階段的開始和結束。 

要如何找出每個使用者工作階段的持續時間？

您可以使用 `extend` 來提供兩個時間戳記的別名，然後計算工作階段持續時間：

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

:::image type="content" source="images/tutorial/user-session-extend.png" alt-text="螢幕擷取畫面：使用者工作階段延伸結果的資料表。":::

在執行聯結之前，最好先使用 `project` 來僅選取您需要的資料行。 在相同的子句中，將 `timestamp` 資料行重新命名。

## <a name="plot-a-distribution"></a>繪製分佈圖

回到 `StormEvents` 資料表，有多少個不同長度的風暴？

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

:::image type="content" source="images/tutorial/event-count-duration.png" alt-text="螢幕擷取畫面：依持續時間的事件計數時間圖結果。":::

或者，您也可以使用 `| render columnchart`：

:::image type="content" source="images/tutorial/column-event-count-duration.png" alt-text="螢幕擷取畫面：依持續時間的事件計數時間圖直條圖。":::

## <a name="percentiles"></a>百分位數

在不同百分比的風暴中，我們會找到哪些持續時間範圍？

若要取得此資訊，請使用上述查詢，但將 `render` 取代為：

```kusto
| summarize percentiles(duration, 5, 20, 50, 80, 95)
```

在此情況下，我們不會使用 `by` 子句，因此輸出是單一資料列：

:::image type="content" source="images/tutorial/summarize-percentiles-duration.png" lightbox="images/tutorial/summarize-percentiles-duration.png" alt-text="螢幕擷取畫面：依持續時間的彙總百分位數結果資料表。":::

我們可以從輸出中發現：

* 5% 的風暴持續不到 5 分鐘。
* 50% 的風暴持續不到 1 小時 25 分鐘。
* 95% 的風暴持續至少 2 小時 50 分鐘。

若要取得每個州別的個別明細，請使用個別 `state` 資料行搭配這兩個 `summarize` 運算子：

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

:::image type="content" source="images/tutorial/summarize-percentiles-state.png" alt-text="依州別的資料表彙總百分位數持續時間。":::

## <a name="assign-a-result-to-a-variable-let"></a>將結果指派給變數：*let*

使用 [let](./letstatement.md) 來分隔前述 `join` 範例中查詢運算式的各個部分。 結果不變：

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
> 在 Kusto Explorer 中，若要執行整個查詢，請不要在查詢的各個部分之間加入空白行。

## <a name="combine-data-from-several-databases-in-a-query"></a>將來自數個資料庫的資料合併到查詢中

在下列查詢中，`Logs` 資料表必須位於預設資料庫中：

```kusto
Logs | where ...
```

若要存取位於不同資料庫的資料表，請使用下列語法：

```kusto
database("db").Table
```

例如，如果您有名為 `Diagnostics` 和 `Telemetry` 的資料庫，而且您想要將這兩個資料表中的部分資料相互關聯，則可以使用下列查詢 (假設 `Diagnostics` 是您的預設資料庫)：

```kusto
Logs | join database("Telemetry").Metrics on Request MachineId | ...
```

如果您的預設資料庫是 `Telemetry`，請使用此查詢：

```kusto
union Requests, database("Diagnostics").Logs | ...
```
    
上述兩個查詢假設兩個資料庫都位於您目前連線的叢集中。 如果 `Telemetry` 資料庫位於名為 *TelemetryCluster.kusto.windows.net* 的叢集中，而您想要存取，則請使用下列查詢：

```kusto
Logs | join cluster("TelemetryCluster").database("Telemetry").Metrics on Request MachineId | ...
```

> [!NOTE]
> 若有指定叢集，就必須指定資料庫。

如需如何將來自數個資料庫的資料合併到查詢中的詳細資訊，請參閱[跨資料庫查詢](cross-cluster-or-database-queries.md)。

## <a name="next-steps"></a>後續步驟

- 檢視 [Kusto 查詢語言](samples.md?pivots=azuredataexplorer)的程式碼範例。

::: zone-end

::: zone pivot="azuremonitor"

若要了解 Kusto 查詢語言，最佳方式便是查看一些基本查詢，以獲得對於該語言的「感覺」。 這些查詢類似於 Azure 資料總管教學課程中所使用的查詢，但其會改為使用 Azure Log Analytics 工作區中通用資料表的資料。 

在 Azure 入口網站中使用 Log Analytics 即可執行這些查詢。 Log Analytics 是可供您用來撰寫記錄查詢的工具。 在 Azure 監視器中使用記錄資料，然後評估記錄查詢結果。 如果您不熟悉 Log Analytics，請完成 [Log Analytics 教學課程](/azure/azure-monitor/log-query/log-analytics-tutorial)。

本教學課程中的所有查詢都會使用 [Log Analytics 示範環境](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade)。 您可以使用自己的環境，但您可能不會有此處使用的部分資料表。 由於示範環境中的資料並非靜態的，因此查詢結果可能會與此處所示的結果略有不同。


## <a name="count-rows"></a>計算資料列數目

[InsightsMetrics](/azure/azure-monitor/reference/tables/insightsmetrics) 資料表包含深入解析 (例如適用於 VM 的 Azure 監視器和適用於容器的 Azure 監視器) 所收集的效能資料。 為了了解資料表有多大，我們會透過管道將其內容傳送至只會計算資料列的運算子。

查詢是一種資料來源 (通常是資料表名稱)，後面並選擇性地接著一組或多組管道字元和某些表格式運算子。 在此情況下，其會傳回 `InsightsMetrics` 資料表中的所有記錄，然後傳送至 [count 運算子](./countoperator.md)。 `count` 運算子會顯示結果，因為此運算子是查詢中的最後一個命令。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
InsightsMetrics | count
```

輸出如下：

|計數|
|-----|
|1,263,191|
    

## <a name="filter-by-boolean-expression-where"></a>依布林運算式篩選：*where*

[AzureActivity](/azure/azure-monitor/reference/tables/azureactivity) 資料表具有 Azure 活動記錄中的項目，可讓您深入了解 Azure 中發生的任何訂用帳戶層級或管理群組層級事件。 讓我們只查看特定一週內的 `Critical` 項目。

[where](/azure/data-explorer/kusto/query/whereoperator) 運算子在 Kusto 查詢語言中很常見。 `where` 會將資料表篩選成符合特定準則的資料列。 下列範例會使用多個命令。 首先，查詢會擷取資料表的所有記錄。 然後，只會篩選出資料中位於時間範圍內的記錄。 最後，只會篩選這些結果中具有 `Critical` 層級的記錄。

> [!NOTE]
> 除了使用 `TimeGenerated` 資料行在查詢中指定篩選之外，您還可以在 Log Analytics 中指定時間範圍。 如需詳細資訊，請參閱 [Azure 監視器 Log Analytics 中的記錄查詢領域和時間範圍](/azure/azure-monitor/log-query/scope)。

```kusto
AzureActivity
| where TimeGenerated > datetime(10-01-2020) and TimeGenerated < datetime(10-07-2020)
| where Level == 'Critical'
```

:::image type="content" source="images/tutorial/azure-monitor-where-results.png" lightbox="images/tutorial/azure-monitor-where-results.png" alt-text="螢幕擷取畫面：顯示 where 運算子範例的結果。":::

## <a name="select-a-subset-of-columns-project"></a>選取資料行的子集：*project*

使用 [project](./projectoperator.md) 即可只納入您想要的資料行。 以先前的範例為基礎，讓我們將輸出限制為特定資料行：

```kusto
AzureActivity
| where TimeGenerated > datetime(10-01-2020) and TimeGenerated < datetime(10-07-2020)
| where Level == 'Critical'
| project TimeGenerated, Level, OperationNameValue, ResourceGroup, _ResourceId
```

:::image type="content" source="images/tutorial/azure-monitor-project-results.png" lightbox="images/tutorial/azure-monitor-project-results.png" alt-text="螢幕擷取畫面：顯示 project 運算子範例的結果。":::

## <a name="show-n-rows-take"></a>顯示 *n* 個資料列：*take*

[NetworkMonitoring](/azure/azure-monitor/reference/tables/networkmonitoring) 包含 Azure 虛擬網路的監視資料。 讓我們使用 [take](./takeoperator.md) 運算子，查看該資料表中的十個隨機範例資料列。 [take](./takeoperator.md) 在顯示資料表中的特定數量資料列時不會依據特定順序：

```kusto
NetworkMonitoring
| take 10
| project TimeGenerated, Computer, SourceNetwork, DestinationNetwork, HighLatency, LowLatency
```

:::image type="content" source="images/tutorial/azure-monitor-take-results.png" lightbox="images/tutorial/azure-monitor-take-results.png" alt-text="螢幕擷取畫面：顯示 take 運算子範例的結果。":::

## <a name="order-results-sort-top"></a>為結果排序：*sort*、*top*

我們可以傳回依第一個排序依據時間所排列的最新五筆記錄，而非傳回隨機記錄：

```kusto
NetworkMonitoring
| sort by TimeGenerated desc
| take 5
| project TimeGenerated, Computer, SourceNetwork, DestinationNetwork, HighLatency, LowLatency
```

您可以改為使用 [top](./topoperator.md) 運算子來取得這個確切行為： 

```kusto
NetworkMonitoring
| top 5 by TimeGenerated desc
| project TimeGenerated, Computer, SourceNetwork, DestinationNetwork, HighLatency, LowLatency
```

:::image type="content" source="images/tutorial/azure-monitor-top-results.png" lightbox="images/tutorial/azure-monitor-top-results.png" alt-text="螢幕擷取畫面：顯示 top 運算子範例的結果。":::

## <a name="compute-derived-columns-extend"></a>計算衍生的資料行：*extend*

[extend](./projectoperator.md) 運算子與 [project](./projectoperator.md) 類似，但其會新增資料行集合，而不是加以取代。 您可以使用這兩個運算子，根據每個資料列上的計算來建立新的資料行。

[Perf](/azure/azure-monitor/reference/tables/perf) 資料表具有從執行 Log Analytics 代理程式的虛擬機器收集而來的效能資料。 

```kusto
Perf
| where ObjectName == "LogicalDisk" and CounterName == "Free Megabytes"
| project TimeGenerated, Computer, FreeMegabytes = CounterValue
| extend FreeGigabytes = FreeMegabytes / 1000
```

:::image type="content" source="images/tutorial/azure-monitor-extend-results.png" lightbox="images/tutorial/azure-monitor-extend-results.png" alt-text="螢幕擷取畫面：顯示 extend 運算子範例的結果。":::

## <a name="aggregate-groups-of-rows-summarize"></a>彙總資料列群組：*summarize*

[summarize](./summarizeoperator.md) 運算子會將在 `by` 子句中具有相同值的資料列群組在一起。 然後，會使用彙總函式 (例如 `count`) 將每個群組合併到單一資料列。 有一系列的[彙總函式](./summarizeoperator.md#list-of-aggregation-functions)可以使用。 您可以在一個 `summarize` 運算子中使用數個彙總函式來產生數個計算資料行。 

[SecurityEvent](/azure/azure-monitor/reference/tables/securityevent) 資料表包含安全性事件，例如在受監視電腦上啟動的登入和處理序。 您可以計算每一部電腦上發生在每個層級的事件數目。 在此範例中，會為每一部電腦和層級的組合產生一個資料列。 資料行則會包含事件的計數。

```kusto
SecurityEvent
| summarize count() by Computer, Level
```

:::image type="content" source="images/tutorial/azure-monitor-summarize-count-results.png" lightbox="images/tutorial/azure-monitor-summarize-count-results.png" alt-text="螢幕擷取畫面：顯示 summarize count 運算子範例的結果。":::

## <a name="summarize-by-scalar-values"></a>依純量值彙總

您可以用純量值 (例如數字和時間值) 進行彙總，但請使用 [bin()](./binfunction.md) 函式將資料列分組到不同的資料集合。 例如，如果您依 `TimeGenerated` 進行彙總，就會取得幾乎每個時間值的資料列。 使用 `bin()` 將這些值合併為小時或天。

[InsightsMetrics](/azure/azure-monitor/reference/tables/insightsmetrics) 資料表包含深入解析 (例如適用於 VM 的 Azure 監視器和適用於容器的 Azure 監視器) 所收集的效能資料。 下列查詢會顯示多部電腦的每小時平均處理器使用率：

```kusto
InsightsMetrics
| where Computer startswith "DC"
| where Namespace  == "Processor" and Name == "UtilizationPercentage"
| summarize avg(Val) by Computer, bin(TimeGenerated, 1h)
```

:::image type="content" source="images/tutorial/azure-monitor-summarize-avg-results.png" lightbox="images/tutorial/azure-monitor-summarize-avg-results.png" alt-text="螢幕擷取畫面：顯示 avg 運算子範例的結果。":::

## <a name="display-a-chart-or-table-render"></a>顯示圖表或資料表：*render*

[render](./renderoperator.md?pivots=azuremonitor) 運算子會指定查詢輸出的呈現方式。 根據預設，Log Analytics 會以資料表的形式呈現輸出。 執行查詢之後，您可以選取不同的圖表類型。 `render` 運算子適用於包含在通常慣用特定圖表類型的查詢中。

下列範例會顯示單一電腦的每小時平均處理器使用率。 其會以時間圖的形式呈現輸出。

```kusto
InsightsMetrics
| where Computer == "DC00.NA.contosohotels.com"
| where Namespace  == "Processor" and Name == "UtilizationPercentage"
| summarize avg(Val) by Computer, bin(TimeGenerated, 1h)
| render timechart
```

:::image type="content" source="images/tutorial/azure-monitor-render-results.png" lightbox="images/tutorial/azure-monitor-render-results.png" alt-text="螢幕擷取畫面：顯示 render 運算子範例的結果。":::

## <a name="work-with-multiple-series"></a>使用多個數列

如果您在 `summarize by` 子句中使用多個值，則圖表會針對每一組值顯示個別的數列：

```kusto
InsightsMetrics
| where Computer startswith "DC"
| where Namespace  == "Processor" and Name == "UtilizationPercentage"
| summarize avg(Val) by Computer, bin(TimeGenerated, 1h)
| render timechart
```

:::image type="content" source="images/tutorial/azure-monitor-render-multiple-results.png" lightbox="images/tutorial/azure-monitor-render-multiple-results.png" alt-text="螢幕擷取畫面：顯示 render 運算子搭配多個數列範例的結果。":::

## <a name="join-data-from-two-tables"></a>聯結來自兩個資料表的資料

如果您需要在單一查詢中從兩個資料表擷取資料，該怎麼辦？ 您可以使用 [join](/azure/data-explorer/kusto/query/joinoperator?pivots=azuremonitor) 運算子，將來自多個資料表的資料列合併至單一結果集。 每個資料表都必須有一個具有相符值的資料行，join 才能知道要比對哪些資料列。

[VMComputer](/azure/azure-monitor/reference/tables/vmcomputer) 是一個資料表，可供 Azure 監視器用於 VM 以儲存所監視虛擬機器的詳細資料。 [InsightsMetrics](/azure/azure-monitor/reference/tables/insightsmetrics) 包含從這些虛擬機器收集而來的效能資料。 在 *InsightsMetrics* 中收集的值是可用記憶體，但不是可用的記憶體百分比。 為了計算百分比，我們需要每個虛擬機器的實體記憶體。 該值位於 `VMComputer` 中。

下列範例查詢會使用 join 來執行這項計算。 [distinct](/azure/data-explorer/kusto/query/distinctoperator) 運算子會與 `VMComputer` 搭配使用，因為會定期從每部電腦收集詳細資料。 因此，資料表中的每一部電腦都會建立多個資料列。 這兩個資料表會使用 `Computer` 資料行加以聯結。 結果集中會建立一個資料列，其會針對 `InsightsMetrics` 中的每個資料列包含來自兩個資料表的資料行，並有 `Computer` 中的值，且此值符合 `VMComputer` 中 `Computer` 資料行中的相同值。

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

:::image type="content" source="images/tutorial/azure-monitor-join-results.png" lightbox="images/tutorial/azure-monitor-join-results.png" alt-text="螢幕擷取畫面：顯示 join 運算子範例的結果。":::

## <a name="assign-a-result-to-a-variable-let"></a>將結果指派給變數：*let*

使用 [let](./letstatement.md) 可讓查詢更方便閱讀和管理。 您可以使用這個運算子將查詢結果指派給變數以供稍後使用。 藉由使用 `let` 陳述式，上述範例中的查詢可重寫為：

 
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

:::image type="content" source="images/tutorial/azure-monitor-let-results.png" lightbox="images/tutorial/azure-monitor-let-results.png" alt-text="螢幕擷取畫面：顯示 let 運算子範例的結果。":::

## <a name="next-steps"></a>後續步驟

- 檢視 [Kusto 查詢語言](samples.md?pivots=azuremonitor)的程式碼範例。


::: zone-end
