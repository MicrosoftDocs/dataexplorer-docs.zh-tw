---
title: 購物籃外掛程式-Azure 資料總管
description: 本文說明 Azure 資料總管中的購物籃外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 05/26/2019
ms.openlocfilehash: 9436254d72f364edc1f6a758ce2325e272367293
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252683"
---
# <a name="basket-plugin"></a>basket 外掛程式

```kusto
T | evaluate basket()
```

購物籃會找出資料中)  (維度的所有常用屬性模式。 然後，它會傳回在原始查詢中傳遞頻率臨界值的常見模式。 購物籃保證會在資料中找出每個頻繁的模式，但不保證會有多項式執行時間。 查詢的執行時間在資料列數中是線性的，但它可能是資料行數 (維度) 的指數。 Basket 是以最初針對購物籃分析資料採礦而開發的 Apriori 演算法為基礎。

## <a name="syntax"></a>語法

`T | evaluate basket(`*引數*`)`

## <a name="returns"></a>傳回

購物籃會傳回在資料列的比率閾值上方出現的所有常用模式。 預設臨界值是0.05。 每個模式會以結果中的一個資料列表示。

第一個資料行是區段識別碼。 接下來的兩個數據行是由模式所捕獲的資料列 *計數* 和 *百分比*（從原始查詢）。 其餘的資料行是來自原始查詢。
其值是資料行中的特定值或萬用字元值，預設值為 null，表示變數值。

**引數 (全部選用)**

`T | evaluate basket(`[*臨界值*， *WeightColumn*， *MaxDimensions*， *CustomWildcard*， *CustomWildcard*，...]`)`

所有引數皆為選用，但必須為上述順序。 若要指出應使用預設值，請使用字串波狀值-' ~ '。 請參閱下列範例。

可用的引數：

* 閾值-0.015 < *雙精度* < 1 [預設值： 0.05]

    設定要頻繁考慮之資料列的最少比例。 不會傳回比率較小的模式。
    
    範例： `T | evaluate basket(0.02)`

* WeightColumn - *column_name*

    會根據指定的權數考慮輸入中的每個資料列。 依預設，每個資料列的權數為 ' 1 '。 引數必須是數值資料行的名稱，例如 int、long、real。 權數資料行的常見用法是考慮已內嵌至各資料列的資料取樣或值區/匯總。

    範例： `T | evaluate basket('~', sample_Count)`

* MaxDimensions-1 < *int* [default： 5]

    設定每個購物籃的最大不相關維度數目（預設為受限制），以將查詢執行時間降至最低。

    範例： `T | evaluate basket('~', '~', 3)`

* CustomWildcard- *"any_value_per_type"*

    在結果資料表中設定特定類型的萬用字元值，會指出目前的模式沒有這個資料行的限制。
    預設為 Null。 字串的預設值是空字串。 如果預設值在資料中是很好的值，則應該使用不同的萬用字元值，例如 `*` 。

    例如：

     `T | evaluate basket('~', '~', '~', '*', int(-1), double(-1), long(0), datetime(1900-1-1))`

## <a name="example"></a>範例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where monthofyear(StartTime) == 5
| extend Damage = iff(DamageCrops + DamageProperty > 0 , "YES" , "NO")
| project State, EventType, Damage, DamageCrops
| evaluate basket(0.2)
```

|SegmentId|計數|百分比|狀態|EventType|Damage|DamageCrops|
|---|---|---|---|---|---|---|---|---|
|0|4574|77.7|||否|0
|1|2278|38.7||Hail|否|0
|2|5675|96.4||||0
|3|2371|40.3||Hail||0
|4|1279|21.7||Thunderstorm Wind||0
|5|2468|41.9||Hail||
|6|1310|22.3|||YES|
|7|1291|21.9||Thunderstorm Wind||

**包含自訂萬用字元的範例**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where monthofyear(StartTime) == 5
| extend Damage = iff(DamageCrops + DamageProperty > 0 , "YES" , "NO")
| project State, EventType, Damage, DamageCrops
| evaluate basket(0.2, '~', '~', '*', int(-1))
```

|SegmentId|計數|百分比|狀態|EventType|Damage|DamageCrops|
|---|---|---|---|---|---|---|---|---|
|0|4574|77.7|\*|\*|否|0
|1|2278|38.7|\*|Hail|否|0
|2|5675|96.4|\*|\*|\*|0
|3|2371|40.3|\*|Hail|\*|0
|4|1279|21.7|\*|Thunderstorm Wind|\*|0
|5|2468|41.9|\*|Hail|\*|-1
|6|1310|22.3|\*|\*|YES|-1
|7|1291|21.9|\*|Thunderstorm Wind|\*|-1
