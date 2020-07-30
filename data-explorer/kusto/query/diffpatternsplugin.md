---
title: diffpatterns 外掛程式-Azure 資料總管
description: 本文說明 Azure 資料總管中的 diffpatterns 外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a02f275dc47e88c7b14b85d19040e907613d1b80
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348321"
---
# <a name="diff-patterns-plugin"></a>diff 模式外掛程式

比較相同結構的兩個資料集，並尋找離散屬性（維度）的模式，以描述兩個資料集之間的差異。
 `Diffpatterns`的開發目的是要協助分析失敗（例如，藉由比較給定時間範圍內的失敗與非失敗），但可能會發現相同結構的任何兩個資料集之間的差異。 

```kusto
T | evaluate diffpatterns(splitColumn)
```


## <a name="syntax"></a>語法

`T | evaluate diffpatterns(SplitColumn, SplitValueA, SplitValueB [, WeightColumn, Threshold, MaxDimensions, CustomWildcard, ...])` 

**必要的引數**

* SplitColumn - *column_name*

    告知演算法如何將查詢分割成資料集。 根據 SplitValueA 和 SplitValueB 引數的指定值 (請參閱下文)，演算法會將查詢分割成兩個資料集 "A"和"B"，然後分析兩者之間的差異。 因此，分割資料行必須至少有兩個相異值。

* SplitValueA - *string*

    指定之 SplitColumn 中其中一個值的字串表示。 在其 SplitColumn 中具有此值的所有資料列都會被視為資料集 "A"。

* SplitValueB - *string*

    指定之 SplitColumn 中其中一個值的字串表示。 在其 SplitColumn 中具有此值的所有資料列都會被視為資料集 "B"。

    範例： `T | extend splitColumn=iff(request_responseCode == 200, "Success" , "Failure") | evaluate diffpatterns(splitColumn, "Success","Failure") `

**選擇性引數**

所有其他引數皆為選用，但必須為下列順序。 若要指示需使用預設值，請放置字串波狀符號值 - '~' (請參閱以下範例)。

* WeightColumn - *column_name*

    根據指定的權數 (依預設每個資料都列具有權數 '1') 考慮輸入中的每個資料列。 引數必須是數值資料行的名稱（例如、、 `int` `long` `real` ）。
    權數資料行的常見用法是考慮已內嵌至各資料列的資料取樣或分組/彙總。
    
    範例： `T | extend splitColumn=iff(request_responseCode == 200, "Success" , "Failure") | evaluate diffpatterns(splitColumn, "Success","Failure", sample_Count) `

* 閾值-0.015 < *double* < 1 [預設值： 0.05]

    設定兩個集合之間的最小模式 (比率) 差異。

    範例：`T | extend splitColumn = iff(request-responseCode == 200, "Success" , "Failure") | evaluate diffpatterns(splitColumn, "Success","Failure", "~", 0.04)`

* MaxDimensions-0 < *int* [預設值：無限制]

    設定每個結果模式的不相關維度數目上限。 藉由指定限制，您可以減少查詢執行時間。

    範例：`T | extend splitColumn = iff(request-responseCode == 200, "Success" , "Failure") | evaluate diffpatterns(splitColumn, "Success","Failure", "~", "~", 3)`

* CustomWildcard-「*每一類型的任何值*」

    在結果資料表中設定特定類型的萬用字元值，會指出目前的模式沒有這個資料行的限制。
    預設值是 null，而字串預設值為空字串。 如果預設值是資料中的可行值，則應該使用不同的萬用字元值（例如 `*` ）。
    請參閱下列範例。

    範例： `T | extend splitColumn = iff(request-responseCode == 200, "Success" , "Failure") | evaluate diffpatterns(splitColumn, "Success","Failure", "~", "~", "~", int(-1), double(-1), long(0), datetime(1900-1-1))`

## <a name="returns"></a>傳回

`Diffpatterns`傳回一小組模式，用來在兩個集合中捕捉資料的不同部分（也就是，在第一個資料集中捕捉大量百分比的資料列，而在第二個集合中，資料列的百分比偏低的模式）。 每個模式會以結果中的一個資料列表示。

的結果會傳回 `diffpatterns` 下列資料行：

* SegmentId：指派給目前查詢中模式的身分識別（注意：識別碼在重複查詢中不保證會是相同的）。

* CountA： Set A 中模式所捕獲的資料列數目（Set A 相當於 `where tostring(splitColumn) == SplitValueA` ）。

* CountB： Set B 中模式所捕獲的資料列數目（Set B 相當於 `where tostring(splitColumn) == SplitValueB` ）。

* PercentA：由模式（100.0 * CountA/count （SetA））所捕捉之集合中的資料列百分比。

* PercentB：由模式（100.0 * CountB/count （SetB））所捕捉之集合 B 中的資料列百分比。

* PercentDiffAB： A 和 B 之間的絕對百分比點差異（|PercentA-PercentB |）這是描述兩個集合之間差異的模式主要量值。

* 其餘的資料行：是輸入的原始架構，而且會描述模式，每個資料列（模式） reresents 資料行的非萬用字元值的交集（相當於針對資料列 `where col1==val1 and col2==val2 and ... colN=valN` 中的每個非萬用字元值）。

針對每個模式，模式中未設定的資料行（也就是沒有特定值的限制）將包含萬用字元值，預設為 null。 請參閱下面的引數一節：如何以手動方式變更萬用字元。

* 注意：模式通常不是相異的。 它們可能會重迭，而且通常不會涵蓋所有原始資料列。 某些資料列可能不會落在任何模式之下。


**提示**

在輸入管道中使用 where 和[project](./projectoperator.md) ，將資料減少到您感興趣的[位置](./whereoperator.md)。

當您找到有趣的資料列時，您可藉由將其特定值加入至您的 `where` 篩選器，進一步深入探索。

* 注意： `diffpatterns` 目標是要尋找重要的模式（這會捕捉集合之間資料差異的部分），而不是用來進行逐列的差異。

## <a name="example"></a>範例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where monthofyear(StartTime) == 5
| extend Damage = iff(DamageCrops + DamageProperty > 0 , 1 , 0)
| project State , EventType , Source , Damage, DamageCrops
| evaluate diffpatterns(Damage, "0", "1" )
```

|SegmentId|CountA|CountB|PercentA|PercentB|PercentDiffAB|State|EventType|來源|DamageCrops|
|---|---|---|---|---|---|---|---|---|---|
|0|2278|93|49.8|7.1|42.7||Hail||0|
|1|779|512|17.03|39.08|22.05||Thunderstorm Wind|||
|2|1098|118|24.01|9.01|15|||Trained Spotter|0|
|3|136|158|2.97|12.06|9.09|||Newspaper||
|4|359|214|7.85|16.34|8.49||Flash Flood|||
|5|50|122|1.09|9.31|8.22|愛荷華州||||
|6|655|279|14.32|21.3|6.98|||執法機關||
|7|150|117|3.28|8.93|5.65||Flood|||
|8|362|176|7.91|13.44|5.52|||Emergency Manager||
