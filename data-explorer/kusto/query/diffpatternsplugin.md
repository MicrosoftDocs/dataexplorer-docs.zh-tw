---
title: 差異模式外掛程式 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的差異模式外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: fc4d1c7441e02eeeb1d90e1f8c0f2a521e3793da
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515992"
---
# <a name="diffpatterns-plugin"></a>擴散模式外掛程式

```kusto
T | evaluate diffpatterns(splitColumn)
```
比較同一結構的兩個數據集,並查找描述兩個數據集之間的差異的離散屬性(維度)的模式。 Diffpatterns 特別開發來協助分析失敗 (例如：藉由比較指定時間範圍內的失敗與非失敗），但有可能找到相同結構的任兩個資料集之間的差異。 

**語法**

`T | evaluate diffpatterns(`分割柱、拆分值A、拆分值B [、權重列、閾值、最大維度、自定義通配符...]`)` 

**必要參數**

* SplitColumn - *column_name*

    告知演算法如何將查詢分割成資料集。 根據 SplitValueA 和 SplitValueB 引數的指定值 (請參閱下文)，演算法會將查詢分割成兩個資料集 "A"和"B"，然後分析兩者之間的差異。 因此，分割資料行必須至少有兩個相異值。

* SplitValueA - *string*

    指定之 SplitColumn 中其中一個值的字串表示。 在其 SplitColumn 中具備此值的所有資料列均會視為資料集 "A"。

* SplitValueB - *string*

    指定之 SplitColumn 中其中一個值的字串表示。 在其 SplitColumn 中具有此值的所有行都被視為數據集「B」。。

    範例： `T | extend splitColumn=iff(request_responseCode == 200, "Success" , "Failure") | evaluate diffpatterns(splitColumn, "Success","Failure") `

**替代參數**

所有其他引數皆為選用，但必須為下列順序。 若要指示需使用預設值，請放置字串波狀符號值 - '~' (請參閱以下範例)。

* WeightColumn - *column_name*

    根據指定的權數 (依預設每個資料都列具有權數 '1') 考慮輸入中的每個資料列。 引數必須是數值資料行名稱 (例如 int、long、real)。
    權數資料行的常見用法是考慮已內嵌至各資料列的資料取樣或分組/彙總。
    
    範例： `T | extend splitColumn=iff(request_responseCode == 200, "Success" , "Failure") | evaluate diffpatterns(splitColumn, "Success","Failure", sample_Count) `

* 下限 - 0.015 <*雙*< 1 [默認值: 0.05]

    設定兩個集合之間的最小模式 (比率) 差異。

    範例：`T | extend splitColumn = iff(request-responseCode == 200, "Success" , "Failure") | evaluate diffpatterns(splitColumn, "Success","Failure", "~", 0.04)`

* 最大尺寸 - 0 < *int* [預設值:無限制]

    設定每個結果模式的不相關維度數目上限，指定限制會縮減查詢執行階段。

    範例：`T | extend splitColumn = iff(request-responseCode == 200, "Success" , "Failure") | evaluate diffpatterns(splitColumn, "Success","Failure", "~", "~", 3)`

* 自定義通配符 - *"每種類型的任意值"*

    在結果資料表中設定特定類型的萬用字元值，會指出目前的模式沒有這個資料行的限制。
    預設值是 null，而字串預設值為空字串。 如果預設值是數據中的可行值,則應使用不同的通配符值(例如`*`)。
    請參閱下列範例。

    範例： `T | extend splitColumn = iff(request-responseCode == 200, "Success" , "Failure") | evaluate diffpatterns(splitColumn, "Success","Failure", "~", "~", "~", int(-1), double(-1), long(0), datetime(1900-1-1))`

**傳回**

Diffpatterns 會傳回一組 (通常很少) 模式，以擷取兩個集合中的不同資料部分 (也就是，此模式會擷取第一個資料集中高百分比的資料列和第二個集合中低百分比的資料列)。 每個模式會以結果中的一個資料列表示。

衍射模式的結果傳回以下列:

* 段段 ID:分配給當前查詢中模式的 ID(注意:在重複查詢中不保證 ID 相同)。

* CountA:模式在集 A 中捕獲的行數(集`where tostring(splitColumn) == SplitValueA`A 等效 於 )。

* CountB:模式在集 B 中擷取的行數(集 B`where tostring(splitColumn) == SplitValueB`等效於)。

* 百分比A:模式擷取的 Set A 中的行百分比 (100.0 = CountA / 計數(SetA)。

* 百分比B:模式捕獲的 Set B 中的行百分比 (100.0 = CountB / 計數(SetB)。

* 百分比差異:A 和 B 之間的絕對百分點差 ( |百分比A - 百分比B*) 是描述兩組差異模式重要性的主要度量。

* 列的其餘部分:是輸入的原始架構並描述模式,每行(模式)重新引發列的非通配符值的交集(相當於`where col1==val1 and col2==val2 and ... colN=valN`行中每個非通配符值)。

對於每個模式,未在模式中設置的列(即對特定值沒有限制)將包含一個通配符值,預設情況下該值為空(請參閱下面的"參數"部分,說明如何手動更改通配符)。

* 注意:模式通常不同,它們可能是重疊的,通常不覆蓋所有原始行。 某些資料列可能不會落在任何模式之下。


**技巧**

在輸入管道中使用[位置](./whereoperator.md)和[專案](./projectoperator.md)將資料減少到您感興趣的內容。

當您找到有趣的資料列時，您可藉由將其特定值加入至您的 `where` 篩選器，進一步深入探索。

* 注意:差異模式旨在查找顯著模式(捕獲集之間的部分數據差異),而不是針對逐行差異。

**範例**

```kusto
StormEvents 
| where monthofyear(StartTime) == 5
| extend Damage = iff(DamageCrops + DamageProperty > 0 , 1 , 0)
| project State , EventType , Source , Damage, DamageCrops
| evaluate diffpatterns(Damage, "0", "1" )
```
|SegmentId|CountA|CountB|PercentA|PercentB|百分比迪法|State|EventType|來源|DamageCrops|
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

