---
title: diffpatterns 外掛程式-Azure 資料總管
description: 本文說明 Azure 資料總管中的 diffpatterns 外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f269bb12c4e4f73f7a7e6c4e9818d47dfc8002ef
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249480"
---
# <a name="diff-patterns-plugin"></a>差異模式外掛程式

比較相同結構的兩個資料集，並找出離散屬性 (維度) 的模式，以描述兩個資料集之間的差異。
 `Diffpatterns` 開發目的是為了協助分析失敗 (例如，藉由比較特定時間範圍內的失敗與非失敗的) ，但可能會找出相同結構的兩個資料集之間的差異。 

```kusto
T | evaluate diffpatterns(splitColumn)
```
> [!NOTE]
> `diffpatterns` 目標是找出顯著的模式 (，以捕捉集合) 之間的部分資料差異，而不是針對逐資料列的差異。

## <a name="syntax"></a>語法

`T | evaluate diffpatterns(SplitColumn, SplitValueA, SplitValueB [, WeightColumn, Threshold, MaxDimensions, CustomWildcard, ...])` 

## <a name="arguments"></a>引數 

### <a name="required-arguments"></a>必要的引數

* SplitColumn - *column_name*

    告知演算法如何將查詢分割成資料集。 根據 SplitValueA 和 SplitValueB 引數的指定值 (請參閱下文)，演算法會將查詢分割成兩個資料集 "A"和"B"，然後分析兩者之間的差異。 因此，分割資料行必須至少有兩個相異值。

* SplitValueA - *string*

    指定之 SplitColumn 中其中一個值的字串表示。 在其 SplitColumn 中具有此值的所有資料列都會被視為資料集 "A"。

* SplitValueB - *string*

    指定之 SplitColumn 中其中一個值的字串表示。 在其 SplitColumn 中具有此值的所有資料列都會被視為資料集 "B"。

    範例： `T | extend splitColumn=iff(request_responseCode == 200, "Success" , "Failure") | evaluate diffpatterns(splitColumn, "Success","Failure") `

### <a name="optional-arguments"></a>選擇性引數

所有其他引數皆為選用，但必須為下列順序。 若要指示需使用預設值，請放置字串波狀符號值 - '~' (請參閱以下範例)。

* WeightColumn - *column_name*

    根據指定的權數 (依預設每個資料都列具有權數 '1') 考慮輸入中的每個資料列。 引數必須是數值資料行的名稱 (例如，、 `int` `long` 、 `real`) 。
    權數資料行的常見用法是考慮已內嵌至各資料列的資料取樣或分組/彙總。
    
    範例： `T | extend splitColumn=iff(request_responseCode == 200, "Success" , "Failure") | evaluate diffpatterns(splitColumn, "Success","Failure", sample_Count) `

* 閾值-0.015 < *雙精度* < 1 [預設值： 0.05]

    設定兩個集合之間的最小模式 (比率) 差異。

    範例：`T | extend splitColumn = iff(request-responseCode == 200, "Success" , "Failure") | evaluate diffpatterns(splitColumn, "Success","Failure", "~", 0.04)`

* MaxDimensions-0 < *int* [default：無限制]

    設定每個結果模式的不相關維度數目上限。 藉由指定限制，您可以減少查詢執行時間。

    範例：`T | extend splitColumn = iff(request-responseCode == 200, "Success" , "Failure") | evaluate diffpatterns(splitColumn, "Success","Failure", "~", "~", 3)`

* CustomWildcard-「*每個類型的任何值*」

    在結果資料表中設定特定類型的萬用字元值，會指出目前的模式沒有這個資料行的限制。
    預設值是 null，而字串預設值為空字串。 如果預設值在資料中是可行的值，則應該使用不同的萬用字元值 (例如 `*`) 。
    請參閱下列範例。

    範例： `T | extend splitColumn = iff(request-responseCode == 200, "Success" , "Failure") | evaluate diffpatterns(splitColumn, "Success","Failure", "~", "~", "~", int(-1), double(-1), long(0), datetime(1900-1-1))`

## <a name="returns"></a>傳回

`Diffpatterns` 傳回一組較小的模式，以在兩個集合中捕捉資料的不同部分 (也就是，在第一個資料集中捕獲大量百分比的資料列，以及第二個集合) 中資料列的低百分比。 每個模式會以結果中的一個資料列表示。

的結果會傳回 `diffpatterns` 下列資料行：

* SegmentId：指派給目前查詢中模式的身分識別 (注意：重複查詢) 不保證識別碼相同。

* CountA： Set A (Set A 中的模式所捕捉的資料列數目，相當於 `where tostring(splitColumn) == SplitValueA`) 。

* CountB： Set B (Set B 中的模式所捕捉的資料列數目相當於 `where tostring(splitColumn) == SplitValueB`) 。

* PercentA： Set A 中由模式所捕捉的資料列百分比 (100.0 * CountA/count (SetA) # A3。

* PercentB： 100.0 * CountB/count (SetB) # A3 (模式所捕獲之 Set B 中的資料列百分比。

* PercentDiffAB： A 和 B (之間的絕對百分比點差異 |PercentA-PercentB |) 是描述兩個集合之間差異之模式重要性的主要量值。

* 其餘的資料行：是輸入的原始架構並描述模式，每個資料列 (模式) reresents 資料) 行之非萬用字元值的交集 (對等的資料列 `where col1==val1 and col2==val2 and ... colN=valN` 中的每個非萬用字元值的交集。

針對每個模式，不會在模式中設定的資料行 (也就是不限制特定值的) 將包含萬用字元值，預設值為 null。 請參閱下面的引數區段，以手動方式變更萬用字元。

* 注意：通常不會有不同的模式。 它們可能會重迭，而且通常不會涵蓋所有原始資料列。 某些資料列可能不會落在任何模式之下。

> [!TIP]
> * 在輸入管道中使用 [where](./whereoperator.md) 和 [project](./projectoperator.md) ，將資料縮減為您感興趣的內容。
> * 當您找到有趣的資料列時，您可藉由將其特定值加入至您的 `where` 篩選器，進一步深入探索。

## <a name="example"></a>範例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where monthofyear(StartTime) == 5
| extend Damage = iff(DamageCrops + DamageProperty > 0 , 1 , 0)
| project State , EventType , Source , Damage, DamageCrops
| evaluate diffpatterns(Damage, "0", "1" )
```

|SegmentId|CountA|CountB|PercentA|PercentB|PercentDiffAB|狀態|EventType|來源|DamageCrops|
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
