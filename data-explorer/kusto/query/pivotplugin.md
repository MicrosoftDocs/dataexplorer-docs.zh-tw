---
title: 樞紐分析外掛程式 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的透視外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e4ec5a94483ade822280ee4a71106c214bb4b9a5
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511096"
---
# <a name="pivot-plugin"></a>樞軸外掛程式

通過將輸入表中的一列唯一值轉換為輸出表中的多個列來旋轉表,並在最終輸出中所需的任何剩餘列值執行所需的聚合。

```kusto
T | evaluate pivot(PivotColumn)
```

**語法**

`T | evaluate pivot(`*樞軸列*`[, `*彙總函數*`] [,`*列 1* `[,`*欄 2* ...`]])`

**引數**

* *樞軸列*:要旋轉的列。 此列中的每個唯一值將是輸出表中的一列。
* *聚合函數*:(可選)將輸入表中的多行聚合到輸出表中的單個行。 目前`min()`支援的函數 : `max()` `any()` `sum()` `dcount()`、 `avg()` `stdev()` `variance()`、 `count()` `count()`、 、 、 、 、 、 、 、 與預設值為 )
* *列 1*,*列 2*,...:(可選)列名稱。 輸出表將包含每個指定列的附加欄。 預設值:旋轉列和聚合列以外的所有列。

**傳回**

數據透視返回具有指定列(列*1、**列 2*、...) 以及透視列所有唯一值的旋轉表。 透視列的每個單元格將包含聚合函數計算。

**注意**

外掛程式`pivot`的 輸出架構基於資料,因此查詢可能會為任意兩次運行生成不同的架構。 這也意味著引用未壓縮列的查詢可能隨時變為"中斷」。 因此,不建議使用此外掛程式進行自動化作業。

**範例**

### <a name="pivot-by-a-column"></a>依列透視

對於以"AL"開頭的每個事件類型和狀態,計算此狀態下此類型的事件數。

```kusto
StormEvents
| project State, EventType 
| where State startswith "AL" 
| where EventType has "Wind" 
| evaluate pivot(State)
```

|EventType|ALABAMA|阿拉斯加|
|---|---|---|
|Thunderstorm Wind|352|1|
|大風|0|95|
|極端冷/風寒|0|10|
|強風|22|0|


### <a name="pivot-by-a-column-with-aggregation-function"></a>由具有聚合函數的列透視。

對於以「AR」開頭的每個事件類型和國家,顯示直接死亡總數。

```kusto
StormEvents 
| where State startswith "AR" 
| project State, EventType, DeathsDirect 
| where DeathsDirect > 0
| evaluate pivot(State, sum(DeathsDirect))
```

|EventType|阿肯色州|亞利桑那州|
|---|---|---|
|大雨|1|0|
|Thunderstorm Wind|1|0|
|閃電|0|1|
|Flash Flood|0|6|
|強風|1|0|
|熱|3|0|


### <a name="pivot-by-a-column-with-aggregation-function-and-a-single-additional-column"></a>由具有聚合函數的列和單個附加列透視。

結果與前面的示例相同。

```kusto
StormEvents 
| where State startswith "AR" 
| project State, EventType, DeathsDirect 
| where DeathsDirect > 0
| evaluate pivot(State, sum(DeathsDirect), EventType)
```

|EventType|阿肯色州|亞利桑那州|
|---|---|---|
|大雨|1|0|
|Thunderstorm Wind|1|0|
|閃電|0|1|
|Flash Flood|0|6|
|強風|1|0|
|熱|3|0|


### <a name="specify-the-pivoted-column-aggregation-function-and-multiple-additional-columns"></a>指定透視列、聚合函數和多個附加列。

對於每個事件類型、源和狀態,並求直接死亡數。

```kusto
StormEvents 
| where State startswith "AR" 
| where DeathsDirect > 0
| evaluate pivot(State, sum(DeathsDirect), EventType, Source)
```

|EventType|來源|阿肯色州|亞利桑那州|
|---|---|---|---|
|大雨|Emergency Manager|1|0|
|Thunderstorm Wind|Emergency Manager|1|0|
|閃電|Newspaper|0|1|
|Flash Flood|Trained Spotter|0|2|
|Flash Flood|廣播媒體|0|3|
|Flash Flood|Newspaper|0|1|
|強風|執法機關|1|0|
|熱|Newspaper|3|0|