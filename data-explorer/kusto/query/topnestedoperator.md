---
title: 最上層嵌套運算子-Azure 資料總管
description: 本文說明 Azure 資料總管中的最上層嵌套運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ce56040e2135a455e29a8ff0ce83d832cbf5c5f7
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243720"
---
# <a name="top-nested-operator"></a>top-nested 運算子

產生階層式匯總和頂端值選取，其中每個層級都是前一個層級的精簡專案。

```kusto
T | top-nested 3 of Location with others="Others" by sum(MachinesNumber), top-nested 4 of bin(Timestamp,5m) by sum(MachinesNumber)
```

`top-nested`運算子接受表格式資料作為輸入，以及一或多個匯總子句。
第一個匯總子句 (最左邊) 將輸入記錄細分到資料分割，根據這些記錄的某個運算式的唯一值。 然後子句會保留特定數目的記錄，以最大化或最小化記錄上的此運算式。 接著，下一個匯總子句會以嵌套的方式套用類似的函式。 下列子句會套用至前一個子句所產生的資料分割。 所有匯總子句都會繼續執行此程式。

例如， `top-nested` 操作員可以用來回答下列問題：「對於包含銷售資料的資料表，例如國家/地區、銷售人員和銷售量：銷售的前五個國家/地區為何？ 這些國家/地區中的前三名銷售人員有哪些？」

## <a name="syntax"></a>語法

*T* `|` `top-nested` *TopNestedClause2* [ `,` *TopNestedClause2*...]

其中 *TopNestedClause* 具有下列語法：

[*N*] `of` [ *`ExprName`* `=` ] *`Expr`* [ `with` `others` `=` *`ConstExpr`* ] `by` [ *`AggName`* `=` *`Aggregation`* `asc`  |  `desc` ] []

## <a name="arguments"></a>引數

針對每個 *TopNestedClause*：

* *`N`*：類型的常 `long` 值，表示要針對此階層層級傳回的前幾個值。
  如果省略，則會傳回所有相異值。

* *`ExprName`*：如果有指定，會設定對應于值之輸出資料行的名稱 *`Expr`* 。

* *`Expr`*：輸入記錄上的運算式，指出要針對此階層層級傳回的值。
  一般而言，它是表格式輸入 (*T*) 的資料行參考，或某些計算 (例如 `bin()`) 這類資料行。

* *`ConstExpr`*：如果指定了，針對每個階層層級，將會加入1筆記錄，並將其值設為所有未「變成最上層」記錄的匯總值。

* *`AggName`*：如果有指定，此識別碼會在輸出中設定 *匯總*值的資料行名稱。

* *`Aggregation`*：數值運算式，表示要套用至所有共用相同值之記錄的匯總 *`Expr`* 。 此匯總的值會決定哪一個產生的記錄是 "top"。
  
  以下是支援的彙總函式：
   * [sum ( # B1 ](sum-aggfunction.md)，
   * [計數 ( # B1 ](count-aggfunction.md)，
   * [最大 ( # B1 ](max-aggfunction.md)，
   * [最小 ( # B1 ](min-aggfunction.md)，
   * [dcount ( # B1 ](dcountif-aggfunction.md)，
   * [avg ( # B1 ](avg-aggfunction.md)，
   * [百分位數 ( # B1 ](percentiles-aggfunction.md)和
   * [percentilew ( # B1 ](percentiles-aggfunction.md)。 也支援匯總的任何代陣列合。

* `asc` 或者 `desc` (預設) 可能會顯示為控制選取專案是從匯總值範圍的「底部」或「頂端」。

## <a name="returns"></a>傳回

這個運算子會傳回資料表，其中每個匯總子句都有兩個數據行：

* 其中一個資料行保留子句計算的相異值 *`Expr`* (如果有指定，資料行名稱會 *ExprName* ）) 

* 其中一個資料行保存 *匯總* 計算的結果 (如果指定了資料行名稱，則為 *AggregationName* ）) 

## <a name="notes"></a>注意

未指定為值的輸入 *`Expr`* 資料行不會輸出。
若要取得特定層級的所有值，請新增匯總計數：

* 省略*N*的值
* 使用資料行名稱做為的值 *`Expr`*
* 會使用 `Ignore=max(1)` 做為匯總，然後忽略資料行)  (或專案離開 `Ignore` 。

記錄數目可能會以匯總子句的數目以指數方式成長 ( # B1 N1 + 1) \* (N2 + 1) \* ... ) 。如果未指定 *N* 限制，記錄成長速度會更快。 請考慮此操作員可能會耗用大量的資源。

如果匯總的分佈相當非統一，請使用 *N*) 來限制要傳回 (的相異值數目，並使用 `with others=` *ConstExpr* 選項來取得所有其他案例的「權數」的指示。

## <a name="examples"></a>範例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| top-nested 2 of State by sum(BeginLat),
  top-nested 3 of Source by sum(BeginLat),
  top-nested 1 of EndLocation by sum(BeginLat)
```

|狀態|aggregated_State|來源|aggregated_Source|EndLocation|aggregated_EndLocation|
|---|---|---|---|---|---|
|堪薩斯|87771.2355000001|執法機關|18744.823|FT SCOTT|264.858|
|堪薩斯|87771.2355000001|Public|22855.6206|BUCKLIN|488.2457|
|堪薩斯|87771.2355000001|Trained Spotter|21279.7083|SHARON SPGS|388.7404|
|德克薩斯州|123400.5101|Public|13650.9079|阿馬里洛|246.2598|
|德克薩斯州|123400.5101|執法機關|37228.5966|PERRYTON|289.3178|
|德克薩斯州|123400.5101|Trained Spotter|13997.7124|克勞德|421.44|

使用 [與其他人] 選項：

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| top-nested 2 of State with others = "All Other States" by sum(BeginLat),
  top-nested 3 of Source by sum(BeginLat),
  top-nested 1 of EndLocation with others = "All Other End Locations" by  sum(BeginLat)


```

|狀態|aggregated_State|來源|aggregated_Source|EndLocation|aggregated_EndLocation|
|---|---|---|---|---|---|
|堪薩斯|87771.2355000001|執法機關|18744.823|FT SCOTT|264.858|
|堪薩斯|87771.2355000001|Public|22855.6206|BUCKLIN|488.2457|
|堪薩斯|87771.2355000001|Trained Spotter|21279.7083|SHARON SPGS|388.7404|
|德克薩斯州|123400.5101|Public|13650.9079|阿馬里洛|246.2598|
|德克薩斯州|123400.5101|執法機關|37228.5966|PERRYTON|289.3178|
|德克薩斯州|123400.5101|Trained Spotter|13997.7124|克勞德|421.44|
|堪薩斯|87771.2355000001|執法機關|18744.823|所有其他結束位置|18479.965|
|堪薩斯|87771.2355000001|Public|22855.6206|所有其他結束位置|22367.3749|
|堪薩斯|87771.2355000001|Trained Spotter|21279.7083|所有其他結束位置|20890.9679|
|德克薩斯州|123400.5101|Public|13650.9079|所有其他結束位置|13404.6481|
|德克薩斯州|123400.5101|執法機關|37228.5966|所有其他結束位置|36939.2788|
|德克薩斯州|123400.5101|Trained Spotter|13997.7124|所有其他結束位置|13576.2724|
|堪薩斯|87771.2355000001|||所有其他結束位置|24891.0836|
|德克薩斯州|123400.5101|||所有其他結束位置|58523.2932000001|
|所有其他狀態|1149279.5923|||所有其他結束位置|1149279.5923|

下列查詢會針對上述範例中所使用的第一個層級顯示相同的結果。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
 StormEvents
 | where State !in ('TEXAS', 'KANSAS')
 | summarize sum(BeginLat)
```

|sum_BeginLat|
|---|
|1149279.5923|


向最上層的結果要求 (結果) 的另一個資料行。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| top-nested 2 of State by sum(BeginLat),    top-nested 2 of Source by sum(BeginLat),    top-nested 1 of EndLocation by sum(BeginLat), top-nested of EventType  by tmp = max(1)
| project-away tmp
```

|狀態|aggregated_State|來源|aggregated_Source|EndLocation|aggregated_EndLocation|EventType|
|---|---|---|---|---|---|---|
|堪薩斯|87771.2355000001|Trained Spotter|21279.7083|SHARON SPGS|388.7404|Thunderstorm Wind|
|堪薩斯|87771.2355000001|Trained Spotter|21279.7083|SHARON SPGS|388.7404|Hail|
|堪薩斯|87771.2355000001|Trained Spotter|21279.7083|SHARON SPGS|388.7404|龍捲風|
|堪薩斯|87771.2355000001|Public|22855.6206|BUCKLIN|488.2457|Hail|
|堪薩斯|87771.2355000001|Public|22855.6206|BUCKLIN|488.2457|Thunderstorm Wind|
|堪薩斯|87771.2355000001|Public|22855.6206|BUCKLIN|488.2457|Flood|
|德克薩斯州|123400.5101|Trained Spotter|13997.7124|克勞德|421.44|Hail|
|德克薩斯州|123400.5101|執法機關|37228.5966|PERRYTON|289.3178|Hail|
|德克薩斯州|123400.5101|執法機關|37228.5966|PERRYTON|289.3178|Flood|
|德克薩斯州|123400.5101|執法機關|37228.5966|PERRYTON|289.3178|Flash Flood|

針對每個群組 (此層級中的每個值，提供索引排序次序) 若要依照此範例中的最後一個嵌套層級 (來排序結果，請 EndLocation) ：

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| top-nested 2 of State  by sum(BeginLat),    top-nested 2 of Source by sum(BeginLat),    top-nested 4 of EndLocation by  sum(BeginLat)
| order by State , Source, aggregated_EndLocation
| summarize EndLocations = make_list(EndLocation, 10000) , endLocationSums = make_list(aggregated_EndLocation, 10000) by State, Source
| extend indicies = range(0, array_length(EndLocations) - 1, 1)
| mv-expand EndLocations, endLocationSums, indicies
```

|狀態|來源|EndLocations|endLocationSums|指標|
|---|---|---|---|---|
|德克薩斯州|Trained Spotter|克勞德|421.44|0|
|德克薩斯州|Trained Spotter|阿馬里洛|316.8892|1|
|德克薩斯州|Trained Spotter|DALHART|252.6186|2|
|德克薩斯州|Trained Spotter|PERRYTON|216.7826|3|
|德克薩斯州|執法機關|PERRYTON|289.3178|0|
|德克薩斯州|執法機關|LEAKEY|267.9825|1|
|德克薩斯州|執法機關|BRACKETTVILLE|264.3483|2|
|德克薩斯州|執法機關|GILMER|261.9068|3|
|堪薩斯|Trained Spotter|SHARON SPGS|388.7404|0|
|堪薩斯|Trained Spotter|ATWOOD|358.6136|1|
|堪薩斯|Trained Spotter|LENORA|317.0718|2|
|堪薩斯|Trained Spotter|SCOTT 城市|307.84|3|
|堪薩斯|Public|BUCKLIN|488.2457|0|
|堪薩斯|Public|亞什蘭|446.4218|1|
|堪薩斯|Public|保護|446.11|2|
|堪薩斯|Public|MEADE 狀態公園|371.1|3|
