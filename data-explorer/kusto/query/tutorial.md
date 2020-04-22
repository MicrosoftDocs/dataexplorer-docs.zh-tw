---
title: 教程 - Azure 資料資源管理員 |微軟文件
description: 本文介紹了 Azure 數據資源管理器中的教程。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/23/2020
zone_pivot_group_filename: kusto/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: f8a5a7b8f96fdd75ccff5dcfe8499ab98a2d4380
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663328"
---
# <a name="tutorial"></a>教學課程

::: zone pivot="azuredataexplorer"

瞭解 Kusto 查詢語言的最佳方法是查看一些簡單的查詢,以便使用[帶有一些範例數據的資料庫](https://help.kusto.windows.net/Samples)獲取語言的"感覺"。 本文中演示的查詢應運行在該資料庫上。 此示例`StormEvents`資料庫中的表提供有關美國發生的風暴的一些資訊。

<!--
  TODO: Provide link to reference data we used originally in StormEvents
-->

<!--
  TODO: A few samples below reference non-existent tables, such as Events and Logs.
        We need to add these tables.
-->

## <a name="count-rows"></a>計算資料列數目

我們的範例資料庫有一個表`StormEvents`,稱為 。
為了找出它有多大,我們將將其內容輸送到一個運算元中,該運算符僅對行進行計數:

* *語法:* 查詢是數據源(通常是表名稱),可選後跟一個或多個管道字元和一些表格運算元。

```kusto
StormEvents | count
```

結果如下︰

|Count|
|-----|
|59066|
    
[計數運算子](./countoperator.md)。

## <a name="project-select-a-subset-of-columns"></a>項目:選擇欄位集

使用[專案](./projectoperator.md)只選取所需的欄。 請參閱下面的示例,該示例同時使用[專案和](./projectoperator.md) [take](./takeoperator.md)運算符。

## <a name="where-filtering-by-a-boolean-expression"></a>位置:由布爾表示式進行篩選

讓我們只看到 2007`California`年`flood`2 月的 中:

```kusto
StormEvents
| where StartTime > datetime(2007-02-01) and StartTime < datetime(2007-03-01)
| where EventType == 'Flood' and State == 'CALIFORNIA'
| project StartTime, EndTime , State , EventType , EpisodeNarrative
```

|StartTime|EndTime|State|EventType|劇集敘述|
|---|---|---|---|---|
|2007-02-19 00:00:00.0000000|2007-02-19 08:00:00.0000000|加州|Flood|19日淩晨,一個穿過南聖華金谷的正面系統給肯恩縣西部帶來了短暫的大雨。 據報導,塔夫脫附近的166號州公路發生了輕微洪水。|

## <a name="take-show-me-n-rows"></a>使用:顯示我 n 行

讓我們看看一些資料 - 範例 5 資料列中的內容為何？

```kusto
StormEvents
| take 5
| project  StartTime, EndTime, EventType, State, EventNarrative  
```

|StartTime|EndTime|EventType|State|EventNarrative|
|---|---|---|---|---|
|2007-09-18 20:00:00.0000000|2007-09-19 18:00:00.0000000|大雨|佛羅里達|在24小時內,在沿海的沃盧西亞縣部分地區降雨量高達9英寸。|
|2007-09-20 21:57:00.0000000|2007-09-20 22:05:00.0000000|龍捲風|佛羅里達|龍捲風在西湖北端的尤斯蒂斯鎮登陸。 龍捲風迅速增強為EF1強度,因為它向北移動西北通過尤斯蒂斯。 賽道只有不到兩英里長,最大寬度為300碼。  龍捲風摧毀了7所房屋。 27所房屋受到嚴重損壞,81所房屋報告受到輕微損壞。 沒有嚴重受傷,財產損失定為620萬美元。|
|2007-09-29 08:11:00.0000000|2007-09-29 08:11:00.0000000|噴水|大西洋南部|在墨爾本海灘東南的大西洋上形成一個噴水,並短暫地向岸邊移動。|
|2007-12-20 07:50:00.0000000|2007-12-20 07:53:00.0000000|Thunderstorm Wind|密西西比|許多大樹被吹倒,有些樹被吹倒在電線上。 東部亞當斯縣發生破壞。|
|2007-12-30 16:00:00.0000000|2007-12-30 16:05:00.0000000|Thunderstorm Wind|格魯吉亞|縣調度報告說,在206國道附近的昆西巴滕環路沿線,有幾棵樹被吹倒。 估計了砍伐樹木的費用。|

但是[,從表中以](./takeoperator.md)沒有特定順序顯示行,因此讓我們對它們進行排序。
* [限制](./takeoperator.md)是[取走](./takeoperator.md)的別名,將具有相同的效果。

## <a name="sort-and-top"></a>排序和頂部

* *語法:* 某些運算元的關鍵字(如`by`)引入了參數。
* `desc` = 遞減順序，`asc` = 遞增。

顯示前 n 個資料列 (依特定資料行排序)︰

```kusto
StormEvents
| top 5 by StartTime desc
| project  StartTime, EndTime, EventType, State, EventNarrative  
```

|StartTime|EndTime|EventType|State|EventNarrative|
|---|---|---|---|---|
|2007-12-31 22:30:00.0000000|2007-12-31 23:59:00.0000000|冬季風暴|密西根|這場大雪活動一直持續到元旦淩晨。|
|2007-12-31 22:30:00.0000000|2007-12-31 23:59:00.0000000|冬季風暴|密西根|這場大雪活動一直持續到元旦淩晨。|
|2007-12-31 22:30:00.0000000|2007-12-31 23:59:00.0000000|冬季風暴|密西根|這場大雪活動一直持續到元旦淩晨。|
|2007-12-31 23:53:00.0000000|2007-12-31 23:53:00.0000000|大風|加州|據報導,文圖拉縣的山區有陣風到每小時58英里左右的西北風。|
|2007-12-31 23:53:00.0000000|2007-12-31 23:53:00.0000000|大風|加州|暖泉 RAWS 感測器報告北風陣風到每小時 58 英里。|

使用[排序](./sortoperator.md),然後[採取](./takeoperator.md)運算子,也可以實現相同的

```kusto
StormEvents
| sort by StartTime desc
| take 5
| project  StartTime, EndLat, EventType, EventNarrative
```

## <a name="extend-compute-derived-columns"></a>延伸:計算派生欄

以計算每行中的值建立新欄:

```kusto
StormEvents
| limit 5
| extend Duration = EndTime - StartTime 
| project StartTime, EndTime, Duration, EventType, State
```

|StartTime|EndTime|Duration|EventType|State|
|---|---|---|---|---|
|2007-09-18 20:00:00.0000000|2007-09-19 18:00:00.0000000|22:00:00|大雨|佛羅里達|
|2007-09-20 21:57:00.0000000|2007-09-20 22:05:00.0000000|00:08:00|龍捲風|佛羅里達|
|2007-09-29 08:11:00.0000000|2007-09-29 08:11:00.0000000|00:00:00|噴水|大西洋南部|
|2007-12-20 07:50:00.0000000|2007-12-20 07:53:00.0000000|00:03:00|Thunderstorm Wind|密西西比|
|2007-12-30 16:00:00.0000000|2007-12-30 16:05:00.0000000|00:05:00|Thunderstorm Wind|格魯吉亞|

可以重用列名稱並將計算結果分配給同一列。
例如：

```kusto
print x=1
| extend x = x + 1, y = x
| extend x = x + 1
```

|x|y|
|---|---|
|3|1|

[Scalar 表示式](./scalar-data-types/index.md)`+`可以包括所有常用運算符(、、、、、、`-``*``/``%`函數)等。

## <a name="summarize-aggregate-groups-of-rows"></a>匯總:聚合行組

計算來自每個國家/地區的事件數:

```kusto
StormEvents
| summarize event_count = count() by State
```

[將](./summarizeoperator.md)`by`子句中具有相同值的行匯總在一起,然後使用聚合函數(`count`如 )將每個組合併為單個行。 因此,在這種情況下,每個狀態都有一個行,並且有一個列用於該狀態中的行計數。

聚合函數的範圍很廣,您可以在一個匯總運算符中使用其中幾個[函數](./summarizeoperator.md#list-of-aggregation-functions)來生成多個計算列。 例如,我們可以獲取每個州的風暴計數以及每個州唯一類型的風暴的總和,  
然後,我們可以使用[頂部](./topoperator.md)獲得風暴影響最大的狀態:

```kusto
StormEvents 
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State
| top 5 by StormCount desc
```

|State|風暴計數|風暴類型|
|---|---|---|
|德克薩斯州|4701|27|
|堪薩斯|3166|21|
|愛荷華州|2337|19|
|伊利諾伊州|2022|23|
|密蘇里州|2016|20|

summarize 的結果有：

* `by`中具名的每個資料行；
* 每個計算表達式的列;
* 每一組 `by` 值的一個資料列。

## <a name="summarize-by-scalar-values"></a>依純量值彙總

您可以在`by`子句中使用標量值(數位、時間或間隔)值,但需要將值放入條柱中。  
[bin()](./binfunction.md)函數對此很有用:

```kusto
StormEvents
| where StartTime > datetime(2007-02-14) and StartTime < datetime(2007-02-21)
| summarize event_count = count() by bin(StartTime, 1d)
```

這會將所有時間戳縮短為 1 天的間隔:

|StartTime|event_count|
|---|---|
|2007-02-14 00:00:00.0000000|180|
|2007-02-15 00:00:00.0000000|66|
|2007-02-16 00:00:00.0000000|164|
|2007-02-17 00:00:00.0000000|103|
|2007-02-18 00:00:00.0000000|22|
|2007-02-19 00:00:00.0000000|52|
|2007-02-20 00:00:00.0000000|60|

[bin()](./binfunction.md)與許多語言的[地板()](./floorfunction.md)功能相同。 它只是將每個值減少到提供的模量的最近倍數,以便[匯總](./summarizeoperator.md)可以將行分配給組。

## <a name="render-display-a-chart-or-table"></a>顯示:顯示圖表或表格

投影兩列,並將其用作圖表的 x 軸和 y 軸:

```kusto
StormEvents 
| summarize event_count=count(), mid = avg(BeginLat) by State 
| sort by mid
| where event_count > 1800
| project State, event_count
| render columnchart
```

:::image type="content" source="images/tour/060.png" alt-text="060":::

儘管我們在專案`mid`操作中刪除了,但如果我們希望圖表按該順序顯示國家/地區,我們仍然需要它。

嚴格地說,"呈現"是用戶端的一個功能,而不是查詢語言的一部分。 儘管如此,它還是集成到語言中,對於設想結果非常有用。


## <a name="timecharts"></a>時間表

回到數位條柱,讓我們顯示一個時間序列:

```kusto
StormEvents
| summarize event_count=count() by bin(StartTime, 1d)
| render timechart
```

:::image type="content" source="images/tour/080.png" alt-text="080":::

## <a name="multiple-series"></a>多個系列

在 `summarize by` 子句中使用多個值，為每組值建立個別的資料列：

```kusto
StormEvents 
| where StartTime > datetime(2007-06-04) and StartTime < datetime(2007-06-10) 
| where Source in ("Source","Public","Emergency Manager","Trained Spotter","Law Enforcement")
| summarize count() by bin(StartTime, 10h), Source
```

:::image type="content" source="images/tour/090.png" alt-text="090":::

只需將呈現術語加入到上述: `| render timechart`.

:::image type="content" source="images/tour/100.png" alt-text="100":::

請注意,`render timechart`使用第一列作為 x 軸,然後將其他列顯示為單獨的行。

## <a name="daily-average-cycle"></a>每日平均週期

活動在平均一天如何變化?

按一天計算事件,按小時計算。 請注意,我們使用`floor`而不是 bin:

```kusto
StormEvents
| extend hour = floor(StartTime % 1d , 1h)
| summarize event_count=count() by hour
| sort by hour asc
| render timechart
```

:::image type="content" source="images/tour/120.png" alt-text="120":::

目前,`render`沒有正確標記持續時間,但我們可以改`| render columnchart`用 :

:::image type="content" source="images/tour/110.png" alt-text="110":::

## <a name="compare-multiple-daily-series"></a>比較多個每日系列

不同州的活動在一天中的時間如何變化?

```kusto
StormEvents
| extend hour= floor( StartTime % 1d , 1h)
| where State in ("GULF OF MEXICO","MAINE","VIRGINIA","WISCONSIN","NORTH DAKOTA","NEW JERSEY","OREGON")
| summarize event_count=count() by hour, State
| render timechart
```

:::image type="content" source="images/tour/130.png" alt-text="130":::

除以`1h`將 x 軸轉換為小時數而不是持續時間:

```kusto
StormEvents
| extend hour= floor( StartTime % 1d , 1h)/ 1h
| where State in ("GULF OF MEXICO","MAINE","VIRGINIA","WISCONSIN","NORTH DAKOTA","NEW JERSEY","OREGON")
| summarize event_count=count() by hour, State
| render columnchart
```

:::image type="content" source="images/tour/140.png" alt-text="140":::

## <a name="join"></a>Join

如何查找兩個給定事件類型在它們發生的狀態?

您可以使用第一個事件類型和第二個事件類型拉取風暴事件,然後加入狀態上的兩個集。

```kusto
StormEvents
| where EventType == "Lightning"
| join (
    StormEvents 
    | where EventType == "Avalanche"
) on State  
| distinct State
```

:::image type="content" source="images/tour/145.png" alt-text="145":::

## <a name="user-session-example-of-join"></a>聯結的使用者作業階段範例

本節不使用表`StormEvents`。

假設您有的數據包含標記每個使用者會話開始和結束的事件,並且每個會話都有唯一的 ID。 

每個使用者會話持續多長時間?

通過使用`extend`提供兩個時間戳的別名,可以計算會話持續時間。

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

:::image type="content" source="images/tour/150.png" alt-text="150":::

在執行聯結之前，使用 `project` 只選取我們需要的資料行是相當好的做法。
在相同的子句中，我們會將時間戳記資料行重新命名。

## <a name="plot-a-distribution"></a>繪製分佈圖

有多少次不同長度的風暴?

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

:::image type="content" source="images/tour/170.png" alt-text="170":::

或使用`| render columnchart`:

:::image type="content" source="images/tour/160.png" alt-text="160":::

## <a name="percentiles"></a>百分位數

哪些持續時間範圍涵蓋不同百分比的風暴?

使用上述查詢,但取代為`render`:

```kusto
| summarize percentiles(duration, 5, 20, 50, 80, 95)
```

在這種情況下,我們沒有提供`by`子句,因此結果是一行:

:::image type="content" source="images/tour/180.png" alt-text="180":::

我們可以從中看到︰

* 5%的風暴持續時間小於500萬;
* 50%的風暴持續不到1h25米;
* 5%的風暴持續至少2小時50米。

要獲得每個狀態的單獨細分,我們只需通過兩個匯總運算符分別將狀態列:

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

:::image type="content" source="images/tour/190.png" alt-text="190":::

## <a name="let-assign-a-result-to-a-variable"></a>Let︰將結果指派給變數

使用[let](./letstatement.md)分隔上面「聯接」範例中的查詢運算式部分。 結果不變：

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

> 提示:在 Kusto 用戶端中,不要在部分之間放置空白行。 務必執行所有一切。

## <a name="combining-data-from-several-databases-in-a-query"></a>在查詢中合併來自多個資料庫的資料

有關詳細討論[,請參閱跨資料庫查詢](./cross-cluster-or-database-queries.md)

編寫樣式查詢時:

```kusto
Logs | where ...
```

名為 Logs 的表必須位於預設資料庫中。 如果要從其他資料庫存取表,請使用以下語法:

```kusto
database("db").Table
```

因此,如果您有名為 *「診斷*和*遙測」的*資料庫,並且想要關聯其某些資料,則可以編寫(假設*診斷*是默認資料庫)

```kusto
Logs | join database("Telemetry").Metrics on Request MachineId | ...
```

或者,如果預設資料庫是*遙測*

```kusto
union Requests, database("Diagnostics").Logs | ...
```
    
上述所有資料庫都假定兩個資料庫都駐留在當前連接到的群集中。 假設*遙測資料庫*屬於另一個名為*TelemetryCluster.kusto.windows.net*的群集,然後訪問它需要它

```kusto
Logs | join cluster("TelemetryCluster").database("Telemetry").Metrics on Request MachineId | ...
```

> 注意:指定群組時,資料庫是強制性的

::: zone-end

::: zone pivot="azuremonitor"

Azure 監視器不支援此功能

::: zone-end