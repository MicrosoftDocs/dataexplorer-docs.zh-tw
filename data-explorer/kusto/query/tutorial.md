---
title: 教學課程：在 Azure 中 Kusto 查詢資料總管 & Azure 監視器
description: 本教學課程說明如何使用 Kusto 查詢語言中的查詢，以符合 Azure 資料總管和 Azure 監視器中常見的查詢需求。
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
ms.openlocfilehash: 8a47c51aa7924a28b27602056ea869bfd7a09936
ms.sourcegitcommit: faa747df81c49b96d173dbd5a28d2ca4f3a2db5f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95783674"
---
# <a name="tutorial-use-kusto-queries-in-azure-data-explorer-and-azure-monitor"></a>教學課程：在 Azure 資料總管和 Azure 監視器中使用 Kusto 查詢

::: zone pivot="azuredataexplorer"

瞭解 Kusto 查詢語言的最佳方式，就是查看一些基本查詢，以取得語言的「感覺」。 我們建議使用 [具有一些範例資料的資料庫](https://help.kusto.windows.net/Samples)。 本教學課程中示範的查詢應該在該資料庫上執行。 `StormEvents`範例資料庫中的資料表提供有關在美國發生的風暴的一些資訊。

## <a name="count-rows"></a>計算資料列數目

我們的範例資料庫有一個名為的資料表 `StormEvents` 。 為了找出資料表的大小，我們會將其內容輸送到只計算資料表中資料列的運算子。 

*語法附注*：查詢是資料來源 (通常是資料表名稱) ，選擇性地接著一或多個管道字元和某些表格式運算子。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents | count
```

輸出如下：

|計數|
|-----|
|59066|
    
如需詳細資訊，請參閱 [count 運算子](./countoperator.md)。

## <a name="select-a-subset-of-columns-project"></a>選取資料行的子集： *project*

使用 [project](./projectoperator.md) 只挑選您想要的資料行。 請參閱下列範例，其會使用 [專案](./projectoperator.md) 和 [take](./takeoperator.md) 運算子。

## <a name="filter-by-boolean-expression-where"></a>依布林運算式篩選： *where*

讓我們在 `flood` `California` 2007 年2月僅查看中的事件：

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
|2007-02-19 00：00：00.0000000|2007-02-19 08：00：00.0000000|加州|Flood|在南 San Joaquin 的正面系統中，移動了一小段時間，在19日的早期早上時，會有一小段高達西歐的國家/地區間距。 在接近 Taft 的州高速公路166之間回報輕微氾濫。|

## <a name="show-n-rows-take"></a>顯示 *n* 個數據列： *take*

讓我們來看看一些資料。 五個數據列的隨機範例有哪些？

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| take 5
| project  StartTime, EndTime, EventType, State, EventNarrative  
```

輸出如下：

|StartTime|EndTime|EventType|州|EventNarrative|
|---|---|---|---|---|
|2007-09-18 20：00：00.0000000|2007-09-19 18：00：00.0000000|大雨|佛羅里達|在近水樓臺 Volusia 國家/地區的每個部分，在24小時的期間內，最多會有9英寸的 rain。|
|2007-09-20 21：57：00.0000000|2007-09-20 22：05：00.0000000|龍捲風|佛羅里達|在北西部 Crooked Lake 的 Eustis 中，有一次龍捲風觸及。 當您在 Eustis 移動北美洲時，會快速更至 EF1 強度。 此播放軌的長度為兩英里以下，最大寬度為300碼。  龍捲風終結7家庭。 20個家庭收到重大損害，而81家中回報了輕微的損毀。 在 $6200000 設定的嚴重傷害和財產損毀。|
|2007-09-29 08：11：00.0000000|2007-09-29 08：11：00.0000000|Waterspout|大西洋南部|在墨爾本海灘的大西洋東南部中形成的 waterspout，並短暫地移往支援腳步。|
|2007-12-20 07：50：00.0000000|2007-12-20 07：53：00.0000000|Thunderstorm Wind|密西西比|許多大型樹狀結構都有一些電源線的下降。 在東部 Adams 縣中發生損毀。|
|2007-12-30 16：00：00.0000000|2007-12-30 16：05：00.0000000|Thunderstorm Wind|格魯吉亞|縣市分派報告了數個樹狀結構，並在接近州道路206的 Quincey Batten 迴圈下進行。 已預估移除樹狀結構的成本。|

但是 [，請以](./takeoperator.md) 沒有特定順序的方式來顯示資料表中的資料列，讓我們加以排序。  ([limit](./takeoperator.md) 是 [take](./takeoperator.md) 的別名，而且具有相同的效果。 ) 

## <a name="order-results-sort-top"></a>訂單結果： *sort*、 *top*

* *語法附注*：某些運算子具有像這樣的關鍵字所引進的參數 `by` 。
* 在下列範例中， `desc` 訂單會以遞減順序排序，並 `asc` 以遞增順序排序結果。

顯示前 *n* 個數據列（依特定資料行排序）：

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| top 5 by StartTime desc
| project  StartTime, EndTime, EventType, State, EventNarrative  
```

輸出如下：

|StartTime|EndTime|EventType|州|EventNarrative|
|---|---|---|---|---|
|2007-12-31 22：30：00.0000000|2007-12-31 23：59：00.0000000|冬季風暴|密西根|這個繁重的雪活動會延續到新年的提早早上。|
|2007-12-31 22：30：00.0000000|2007-12-31 23：59：00.0000000|冬季風暴|密西根|這個繁重的雪活動會延續到新年的提早早上。|
|2007-12-31 22：30：00.0000000|2007-12-31 23：59：00.0000000|冬季風暴|密西根|這個繁重的雪活動會延續到新年的提早早上。|
|2007-12-31 23：53：00.0000000|2007-12-31 23：53：00.0000000|高風|加州|在 Ventura 縣（山脈）中回報到 58 >mph 的北部至東北部接近尾聲 gusting。|
|2007-12-31 23：53：00.0000000|2007-12-31 23：53：00.0000000|高風|加州|暖彈簧 RAWS 感應器回報北邊接近尾聲 gusting 至 58 >mph。|

您可以使用 [sort](./sortoperator.md)來達到相同的結果，然後 [採取](./takeoperator.md)下列動作：

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| sort by StartTime desc
| take 5
| project  StartTime, EndTime, EventType, EventNarrative
```

## <a name="compute-derived-columns-extend"></a>計算衍生的資料行： *擴充*

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
|2007-09-18 20：00：00.0000000|2007-09-19 18：00：00.0000000|22:00:00|大雨|佛羅里達|
|2007-09-20 21：57：00.0000000|2007-09-20 22：05：00.0000000|00:08:00|龍捲風|佛羅里達|
|2007-09-29 08：11：00.0000000|2007-09-29 08：11：00.0000000|00:00:00|Waterspout|大西洋南部|
|2007-12-20 07：50：00.0000000|2007-12-20 07：53：00.0000000|00:03:00|Thunderstorm Wind|密西西比|
|2007-12-30 16：00：00.0000000|2007-12-30 16：05：00.0000000|00:05:00|Thunderstorm Wind|格魯吉亞|

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

純量[運算式](./scalar-data-types/index.md)可包含所有一般運算子， (`+` 、 `-` 、 `*` 、 `/` 、 `%`) 和可用函式的範圍。

## <a name="aggregate-groups-of-rows-summarize"></a>匯總資料列群組： *摘要*

計算每個國家/地區中發生的事件數目：

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| summarize event_count = count() by State
```

[摘要](./summarizeoperator.md) 群組在子句中具有相同值的資料列 `by` ，然後使用彙總函式 (例如， `count`) ，將每個群組合並成單一資料列。 在此情況下，每個狀態都有一個資料列，以及該狀態的資料列計數的資料行。

有一系列的 [彙總函式](./summarizeoperator.md#list-of-aggregation-functions) 可供使用。 您可以在一個運算子中使用數個彙總函式 `summarize` 來產生數個計算資料行。 例如，我們可以取得每個狀態的風暴計數，也可以取得每個狀態的唯一風暴類型的總和。 然後，我們可以使用 [top](./topoperator.md) 來取得最常受到影響的狀態：

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
|堪薩斯|3166|21|
|愛荷華州|2337|19|
|伊利諾州|2022|23|
|密蘇里州|2016|20|

在運算子的結果中 `summarize` ：

* 每個資料行的命名方式為 `by` 。
* 每個計算運算式都有一個資料行。
* 每個值的組合 `by` 都有一個資料列。

## <a name="summarize-by-scalar-values"></a>依純量值彙總

您可以在子句中使用純量 (數值、時間或間隔) 值 `by` ，但您會想要使用 [bin ( # B3 ](./binfunction.md) 函式將值放入 bin：

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| where StartTime > datetime(2007-02-14) and StartTime < datetime(2007-02-21)
| summarize event_count = count() by bin(StartTime, 1d)
```

此查詢會將所有時間戳記縮減為一天的間隔：

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

<a name="displaychartortable"></a>
## <a name="display-a-chart-or-table-render"></a>顯示圖表或 *資料表：轉譯*

您可以投影兩個數據行，並將其作為 X 軸和圖表的 y 軸：

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents 
| summarize event_count=count(), mid = avg(BeginLat) by State 
| sort by mid
| where event_count > 1800
| project State, event_count
| render columnchart
```

:::image type="content" source="images/tutorial/event-counts-state.png" alt-text="螢幕擷取畫面，顯示依州/省的風暴事件計數的直條圖。":::

雖然我們已 `mid` 在作業中移除 `project` ，但如果我們想要讓圖表以該順序顯示國家/地區，仍然需要它。

嚴格來說， `render` 它是用戶端的一項功能，而不是查詢語言的一部分。 同樣地，它也已整合至語言，而且對您的結果進行構想很有用。


## <a name="timecharts"></a>時間表

回到數值箱，讓我們來顯示時間序列：

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| summarize event_count=count() by bin(StartTime, 1d)
| render timechart
```

:::image type="content" source="images/tutorial/time-series-start-bin.png" alt-text="依時間分類收納之事件折線圖的螢幕擷取畫面。":::

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

您只需將 `render` 詞彙新增至上述範例： `| render timechart` 。

:::image type="content" source="images/tutorial/line-count-source.png" alt-text="顯示依來源的折線圖計數的螢幕擷取畫面。":::

請注意， `render timechart` 使用第一個資料行做為 X 軸，然後將其他資料行顯示為個別的線條。

## <a name="daily-average-cycle"></a>每日平均週期

活動在平均當日有何不同？

依時間模數一天計算事件數目，分類收納為小時。 在這裡，我們會使用 `floor` 而非 `bin` ：

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| extend hour = floor(StartTime % 1d , 1h)
| summarize event_count=count() by hour
| sort by hour asc
| render timechart
```

:::image type="content" source="images/tutorial/time-count-hour.png" alt-text="依小時顯示時間圖表計數的螢幕擷取畫面。":::

目前 `render` 不會正確地標示持續時間，但我們可以改用 `| render columnchart` ：

:::image type="content" source="images/tutorial/column-count-hour.png" alt-text="顯示依小時的直條圖計數的螢幕擷取畫面。":::

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

:::image type="content" source="images/tutorial/time-hour-state.png" alt-text="依小時和狀態時間圖表的螢幕擷取畫面。":::

除 `1h` 以將 X 軸轉換成小時數，而不是持續時間：

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| extend hour= floor( StartTime % 1d , 1h)/ 1h
| where State in ("GULF OF MEXICO","MAINE","VIRGINIA","WISCONSIN","NORTH DAKOTA","NEW JERSEY","OREGON")
| summarize event_count=count() by hour, State
| render columnchart
```

:::image type="content" source="images/tutorial/column-hour-state.png" alt-text="依小時和州顯示直條圖的螢幕擷取畫面。":::

## <a name="join-data-types"></a>聯結資料類型

您要如何找到兩個特定的事件種類，以及每個事件種類發生的狀態為何？

您可以使用第一個 `EventType` 和第二個來提取風暴事件 `EventType` ，然後將兩個集合聯結在 `State` ：

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

:::image type="content" source="images/tutorial/join-events-lightning-avalanche.png" alt-text="顯示加入事件閃電和大量的螢幕擷取畫面。":::

## <a name="user-session-example-of-join"></a>*Join* 的使用者會話範例

本節不會使用 `StormEvents` 資料表。

假設您有包含事件的資料，這些事件會將每個使用者會話的開始和結束標記為每個會話的唯一識別碼。 

您要如何得知每個使用者會話的持續時間？

您可以使用 `extend` 來提供這兩個時間戳記的別名，然後計算會話持續時間：

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

:::image type="content" source="images/tutorial/user-session-extend.png" alt-text="使用者會話擴充結果資料表的螢幕擷取畫面。":::

在 `project` 執行聯結之前，最好先使用來選取您需要的資料行。 在相同的子句中，重新命名資料 `timestamp` 行。

## <a name="plot-a-distribution"></a>繪製分佈圖

返回 `StormEvents` 資料表，有多少個風暴有不同的長度？

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

:::image type="content" source="images/tutorial/event-count-duration.png" alt-text="依持續時間時間圖表事件計數之結果的螢幕擷取畫面。":::

或者，您可以使用 `| render columnchart` ：

:::image type="content" source="images/tutorial/column-event-count-duration.png" alt-text="依持續時間時間圖表之事件計數的直條圖螢幕擷取畫面。":::

## <a name="percentiles"></a>百分位數

我們發現不同的持續時間範圍是在不同的風暴百分比中？

若要取得此資訊，請使用上述查詢，但將取代 `render` 為：

```kusto
| summarize percentiles(duration, 5, 20, 50, 80, 95)
```

在此情況下，我們並未使用 `by` 子句，因此輸出是單一資料列：

:::image type="content" source="images/tutorial/summarize-percentiles-duration.png" lightbox="images/tutorial/summarize-percentiles-duration.png" alt-text="依持續時間摘要百分位數之結果資料表的螢幕擷取畫面。":::

我們可以從下列輸出中看出：

* 5% 的風暴持續時間少於5分鐘。
* 50% 的風暴持續時間不到一小時和25分鐘。
* 5% 的風暴持續時間至少有兩個小時和50分鐘。

若要針對每個狀態取得個別的明細，請 `state` 分別使用這兩個運算子的資料行 `summarize` ：

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

:::image type="content" source="images/tutorial/summarize-percentiles-state.png" alt-text="資料表摘要百分位數持續時間（依州）。":::

## <a name="assign-a-result-to-a-variable-let"></a>將結果指派給變數： *let*

使用「 [讓](./letstatement.md) 」分隔上述範例中的查詢運算式部分 `join` 。 結果不變：

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
> 在 [Kusto Explorer] 中，若要執行整個查詢，請勿在查詢的各個部分之間加入空白行。

## <a name="combine-data-from-several-databases-in-a-query"></a>在查詢中結合數個資料庫的資料

在下列查詢中， `Logs` 資料表必須位於您的預設資料庫中：

```kusto
Logs | where ...
```

若要存取不同資料庫中的資料表，請使用下列語法：

```kusto
database("db").Table
```

例如，如果您有一個名為 `Diagnostics` 的資料庫， `Telemetry` 而且您想要讓兩個數據表中的部分資料相互關聯，您可以使用下列查詢 (假設 `Diagnostics` 是您的預設資料庫) ：

```kusto
Logs | join database("Telemetry").Metrics on Request MachineId | ...
```

如果您的預設資料庫為，請使用此查詢 `Telemetry` ：

```kusto
union Requests, database("Diagnostics").Logs | ...
```
    
上述兩個查詢會假設兩個資料庫都位於您目前所連接的叢集中。 如果 `Telemetry` 資料庫位於名為 *TelemetryCluster.kusto.windows.net* 的叢集中，請使用下列查詢：

```kusto
Logs | join cluster("TelemetryCluster").database("Telemetry").Metrics on Request MachineId | ...
```

> [!NOTE]
> 當指定叢集時，資料庫是強制性的。

如需在查詢中結合數個資料庫之資料的詳細資訊，請參閱 [跨資料庫查詢](cross-cluster-or-database-queries.md)。

## <a name="next-steps"></a>後續步驟

- 查看 [Kusto 查詢語言](samples.md?pivots=azuredataexplorer)的程式碼範例。

::: zone-end

::: zone pivot="azuremonitor"

瞭解 Kusto 查詢語言的最佳方式，就是查看一些基本查詢，以取得語言的「感覺」。 這些查詢類似于 Azure 資料總管教學課程中使用的查詢，但會改為使用 Azure Log Analytics 工作區中通用資料表的資料。 

使用 Azure 入口網站中的 Log Analytics 來執行這些查詢。 Log Analytics 是您可用來撰寫記錄查詢的工具。 使用 Azure 監視器中的記錄資料，然後評估記錄查詢結果。 如果您不熟悉 Log Analytics，請完成 [Log analytics 教學](/azure/azure-monitor/log-query/log-analytics-tutorial)課程。

本教學課程中的所有查詢都會使用 [Log Analytics 示範環境](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade)。 您可以使用自己的環境，但您可能沒有在這裡使用的一些資料表。 因為示範環境中的資料不是靜態的，所以您的查詢結果可能會與此處顯示的結果稍有不同。


## <a name="count-rows"></a>計算資料列數目

[InsightsMetrics](/azure/azure-monitor/reference/tables/insightsmetrics)資料表包含由見解收集的效能資料，例如容器的適用於 VM 的 Azure 監視器和 Azure 監視器。 為了找出資料表的大小，我們會將其內容輸送到只計算資料列的運算子。

查詢是資料來源 (通常是) 資料表名稱，並選擇性地接著一或多個管道字元和某些表格式運算子。 在此情況下，會傳回資料表中的所有記錄， `InsightsMetrics` 然後傳送至 [count 運算子](./countoperator.md)。 `count`運算子會顯示結果，因為運算子是查詢中的最後一個命令。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
InsightsMetrics | count
```

輸出如下：

|計數|
|-----|
|1263191|
    

## <a name="filter-by-boolean-expression-where"></a>依布林運算式篩選： *where*

[AzureActivity](/azure/azure-monitor/reference/tables/azureactivity)資料表具有來自 azure 活動記錄的專案，可讓您深入瞭解在 azure 中發生的任何訂用帳戶層級或管理群組層級事件。 讓我們僅查看 `Critical` 特定周內的專案。

[Where](/azure/data-explorer/kusto/query/whereoperator)運算子在 Kusto 查詢語言中是共通的。 `where` 將資料表篩選為符合特定準則的資料列。 下列範例會使用多個命令。 首先，查詢會捕獲資料表的所有記錄。 然後，它只會針對時間範圍內的記錄篩選資料。 最後，它只會針對具有層級的記錄篩選這些結果 `Critical` 。

> [!NOTE]
> 除了在查詢中使用資料行指定篩選 `TimeGenerated` ，您可以在 Log Analytics 中指定時間範圍。 如需詳細資訊，請參閱 [Azure 監視器 Log Analytics 中的記錄查詢範圍和時間範圍](/azure/azure-monitor/log-query/scope)。

```kusto
AzureActivity
| where TimeGenerated > datetime(10-01-2020) and TimeGenerated < datetime(10-07-2020)
| where Level == 'Critical'
```

:::image type="content" source="images/tutorial/azure-monitor-where-results.png" lightbox="images/tutorial/azure-monitor-where-results.png" alt-text="顯示 where 運算子範例之結果的螢幕擷取畫面。":::

## <a name="select-a-subset-of-columns-project"></a>選取資料行的子集： *project*

使用 [ [專案](./projectoperator.md) ] 只包含您想要的資料行。 以上述範例為基礎，讓我們將輸出限制為某些資料行：

```kusto
AzureActivity
| where TimeGenerated > datetime(10-01-2020) and TimeGenerated < datetime(10-07-2020)
| where Level == 'Critical'
| project TimeGenerated, Level, OperationNameValue, ResourceGroup, _ResourceId
```

:::image type="content" source="images/tutorial/azure-monitor-project-results.png" lightbox="images/tutorial/azure-monitor-project-results.png" alt-text="顯示專案運算子範例結果的螢幕擷取畫面。":::

## <a name="show-n-rows-take"></a>顯示 *n* 個數據列： *take*

[NetworkMonitoring](/azure/azure-monitor/reference/tables/networkmonitoring) 包含 Azure 虛擬網路的監視資料。 讓我們使用 [take](./takeoperator.md) 運算子來查看該資料表中五個隨機範例資料列。 [Take 會](./takeoperator.md)以沒有特定順序的方式從資料表中顯示特定數目的資料列：

```kusto
NetworkMonitoring
| take 10
| project TimeGenerated, Computer, SourceNetwork, DestinationNetwork, HighLatency, LowLatency
```

:::image type="content" source="images/tutorial/azure-monitor-take-results.png" lightbox="images/tutorial/azure-monitor-take-results.png" alt-text="顯示 take 運算子範例結果的螢幕擷取畫面。":::

## <a name="order-results-sort-top"></a>訂單結果： *sort*、 *top*

我們可以透過第一次依時間排序來傳回最新五筆記錄，而不是隨機記錄：

```kusto
NetworkMonitoring
| sort by TimeGenerated desc
| take 5
| project TimeGenerated, Computer, SourceNetwork, DestinationNetwork, HighLatency, LowLatency
```

您可以改為使用 [top](./topoperator.md) 運算子來取得此確切行為： 

```kusto
NetworkMonitoring
| top 5 by TimeGenerated desc
| project TimeGenerated, Computer, SourceNetwork, DestinationNetwork, HighLatency, LowLatency
```

:::image type="content" source="images/tutorial/azure-monitor-top-results.png" lightbox="images/tutorial/azure-monitor-top-results.png" alt-text="顯示 top 運算子範例結果的螢幕擷取畫面。":::

## <a name="compute-derived-columns-extend"></a>計算衍生的資料行： *擴充*

[擴充](./projectoperator.md)運算子類似于[專案](./projectoperator.md)，但是它會加入至資料行集合，而不是取代它們。 您可以使用這兩個運算子，根據每個資料列上的計算來建立新的資料行。

效能 [資料表有](/azure/azure-monitor/reference/tables/perf) 從執行 Log Analytics 代理程式的虛擬機器收集的效能資料。 

```kusto
Perf
| where ObjectName == "LogicalDisk" and CounterName == "Free Megabytes"
| project TimeGenerated, Computer, FreeMegabytes = CounterValue
| extend FreeGigabytes = FreeMegabytes / 1000
```

:::image type="content" source="images/tutorial/azure-monitor-extend-results.png" lightbox="images/tutorial/azure-monitor-extend-results.png" alt-text="顯示擴充運算子範例結果的螢幕擷取畫面。":::

## <a name="aggregate-groups-of-rows-summarize"></a>匯總資料列群組： *摘要*

[摘要](./summarizeoperator.md)運算子會將在子句中具有相同值的資料列群組在一起 `by` 。 然後，它會使用類似的彙總函式 `count` 來合併單一資料列中的每個群組。 有一系列的 [彙總函式](./summarizeoperator.md#list-of-aggregation-functions) 可供使用。 您可以在一個運算子中使用數個彙總函式 `summarize` 來產生數個計算資料行。 

[SecurityEvent](/azure/azure-monitor/reference/tables/securityevent)資料表包含安全性事件，例如在受監視電腦上啟動的登入和處理常式。 您可以計算每一部電腦上發生各層級的事件數目。 在此範例中，會針對每一部電腦和層級組合產生一個資料列。 包含事件計數的資料行。

```kusto
SecurityEvent
| summarize count() by Computer, Level
```

:::image type="content" source="images/tutorial/azure-monitor-summarize-count-results.png" lightbox="images/tutorial/azure-monitor-summarize-count-results.png" alt-text="顯示摘要計數運算子範例結果的螢幕擷取畫面。":::

## <a name="summarize-by-scalar-values"></a>依純量值彙總

您可以透過純量值（例如數位和時間值）來匯總，但您應該使用 [bin ( # B1 ](./binfunction.md) 函數將資料列分組為不同的資料集。 例如，如果您匯總了 `TimeGenerated` ，就會取得幾乎每個時間值的資料列。 使用 `bin()` 將這些值合併為小時或天。

[InsightsMetrics](/azure/azure-monitor/reference/tables/insightsmetrics)資料表包含由見解收集的效能資料，例如容器的適用於 VM 的 Azure 監視器和 Azure 監視器。 下列查詢顯示多部電腦的每小時平均處理器使用率：

```kusto
InsightsMetrics
| where Computer startswith "DC"
| where Namespace  == "Processor" and Name == "UtilizationPercentage"
| summarize avg(Val) by Computer, bin(TimeGenerated, 1h)
```

:::image type="content" source="images/tutorial/azure-monitor-summarize-avg-results.png" lightbox="images/tutorial/azure-monitor-summarize-avg-results.png" alt-text="顯示 avg 運算子範例之結果的螢幕擷取畫面。":::

## <a name="display-a-chart-or-table-render"></a>顯示圖表或 *資料表：轉譯*

[Render](./renderoperator.md?pivots=azuremonitor)運算子會指定如何轉譯查詢的輸出。 根據預設，Log Analytics 會將輸出轉譯成資料表。 執行查詢之後，您可以選取不同的圖表類型。 `render`運算子適用于在通常偏好特定圖表類型的查詢中包含。

下列範例顯示單一電腦的每小時平均處理器使用率。 它會以時間圖表的形式呈現輸出。

```kusto
InsightsMetrics
| where Computer == "DC00.NA.contosohotels.com"
| where Namespace  == "Processor" and Name == "UtilizationPercentage"
| summarize avg(Val) by Computer, bin(TimeGenerated, 1h)
| render timechart
```

:::image type="content" source="images/tutorial/azure-monitor-render-results.png" lightbox="images/tutorial/azure-monitor-render-results.png" alt-text="顯示轉譯運算子範例之結果的螢幕擷取畫面。":::

## <a name="work-with-multiple-series"></a>使用多個數列

如果您在子句中使用多個值 `summarize by` ，則圖表會顯示每一組值的個別數列：

```kusto
InsightsMetrics
| where Computer startswith "DC"
| where Namespace  == "Processor" and Name == "UtilizationPercentage"
| summarize avg(Val) by Computer, bin(TimeGenerated, 1h)
| render timechart
```

:::image type="content" source="images/tutorial/azure-monitor-render-multiple-results.png" lightbox="images/tutorial/azure-monitor-render-multiple-results.png" alt-text="顯示轉譯運算子之結果的螢幕擷取畫面，其中包含多個數列範例。":::

## <a name="join-data-from-two-tables"></a>從兩個數據表聯結資料

如果您需要從單一查詢的兩個數據表中取出資料，該怎麼辦？ 您可以使用 [join](/azure/data-explorer/kusto/query/joinoperator?pivots=azuremonitor) 運算子，在單一結果集中合併多個資料表的資料列。 每個資料表都必須具有具有相符值的資料行，以便讓聯結瞭解要比對的資料列。

[VMComputer](/azure/azure-monitor/reference/tables/vmcomputer) 是一種資料表，Azure 監視器使用 vm 來儲存其所監視之虛擬機器的詳細資料。 [InsightsMetrics](/azure/azure-monitor/reference/tables/insightsmetrics) 包含從這些虛擬機器收集的效能資料。 *InsightsMetrics* 中所收集的一個值是可用的記憶體，但不是可用的記憶體百分比。 為了計算百分比，我們需要每部虛擬機器的實體記憶體。 該值為 in `VMComputer` 。

下列範例查詢會使用聯結來執行這項計算。 [Distinct](/azure/data-explorer/kusto/query/distinctoperator)運算子是與一起使用， `VMComputer` 因為每一部電腦都會定期收集詳細資料。 因此，會為數據表中的每一部電腦建立多個資料列。 這兩個數據表會使用資料 `Computer` 行聯結。 結果集中會建立一個資料列，其中包含每個資料表的資料行，其中的值符合中資料行 `InsightsMetrics` `Computer` 的相同值 `Computer` `VMComputer` 。

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

:::image type="content" source="images/tutorial/azure-monitor-join-results.png" lightbox="images/tutorial/azure-monitor-join-results.png" alt-text="顯示聯結運算子範例結果的螢幕擷取畫面。":::

## <a name="assign-a-result-to-a-variable-let"></a>將結果指派給變數： *let*

使用 [let 讓](./letstatement.md) 查詢更容易閱讀及管理。 您可以使用這個運算子，將查詢的結果指派給您稍後可以使用的變數。 藉由使用 `let` 語句，可以將上述範例中的查詢重寫為：

 
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

:::image type="content" source="images/tutorial/azure-monitor-let-results.png" lightbox="images/tutorial/azure-monitor-let-results.png" alt-text="顯示 let 運算子範例結果的螢幕擷取畫面。":::

## <a name="next-steps"></a>後續步驟

- 查看 [Kusto 查詢語言](samples.md?pivots=azuremonitor)的程式碼範例。


::: zone-end
