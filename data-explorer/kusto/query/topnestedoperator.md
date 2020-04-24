---
title: 頂頂層運算子 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的頂級嵌套運算符。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 296c36f4653bec971c71dc210008af7b0d98959a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505928"
---
# <a name="top-nested-operator"></a>top-nested 運算子

產生階層式的首要結果，其中可根據上一層的值向下切入每一層。 

```kusto
T | top-nested 3 of Location with others="Others" by sum(MachinesNumber), top-nested 4 of bin(Timestamp,5m) by sum(MachinesNumber)
```

它對於儀錶板可視化方案很有用,或者當需要回答一個聽起來像「查找 K1 的 N 前 N 值」(使用一些聚合)時非常有用。對於每個值,查找 K2 的 M 前 M 值(使用另一個聚合);..."

**語法**

*T* `|` T `desc` `,` `asc`  |  `top-nested` = `=` N1 = 運算式1 = ConstExpr1 = Aggname1 = 聚合1 = = = [ ] ... `by` `with others=` `of` *Expression1* *Aggregation1* *N1* *AggName1* * *

**引數**

對每個頂層嵌 sock:
* *N1*: 每個層次結構級別要返回的最高值數。 可選(如果省略,將返回所有不同的值)。
* *運算式1*:用於選擇最高值的運算式。 通常,它是*T*中的列名稱,或者在這樣的列上的某個分箱操作(例如,)。 `bin()` 
* *ConstExpr1*:如果指定,則對於適用的嵌套級別,將附加一個行,用於保存頂部值中未包括的其他值的聚合結果。
* *聚合1:* 對聚合函數的調用,可以是[:sum()](sum-aggfunction.md) [、count()](count-aggfunction.md)、[最大值](max-aggfunction.md)[()、最小值](min-aggfunction.md)[()、dcount()、](dcountif-aggfunction.md)[平均()、](avg-aggfunction.md)[百分位數()](percentiles-aggfunction.md)[或這些](percentiles-aggfunction.md)聚合的任何等值群組。
* `asc` 或 `desc` (預設值) 可能會出現，以控制實際上是從範圍的「下限」或「上限」進行選取。

**傳回**

層次結構表,包括輸入列,並為每個列生成一個新列,以包括每個元素相同級別的聚合結果。
列按輸入列的相同順序排列,新生成的列將接近聚合列。 每個記錄都有一個層次結構,在在所有以前的級別上應用所有以前的頂級規則,然後在此輸出上應用當前級別的規則后,將選擇每個值。
這意味著等級 i 的前 n 值是為級別 i - 1 中的每個值計算的。
 
**技巧**

* 使用中重新命名的列以*進行聚合*結果:T |依機器編號位置 = 總和(機器編號) ...

* 返回的記錄數可能很大;高達 (*N1* \* +1) ( \* *N2*+1) ...\* *(Nm*+1)(其中 m 是級別的數量 *,Ni*是級別 i 的最高計數)。

* 聚合必須接收具有聚合函數的數位列,這是上述函數之一。

* 使用`with others=`選項,以獲得某些級別中未達到 N 前值的所有其他值的聚合值。

* 如果您對獲取`with others=`某個級別不感興趣,將附加 null 值(對於 aggreagate 列和級別鍵,請參閱下面的示例)。


* 可以通過附加其他諸如此類的頂級嵌套語句來為選定的頂級嵌套候選項返回其他列(請參閱下面的示例):

```kusto
top-nested 2 of ...., ..., ..., top-nested of <additionalRequiredColumn1> by max(1), top-nested of <additionalRequiredColumn2> by max(1)
```

**範例**

```kusto
StormEvents
| top-nested 2 of State by sum(BeginLat),
  top-nested 3 of Source by sum(BeginLat),
  top-nested 1 of EndLocation by sum(BeginLat)
```

|State|aggregated_State|來源|aggregated_Source|結束位置|aggregated_EndLocation|
|---|---|---|---|---|---|
|堪薩斯|87771.2355000001|執法機關|18744.823|英國《金融時報》|264.858|
|堪薩斯|87771.2355000001|公開|22855.6206|巴克林|488.2457|
|堪薩斯|87771.2355000001|Trained Spotter|21279.7083|夏隆 SPGS|388.7404|
|德克薩斯州|123400.5101|公開|13650.9079|阿馬里洛|246.2598|
|德克薩斯州|123400.5101|執法機關|37228.5966|佩里頓|289.3178|
|德克薩斯州|123400.5101|Trained Spotter|13997.7124|克勞德|421.44|


* 與其他範例一起:

```kusto
StormEvents
| top-nested 2 of State with others = "All Other States" by sum(BeginLat),
  top-nested 3 of Source by sum(BeginLat),
  top-nested 1 of EndLocation with others = "All Other End Locations" by  sum(BeginLat)


```

|State|aggregated_State|來源|aggregated_Source|結束位置|aggregated_EndLocation|
|---|---|---|---|---|---|
|堪薩斯|87771.2355000001|執法機關|18744.823|英國《金融時報》|264.858|
|堪薩斯|87771.2355000001|公開|22855.6206|巴克林|488.2457|
|堪薩斯|87771.2355000001|Trained Spotter|21279.7083|夏隆 SPGS|388.7404|
|德克薩斯州|123400.5101|公開|13650.9079|阿馬里洛|246.2598|
|德克薩斯州|123400.5101|執法機關|37228.5966|佩里頓|289.3178|
|德克薩斯州|123400.5101|Trained Spotter|13997.7124|克勞德|421.44|
|堪薩斯|87771.2355000001|執法機關|18744.823|所有其他結束位置|18479.965|
|堪薩斯|87771.2355000001|公開|22855.6206|所有其他結束位置|22367.3749|
|堪薩斯|87771.2355000001|Trained Spotter|21279.7083|所有其他結束位置|20890.9679|
|德克薩斯州|123400.5101|公開|13650.9079|所有其他結束位置|13404.6481|
|德克薩斯州|123400.5101|執法機關|37228.5966|所有其他結束位置|36939.2788|
|德克薩斯州|123400.5101|Trained Spotter|13997.7124|所有其他結束位置|13576.2724|
|堪薩斯|87771.2355000001|||所有其他結束位置|24891.0836|
|德克薩斯州|123400.5101|||所有其他結束位置|58523.2932000001|
|所有其他國家|1149279.5923|||所有其他結束位置|1149279.5923|


以下查詢顯示與上述範例中使用的第一個等級相同的結果:

```kusto
 StormEvents
 | where State !in ('TEXAS', 'KANSAS')
 | summarize sum(BeginLat)
```

|sum_BeginLat|
|---|
|1149279.5923|


向上嵌入結果要求另一列(事件類型): 

```kusto
StormEvents
| top-nested 2 of State by sum(BeginLat),    top-nested 2 of Source by sum(BeginLat),    top-nested 1 of EndLocation by sum(BeginLat), top-nested of EventType  by tmp = max(1)
| project-away tmp
```

|State|aggregated_State|來源|aggregated_Source|結束位置|aggregated_EndLocation|EventType|
|---|---|---|---|---|---|---|
|堪薩斯|87771.2355000001|Trained Spotter|21279.7083|夏隆 SPGS|388.7404|Thunderstorm Wind|
|堪薩斯|87771.2355000001|Trained Spotter|21279.7083|夏隆 SPGS|388.7404|Hail|
|堪薩斯|87771.2355000001|Trained Spotter|21279.7083|夏隆 SPGS|388.7404|龍捲風|
|堪薩斯|87771.2355000001|公開|22855.6206|巴克林|488.2457|Hail|
|堪薩斯|87771.2355000001|公開|22855.6206|巴克林|488.2457|Thunderstorm Wind|
|堪薩斯|87771.2355000001|公開|22855.6206|巴克林|488.2457|Flood|
|德克薩斯州|123400.5101|Trained Spotter|13997.7124|克勞德|421.44|Hail|
|德克薩斯州|123400.5101|執法機關|37228.5966|佩里頓|289.3178|Hail|
|德克薩斯州|123400.5101|執法機關|37228.5966|佩里頓|289.3178|Flood|
|德克薩斯州|123400.5101|執法機關|37228.5966|佩里頓|289.3178|Flash Flood|

為了按最後一個嵌套等級對結果進行排序(在此範例中按 EndLocation),並為此等級(每個組)中的每個值給出索引排序順序:

```kusto
StormEvents
| top-nested 2 of State  by sum(BeginLat),    top-nested 2 of Source by sum(BeginLat),    top-nested 4 of EndLocation by  sum(BeginLat)
| order by State , Source, aggregated_EndLocation
| summarize EndLocations = make_list(EndLocation, 10000) , endLocationSums = make_list(aggregated_EndLocation, 10000) by State, Source
| extend indicies = range(0, array_length(EndLocations) - 1, 1)
| mv-expand EndLocations, endLocationSums, indicies
```

|State|來源|結束位置|結束位置與|稻子|
|---|---|---|---|---|
|德克薩斯州|Trained Spotter|克勞德|421.44|0|
|德克薩斯州|Trained Spotter|阿馬里洛|316.8892|1|
|德克薩斯州|Trained Spotter|達爾哈特|252.6186|2|
|德克薩斯州|Trained Spotter|佩里頓|216.7826|3|
|德克薩斯州|執法機關|佩里頓|289.3178|0|
|德克薩斯州|執法機關|萊基|267.9825|1|
|德克薩斯州|執法機關|布拉克特維爾|264.3483|2|
|德克薩斯州|執法機關|吉爾默|261.9068|3|
|堪薩斯|Trained Spotter|夏隆 SPGS|388.7404|0|
|堪薩斯|Trained Spotter|ATWOOD|358.6136|1|
|堪薩斯|Trained Spotter|萊納拉|317.0718|2|
|堪薩斯|Trained Spotter|斯科特市|307.84|3|
|堪薩斯|公開|巴克林|488.2457|0|
|堪薩斯|公開|亞什蘭|446.4218|1|
|堪薩斯|公開|保護|446.11|2|
|堪薩斯|公開|米德·斯特邦派克|371.1|3|