---
title: 最上層嵌套運算子-Azure 資料總管
description: 本文說明 Azure 資料總管中的最上層嵌套運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f87606442dcc5d7c9e7e0fceec379c37169757c3
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83370777"
---
# <a name="top-nested-operator"></a>top-nested 運算子

產生階層式的首要結果，其中可根據上一層的值向下切入每一層。 

```kusto
T | top-nested 3 of Location with others="Others" by sum(MachinesNumber), top-nested 4 of bin(Timestamp,5m) by sum(MachinesNumber)
```

它適用于儀表板視覺效果案例，或必須回答聽起來像這樣的問題：「尋找什麼是版 k1 的前 N 個值（使用某種匯總）;針對每個專案，找出 K2 的前 M 個值（使用另一個匯總）。..."

**語法**

*T* `|` `top-nested` [*N1*] `of` *運算式*[ `with others=` *ConstExpr1*] `by` [*AggName1* `=` ] *Aggregation1* [ `asc`  |  `desc` ] [ `,` ...]

**引數**

針對每個頂端嵌套的規則：
* *N1*：要針對每個階層層級傳回的前幾個值數目。 選擇性（如果省略，則會傳回所有相異值）。
* *運算式方法：用*來選取最大值的運算式。 通常它是*T*中的資料行名稱，或是這類資料行上的某些分類收納作業（例如 `bin()` ）。 
* *ConstExpr1*：如果已指定，則針對適用的嵌套層級，將會附加額外的資料列，以保存最大值中未包含之其他值的匯總結果。
* *Aggregation1*：彙總函式的呼叫，可能是下列其中一個值： [sum （）](sum-aggfunction.md)、 [count （）](count-aggfunction.md)、 [max （）](max-aggfunction.md)、 [min （）](min-aggfunction.md)、 [dcount （）](dcountif-aggfunction.md)、 [avg （）](avg-aggfunction.md)、[百分位數（）](percentiles-aggfunction.md)、 [percentilew （）](percentiles-aggfunction.md)或這些匯總的任何 algebric 組合。
* `asc` 或 `desc` (預設值) 可能會出現，以控制實際上是從範圍的「下限」或「上限」進行選取。

**傳回**

階層資料表，其中包含輸入資料行，並針對每個專案產生新的資料行，以包含每個專案之相同層級的匯總結果。
資料行會以輸入資料行的相同順序排列，而新產生的資料行將會接近匯總資料行。 每一筆記錄都有一個階層結構，其中每個值都是在套用所有先前層級的先前所有頂端嵌套規則之後，然後在此輸出上套用目前層級的規則。
這展示層級 i 的前 n 個值是針對層級 i-1 中的每個值計算而得。
 
**提示**

* 在中使用資料行重新命名以取得*匯總*結果： T |位置的頂端嵌套3，依據 MachinesNumberForLocation = sum （MachinesNumber） ...。

* 傳回的記錄數目可能相當大;最多（*N1*+ 1） \* （*N2*+ 1） \* ... \* （*Nm*+ 1）（其中 m 是層級的數目，而*Ni*是層級 i 的最高計數）。

* 匯總必須接收包含彙總函式的數值資料行，這是上述的其中一個。

* 使用 `with others=` 選項，即可取得某些層級中不是前 N 個值的所有其他值的匯總值。

* 如果您不想要取得 `with others=` 某些層級，將會附加 null 值（針對 aggreagated 資料行和層級索引鍵，請參閱下面的範例）。


* 藉由附加額外的最上層嵌套語句（如下所示），可以針對選取的最上層內嵌候選項目傳回額外的資料行（請參閱下面的範例）：

```kusto
top-nested 2 of ...., ..., ..., top-nested of <additionalRequiredColumn1> by max(1), top-nested of <additionalRequiredColumn2> by max(1)
```

**範例**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| top-nested 2 of State by sum(BeginLat),
  top-nested 3 of Source by sum(BeginLat),
  top-nested 1 of EndLocation by sum(BeginLat)
```

|State|aggregated_State|來源|aggregated_Source|EndLocation|aggregated_EndLocation|
|---|---|---|---|---|---|
|KANSAS|87771.2355000001|執法機關|18744.823|FT SCOTT|264.858|
|KANSAS|87771.2355000001|公用|22855.6206|BUCKLIN|488.2457|
|KANSAS|87771.2355000001|Trained Spotter|21279.7083|SHARON SPGS|388.7404|
|德克薩斯州|123400.5101|公用|13650.9079|AMARILLO|246.2598|
|德克薩斯州|123400.5101|執法機關|37228.5966|PERRYTON|289.3178|
|德克薩斯州|123400.5101|Trained Spotter|13997.7124|CLAUDE|421.44|


* 還有其他範例：

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| top-nested 2 of State with others = "All Other States" by sum(BeginLat),
  top-nested 3 of Source by sum(BeginLat),
  top-nested 1 of EndLocation with others = "All Other End Locations" by  sum(BeginLat)


```

|State|aggregated_State|來源|aggregated_Source|EndLocation|aggregated_EndLocation|
|---|---|---|---|---|---|
|KANSAS|87771.2355000001|執法機關|18744.823|FT SCOTT|264.858|
|KANSAS|87771.2355000001|公用|22855.6206|BUCKLIN|488.2457|
|KANSAS|87771.2355000001|Trained Spotter|21279.7083|SHARON SPGS|388.7404|
|德克薩斯州|123400.5101|公用|13650.9079|AMARILLO|246.2598|
|德克薩斯州|123400.5101|執法機關|37228.5966|PERRYTON|289.3178|
|德克薩斯州|123400.5101|Trained Spotter|13997.7124|CLAUDE|421.44|
|KANSAS|87771.2355000001|執法機關|18744.823|所有其他結束位置|18479.965|
|KANSAS|87771.2355000001|公用|22855.6206|所有其他結束位置|22367.3749|
|KANSAS|87771.2355000001|Trained Spotter|21279.7083|所有其他結束位置|20890.9679|
|德克薩斯州|123400.5101|公用|13650.9079|所有其他結束位置|13404.6481|
|德克薩斯州|123400.5101|執法機關|37228.5966|所有其他結束位置|36939.2788|
|德克薩斯州|123400.5101|Trained Spotter|13997.7124|所有其他結束位置|13576.2724|
|KANSAS|87771.2355000001|||所有其他結束位置|24891.0836|
|德克薩斯州|123400.5101|||所有其他結束位置|58523.2932000001|
|所有其他狀態|1149279.5923|||所有其他結束位置|1149279.5923|


下列查詢會針對上述範例中使用的第一個層級顯示相同的結果：

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
 StormEvents
 | where State !in ('TEXAS', 'KANSAS')
 | summarize sum(BeginLat)
```

|sum_BeginLat|
|---|
|1149279.5923|


向上方的嵌套結果要求另一個資料行（事件層）： 

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| top-nested 2 of State by sum(BeginLat),    top-nested 2 of Source by sum(BeginLat),    top-nested 1 of EndLocation by sum(BeginLat), top-nested of EventType  by tmp = max(1)
| project-away tmp
```

|State|aggregated_State|來源|aggregated_Source|EndLocation|aggregated_EndLocation|EventType|
|---|---|---|---|---|---|---|
|KANSAS|87771.2355000001|Trained Spotter|21279.7083|SHARON SPGS|388.7404|Thunderstorm Wind|
|KANSAS|87771.2355000001|Trained Spotter|21279.7083|SHARON SPGS|388.7404|Hail|
|KANSAS|87771.2355000001|Trained Spotter|21279.7083|SHARON SPGS|388.7404|龍捲風|
|KANSAS|87771.2355000001|公用|22855.6206|BUCKLIN|488.2457|Hail|
|KANSAS|87771.2355000001|公用|22855.6206|BUCKLIN|488.2457|Thunderstorm Wind|
|KANSAS|87771.2355000001|公用|22855.6206|BUCKLIN|488.2457|Flood|
|德克薩斯州|123400.5101|Trained Spotter|13997.7124|CLAUDE|421.44|Hail|
|德克薩斯州|123400.5101|執法機關|37228.5966|PERRYTON|289.3178|Hail|
|德克薩斯州|123400.5101|執法機關|37228.5966|PERRYTON|289.3178|Flood|
|德克薩斯州|123400.5101|執法機關|37228.5966|PERRYTON|289.3178|Flash Flood|

若要依照最後一個嵌套層級（在此範例中為 EndLocation）排序結果，並為此層級中的每個值（每個群組）提供索引排序次序：

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| top-nested 2 of State  by sum(BeginLat),    top-nested 2 of Source by sum(BeginLat),    top-nested 4 of EndLocation by  sum(BeginLat)
| order by State , Source, aggregated_EndLocation
| summarize EndLocations = make_list(EndLocation, 10000) , endLocationSums = make_list(aggregated_EndLocation, 10000) by State, Source
| extend indicies = range(0, array_length(EndLocations) - 1, 1)
| mv-expand EndLocations, endLocationSums, indicies
```

|State|來源|EndLocations|endLocationSums|indicies|
|---|---|---|---|---|
|德克薩斯州|Trained Spotter|CLAUDE|421.44|0|
|德克薩斯州|Trained Spotter|AMARILLO|316.8892|1|
|德克薩斯州|Trained Spotter|DALHART|252.6186|2|
|德克薩斯州|Trained Spotter|PERRYTON|216.7826|3|
|德克薩斯州|執法機關|PERRYTON|289.3178|0|
|德克薩斯州|執法機關|LEAKEY|267.9825|1|
|德克薩斯州|執法機關|BRACKETTVILLE|264.3483|2|
|德克薩斯州|執法機關|GILMER|261.9068|3|
|KANSAS|Trained Spotter|SHARON SPGS|388.7404|0|
|KANSAS|Trained Spotter|ATWOOD|358.6136|1|
|KANSAS|Trained Spotter|LENORA|317.0718|2|
|KANSAS|Trained Spotter|SCOTT 市|307.84|3|
|KANSAS|公用|BUCKLIN|488.2457|0|
|KANSAS|公用|ASHLAND|446.4218|1|
|KANSAS|公用|保護|446.11|2|
|KANSAS|公用|MEADE 狀態公園|371.1|3|
