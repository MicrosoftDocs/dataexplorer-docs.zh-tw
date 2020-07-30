---
title: pivot 外掛程式-Azure 資料總管
description: 本文說明 Azure 資料總管中的樞紐分析表外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d2f9db1dbace646c41d8751272cf44cf6d04c2c3
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346128"
---
# <a name="pivot-plugin"></a>pivot 外掛程式

藉由將輸入資料表中某個資料行的唯一值轉換成輸出資料表中的多個資料行來旋轉資料表，並在最後輸出所需的任何剩餘資料行值上執行需要的匯總。

```kusto
T | evaluate pivot(PivotColumn)
```

## <a name="syntax"></a>語法

`T | evaluate pivot(`*pivotColumn* `[, `*aggregationFunction* `] [,`*column1* `[,`*column2* .。。`]])`

## <a name="arguments"></a>引數

* *pivotColumn*：要旋轉的資料行。 此資料行中的每個唯一值都是輸出資料表中的資料行。
* *彙總函式*：（選擇性）將輸入資料表中的多個資料列匯總到輸出資料表中的單一資料列。 目前支援的函式：、、、、、、、、、、、 `min()` `max()` `any()` `sum()` `dcount()` `avg()` `stdev()` `variance()` `make_list()` `make_bag()` `make_set()` `count()` （預設值為 `count()` ）。
* *column1*、 *column2*、...：（選擇性）資料行名稱。 輸出資料表會在每個指定的資料行中包含一個額外的資料行。 預設值： [已切換] 資料行和 [匯總] 資料行以外的所有資料行。

## <a name="returns"></a>傳回

Pivot 會傳回具有指定之資料行的旋轉資料表（*column1*、 *column2*、...）加上 pivot 資料行的所有唯一值。 切換資料行的每個資料格都會包含彙總函式計算。

**注意**

外掛程式的輸出架構 `pivot` 是以資料為基礎，因此查詢可能會針對兩個執行產生不同的架構。 這也表示參考解壓縮資料行的查詢可能會在任何時間變成「已中斷」。 基於這個理由，建議您不要將此外掛程式用於自動化作業。

## <a name="examples"></a>範例

### <a name="pivot-by-a-column"></a>依資料行 Pivot

針對每個以 ' AL ' 為開頭的事件種類和狀態，計算此狀態中此類型的事件數目。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| project State, EventType 
| where State startswith "AL" 
| where EventType has "Wind" 
| evaluate pivot(State)
```

|EventType|ALABAMA|ALASKA|
|---|---|---|
|Thunderstorm Wind|352|1|
|高風|0|95|
|極端冷/風冷藏|0|10|
|強式風|22|0|


### <a name="pivot-by-a-column-with-aggregation-function"></a>依據具有彙總函式的資料行進行 Pivot。

針對每個具有開頭為 ' AR ' 的事件數和狀態，顯示直接裝死的總數。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where State startswith "AR" 
| project State, EventType, DeathsDirect 
| where DeathsDirect > 0
| evaluate pivot(State, sum(DeathsDirect))
```

|EventType|ARKANSAS|亞利桑那州|
|---|---|---|
|繁重 Rain|1|0|
|Thunderstorm Wind|1|0|
|卻|0|1|
|Flash Flood|0|6|
|強式風|1|0|
|熱度|3|0|


### <a name="pivot-by-a-column-with-aggregation-function-and-a-single-additional-column"></a>依據具有彙總函式和單一額外資料行的資料行來進行 Pivot。

結果與上一個範例相同。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where State startswith "AR" 
| project State, EventType, DeathsDirect 
| where DeathsDirect > 0
| evaluate pivot(State, sum(DeathsDirect), EventType)
```

|EventType|ARKANSAS|亞利桑那州|
|---|---|---|
|繁重 Rain|1|0|
|Thunderstorm Wind|1|0|
|卻|0|1|
|Flash Flood|0|6|
|強式風|1|0|
|熱度|3|0|


### <a name="specify-the-pivoted-column-aggregation-function-and-multiple-additional-columns"></a>指定 [已切換資料行]、[彙總函式] 和多個其他資料行。

針對每個事件種類 [來源] 和 [狀態]，加總直接裝死的數目。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where State startswith "AR" 
| where DeathsDirect > 0
| evaluate pivot(State, sum(DeathsDirect), EventType, Source)
```

|EventType|來源|ARKANSAS|亞利桑那州|
|---|---|---|---|
|繁重 Rain|Emergency Manager|1|0|
|Thunderstorm Wind|Emergency Manager|1|0|
|卻|Newspaper|0|1|
|Flash Flood|Trained Spotter|0|2|
|Flash Flood|廣播媒體|0|3|
|Flash Flood|Newspaper|0|1|
|強式風|執法機關|1|0|
|熱度|Newspaper|3|0|
