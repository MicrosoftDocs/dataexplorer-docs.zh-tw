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
ms.openlocfilehash: a2a8f4fa92a7b8722097ec3595674b855a90f216
ms.sourcegitcommit: 41cd88acc1fd79f320a8fe8012583d4c8522db78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/02/2020
ms.locfileid: "84294656"
---
# <a name="top-nested-operator"></a>top-nested 運算子

產生階層式匯總和前幾個值選取專案，其中每個層級都是上一個等級的精簡。

```kusto
T | top-nested 3 of Location with others="Others" by sum(MachinesNumber), top-nested 4 of bin(Timestamp,5m) by sum(MachinesNumber)
```

`top-nested`運算子會接受表格式資料做為輸入，以及一個或多個匯總子句。
第一個匯總子句（最左邊）會根據這些記錄上某個運算式的唯一值，將輸入記錄會細分到資料分割中。 然後，子句會保留特定數目的記錄，將此運算式最大化或最小化到記錄上。 接著，下一個匯總子句會以嵌套的方式套用類似的函式。 下列子句會套用至上一個子句所產生的資料分割。 此程式會針對所有匯總子句繼續進行。

例如， `top-nested` 運算子可以用來回答下列問題：「對於包含銷售資料的資料表（例如國家/地區、銷售人員和銷售量）：「銷售」的前五個國家/地區是什麼？ 這些國家/地區的前三名銷售人員有哪些？」

**語法**

*T* `|` `top-nested` *TopNestedClause2* [ `,` *TopNestedClause2*...]

其中*TopNestedClause*的語法如下：

[*N*] `of` [ *`ExprName`* `=` ] *`Expr`* [ `with` `others` `=` *`ConstExpr`* `by` *`AggName`* `=` *`Aggregation`* `asc`  |  ] [] [ `desc` ]

**引數**

針對每個*TopNestedClause*：

* *`N`*：類型的常 `long` 值，表示要為此階層層級傳回多少個前數值。
  如果省略，則會傳回所有相異值。

* *`ExprName`*：如果指定，會設定對應至值的輸出資料行名稱 *`Expr`* 。

* *`Expr`*：輸入記錄的運算式，指出要為此階層層級傳回哪個值。
  這通常是表格式輸入（*T*）的資料行參考，或是這類資料行的某些計算（例如 `bin()` ）。

* *`ConstExpr`*：如果已指定，則會將每個階層層級1筆記錄的值，加上不是「設為最上層」的所有記錄匯總。

* *`AggName`*：如果指定，此識別碼會在輸出中設定*匯總*值的資料行名稱。

* *`Aggregation`*：數值運算式，表示要套用至所有共用相同值之記錄的匯總 *`Expr`* 。 此匯總的值會決定哪一個產生的記錄是「最上層」。
  
  支援下列彙總函式：
   * [sum （）](sum-aggfunction.md)、
   * [count （）](count-aggfunction.md)、
   * [max （）](max-aggfunction.md)、
   * [min （）](min-aggfunction.md)、
   * [dcount （）](dcountif-aggfunction.md)、
   * [avg （）](avg-aggfunction.md)、
   * [百分位數（）](percentiles-aggfunction.md)和
   * [percentilew （）](percentiles-aggfunction.md)。 也支援匯總的任何代陣列合。

* `asc`或 `desc` （預設值）可能會出現，以控制選取專案實際上是來自匯總值範圍的「底部」或「頂端」。

**傳回**

這個運算子會傳回一個資料表，其中包含每個匯總子句的兩個數據行：

* 一個資料行包含子句計算的相異值 *`Expr`* （如果有指定，就會將資料行名稱*ExprName* ）

* 一個資料行包含*匯總*計算的結果（如果有指定，則資料行名稱為*AggregationName* ）。

**註解**

未指定為值的輸入 *`Expr`* 資料行不會輸出。
若要取得特定層級的所有值，請加入匯總計數，其如下所示：

* 省略*N*的值
* 使用資料行名稱做為的值*`Expr`*
* 會使用 `Ignore=max(1)` 做為匯總，然後忽略（或離開專案）資料行 `Ignore` 。

記錄的數目可能會與匯總子句的數目（（N1 + 1） \* （N2 + 1） \* ...）以指數方式成長。如果未指定*N*限制，記錄成長速度會更快。 請考慮此操作員可能會耗用大量資源。

對於匯總分佈非常非統一的情況，請限制要傳回的相異值數目（藉由使用*N*），並使用 `with others=` *ConstExpr*選項來取得所有其他情況的「權數」指示。

**範例**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| top-nested 2 of State by sum(BeginLat),
  top-nested 3 of Source by sum(BeginLat),
  top-nested 1 of EndLocation by sum(BeginLat)
```

|州|aggregated_State|來源|aggregated_Source|EndLocation|aggregated_EndLocation|
|---|---|---|---|---|---|
|KANSAS|87771.2355000001|執法機關|18744.823|FT SCOTT|264.858|
|KANSAS|87771.2355000001|公用|22855.6206|BUCKLIN|488.2457|
|KANSAS|87771.2355000001|Trained Spotter|21279.7083|SHARON SPGS|388.7404|
|德克薩斯州|123400.5101|公用|13650.9079|AMARILLO|246.2598|
|德克薩斯州|123400.5101|執法機關|37228.5966|PERRYTON|289.3178|
|德克薩斯州|123400.5101|Trained Spotter|13997.7124|CLAUDE|421.44|

使用 [與其他人] 選項：

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| top-nested 2 of State with others = "All Other States" by sum(BeginLat),
  top-nested 3 of Source by sum(BeginLat),
  top-nested 1 of EndLocation with others = "All Other End Locations" by  sum(BeginLat)


```

|州|aggregated_State|來源|aggregated_Source|EndLocation|aggregated_EndLocation|
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

|州|aggregated_State|來源|aggregated_Source|EndLocation|aggregated_EndLocation|EventType|
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

為此層級（每個群組）中的每個值提供索引排序次序，以便依最後一個嵌套層級（在此範例中為 EndLocation）排序結果：

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| top-nested 2 of State  by sum(BeginLat),    top-nested 2 of Source by sum(BeginLat),    top-nested 4 of EndLocation by  sum(BeginLat)
| order by State , Source, aggregated_EndLocation
| summarize EndLocations = make_list(EndLocation, 10000) , endLocationSums = make_list(aggregated_EndLocation, 10000) by State, Source
| extend indicies = range(0, array_length(EndLocations) - 1, 1)
| mv-expand EndLocations, endLocationSums, indicies
```

|州|來源|EndLocations|endLocationSums|indicies|
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
