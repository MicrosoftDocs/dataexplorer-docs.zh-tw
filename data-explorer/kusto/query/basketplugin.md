---
title: 購物籃外掛程式 - Azure 資料資源管理員 |微軟文件
description: 本文介紹了 Azure 數據資源管理器中的購物籃外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 05/26/2019
ms.openlocfilehash: 06fd1e33624bca6aee18a1ca969af18656c6b3e0
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663872"
---
# <a name="basket-plugin"></a>籃子外掛程式

```kusto
T | evaluate basket()
```

Basket 可尋找資料中離散屬性 (維度) 的所有常見模式，將會傳回在原始查詢中傳遞頻率臨界值的所有常見模式。 購物籃保證在數據中找到所有頻繁的模式,但不能保證具有多項式運行時,查詢的運行時在行數中是線性的,但在某些情況下,列數(維度)可能呈指數級。 Basket 是以最初針對購物籃分析資料採礦而開發的 Apriori 演算法為基礎。

**語法**

`T | evaluate basket(`*參數*`)`

**傳回**

置物籃會傳回高於比例閾值 (預設值：0.05) 的所有經常出現模式。每個模式是以結果中的一個資料列表示。

第一列是段 ID。接下來的兩列是模式捕獲的原始查詢中行的計數和百分比。 其餘的資料行來自原始查詢，其值是資料行中的特定值或表示變數值的萬用字元值 (預設為 null)。

**引數 (全部選用)**

`T | evaluate basket(`[*閾值*,*重量柱*,*最大尺寸*,*自訂通配符*,*自訂通配符*, ...]`)`

所有引數皆為選用，但必須為上述順序。 若要指示需使用預設值，請放置字串波狀符號值 - '~' (請參閱以下範例)。

可用參數:

* 下限 - 0.015 <*雙*< 1 [默認值: 0.05]

    將資料列的最小比率設為認定的頻繁 (不會傳回較小比例的模式)。
    
    範例： `T | evaluate basket(0.02)`

* WeightColumn - *column_name*

    根據指定的權數 (依預設每個資料都列具有權數 '1') 考慮輸入中的每個資料列。 引數必須是數值資料行名稱 (例如 int、long、real)。 權數資料行的常見用法是考慮已內嵌至各資料列的資料取樣或分組/彙總。
    
    範例： `T | evaluate basket('~', sample_Count)`

* 最大尺寸 - 1 < *int* [預設值: 5]

    設定每個 Basket 的不相關維度數目上限 (依預設會受到限制以縮減查詢執行階段)。

    範例： `T | evaluate basket('~', '~', 3)`

* 自定義通配符 - *"any_value_per_type"*

    在結果資料表中設定特定類型的萬用字元值，會指出目前的模式沒有這個資料行的限制。
    預設值是 null，而字串預設值為空字串。 如果預設值是數據中的可行值,則應使用不同的通配符值(例如`*`)。
    請參閱下列範例。

    範例： `T | evaluate basket('~', '~', '~', '*', int(-1), double(-1), long(0), datetime(1900-1-1))`

**範例**

```kusto
StormEvents 
| where monthofyear(StartTime) == 5
| extend Damage = iff(DamageCrops + DamageProperty > 0 , "YES" , "NO")
| project State, EventType, Damage, DamageCrops
| evaluate basket(0.2)
```

|SegmentId|Count|百分比|State|EventType|Damage|DamageCrops|
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

```kusto
StormEvents 
| where monthofyear(StartTime) == 5
| extend Damage = iff(DamageCrops + DamageProperty > 0 , "YES" , "NO")
| project State, EventType, Damage, DamageCrops
| evaluate basket(0.2, '~', '~', '*', int(-1))
```

|SegmentId|Count|百分比|State|EventType|Damage|DamageCrops|
|---|---|---|---|---|---|---|---|---|
|0|4574|77.7|\*|\*|否|0
|1|2278|38.7|\*|Hail|否|0
|2|5675|96.4|\*|\*|\*|0
|3|2371|40.3|\*|Hail|\*|0
|4|1279|21.7|\*|Thunderstorm Wind|\*|0
|5|2468|41.9|\*|Hail|\*|-1
|6|1310|22.3|\*|\*|YES|-1
|7|1291|21.9|\*|Thunderstorm Wind|\*|-1
