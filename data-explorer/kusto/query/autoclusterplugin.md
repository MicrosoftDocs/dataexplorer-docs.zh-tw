---
title: autocluster 外掛程式-Azure 資料總管
description: 本文說明 Azure 資料總管中的 autocluster 外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0b2d76c0dd7a3ee0724db67aa8cb0cd9229748fa
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225525"
---
# <a name="autocluster-plugin"></a>autocluster 外掛程式

```kusto
T | evaluate autocluster()
```

AutoCluster 可尋找資料中離散屬性 (維度) 的常見模式，並將原始查詢的結果 (不論是 100 或 100000 個資料列) 減少至少量模式。 AutoCluster 特別開發來協助分析失敗 (如例外狀況、毀損)，但可能作用於任何已篩選的資料集上。 

**語法**

`T | evaluate autocluster(`*引數*`)`

**傳回**

AutoCluster 會傳回一組 (通常很少) 模式，以擷取有橫跨多個離散屬性之共用常見值的部分資料。 每個模式會以結果中的一個資料列表示。 

第一個資料行是區段識別碼。接下來的兩個數據行是由模式所捕捉之原始查詢中的資料列計數和百分比。 其餘的資料行來自原始查詢，其值是資料行中的特定值或表示變數值的萬用字元值 (預設為 null)。 

請注意，模式並未不同︰它們可能會重疊，而且通常不會涵蓋所有原始資料列。 某些資料列可能不會落在任何模式之下。

> [!TIP]
> 在輸入管道中使用 where 和[project](./projectoperator.md) ，將資料減少到您感興趣的[位置](./whereoperator.md)。
>
> 當您找到有趣的資料列時，您可藉由將其特定值加入至您的 `where` 篩選器，進一步深入探索。

**引數 (全部選用)**

`T | evaluate autocluster(`[*SizeWeight*， *WeightColumn*， *NumSeeds*， *CustomWildcard*， *CustomWildcard*，...]`)`

所有引數皆為選用，但必須為上述順序。 若要指示需使用預設值，請放置字串波狀符號值 - '~' (請參閱以下範例)。

可用的引數：

* SizeWeight-0 < *double* < 1 [預設值： 0.5]

    可讓您控制泛型 (高涵蓋範圍) 與資訊豐富 (許多共用值) 之間的平衡。 增加此值通常可減少模式數目，而每個模式傾向於涵蓋較大的百分比。 減少此值通常會產生具有更多共用值和較小涵蓋百分比的更特定模式。 幕後公式下的是標準化的一般分數和資訊分數之間的加權幾何平均值， `SizeWeight` 以及和 `1-SizeWeight` 作為權數。 

    範例：`T | evaluate autocluster(0.8)`

* WeightColumn - *column_name*

    根據指定的權數 (依預設每個資料都列具有權數 '1') 考慮輸入中的每個資料列。 引數必須是數值資料行名稱 (例如 int、long、real)。 權數資料行的常見用法是考慮已內嵌至各資料列的資料取樣或分組/彙總。
    
    範例：`T | evaluate autocluster('~', sample_Count)` 

* NumSeeds- *int* [預設值： 25] 

    種子數目可決定演算法的初始本機搜尋點數。 在某些情況下，視資料的結構而定，增加種子數目會透過以查詢速度變慢所換取的搜尋空間增加，來增加結果的數目 (或品質)。 此值會以兩個方向減少結果，因此將其降到5以下將可達成不明顯的效能改進，而增加的50不常產生其他模式。

    範例：`T | evaluate autocluster('~', '~', 15)`

* CustomWildcard- *"any_value_per_type"*

    在結果資料表中設定特定類型的萬用字元值，會指出目前的模式沒有這個資料行的限制。
    預設值是 null，而字串預設值為空字串。 如果預設值是資料中的可行值，則應該使用不同的萬用字元值（例如 `*` ）。

    範例：`T | evaluate autocluster('~', '~', '~', '*', int(-1), double(-1), long(0), datetime(1900-1-1))`

**範例**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where monthofyear(StartTime) == 5
| extend Damage = iff(DamageCrops + DamageProperty > 0 , "YES" , "NO")
| project State , EventType , Damage
| evaluate autocluster(0.6)
```

|SegmentId|計數|百分比|State|EventType|Damage|
|---|---|---|---|---|---|---|---|---|
|0|2278|38.7||Hail|否
|1|512|8.7||Thunderstorm Wind|YES
|2|898|15.3|德克薩斯州||

**包含自訂萬用字元的範例**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where monthofyear(StartTime) == 5
| extend Damage = iff(DamageCrops + DamageProperty > 0 , "YES" , "NO")
| project State , EventType , Damage 
| evaluate autocluster(0.2, '~', '~', '*')
```

|SegmentId|計數|百分比|State|EventType|Damage|
|---|---|---|---|---|---|---|---|---|
|0|2278|38.7|\*|Hail|否
|1|512|8.7|\*|Thunderstorm Wind|YES
|2|898|15.3|德克薩斯州|\*|\*

**另請參閱**

* [basket](./basketplugin.md)
* [減少](./reduceoperator.md)

**其他資訊**

* AutoCluster 主要是以下列文件中的 Seed-Expand 演算法為基礎：[使用離散屬性的遙測資料採礦演算法](https://www.scitepress.org/DigitalLibrary/PublicationsDetail.aspx?ID=d5kcrO+cpEU=&t=1)。 

