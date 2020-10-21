---
title: pivot 外掛程式-Azure 資料總管
description: 本文說明 Azure 資料總管中的 pivot 外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 430370404a7111f808a5d343b7fcd58c4eef0b41
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249673"
---
# <a name="pivot-plugin"></a>pivot 外掛程式

藉由將輸入資料表中某資料行的唯一值轉換成輸出資料表中的多個資料行，來旋轉資料表，並在需要最後輸出的任何其餘資料行值上執行需要的匯總。

```kusto
T | evaluate pivot(PivotColumn)
```

> [!NOTE]
> 外掛程式的輸出架構 `pivot` 是以資料為基礎，因此查詢可能會針對任何兩個執行產生不同的架構。 這也表示，參考已解壓縮資料行的查詢可能會在任何時間變成「中斷」。 基於這個理由，不建議使用此外掛程式進行自動化工作。

## <a name="syntax"></a>語法

`T | evaluate pivot(`*pivotColumn* `[, `*aggregationFunction* `] [,`*column1* `[,`*column2* .。。`]])`

## <a name="arguments"></a>引數

* *pivotColumn*：要旋轉的資料行。 此資料行中的每個唯一值都將是輸出資料表中的資料行。
* *彙總函式*： (選擇性) 將輸入資料表中的多個資料列匯總到輸出資料表中的單一資料列。 目前支援的函數： `min()` 、 `max()` 、 `any()` 、 `sum()` 、、、、、、、 `dcount()` `avg()` `stdev()` `variance()` `make_list()` `make_bag()` `make_set()` `count()` (預設值為 `count()`) 。
* *column1*， *column2*，...： (選擇性的) 資料行名稱。 輸出資料表將會在每個指定的資料行中包含一個額外的資料行。 預設值：已切換資料行和匯總資料行以外的所有資料行。

## <a name="returns"></a>傳回

Pivot 會傳回具有指定資料行的旋轉資料表 (*column1*、 *column2*、... ) 加上資料行的所有唯一值。 移動資料行的每個資料格都會包含彙總函式計算。

## <a name="examples"></a>範例

### <a name="pivot-by-a-column"></a>依資料行進行 Pivot

針對以 ' AL ' 開頭的每個型別和狀態，在此狀態中計算此類型的事件數目。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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
|高風|0|95|
|極端的冷/風冷藏|0|10|
|強大風|22|0|


### <a name="pivot-by-a-column-with-aggregation-function"></a>使用彙總函式依資料行進行 Pivot

針對每個以 ' AR ' 開頭的事件狀態和狀態，顯示直接裝死總數。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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
|強大風|1|0|
|熱|3|0|


### <a name="pivot-by-a-column-with-aggregation-function-and-a-single-additional-column"></a>依具有彙總函式和單一額外資料行的資料行進行 Pivot

結果與上一個範例相同。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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
|強大風|1|0|
|熱|3|0|


### <a name="specify-the-pivoted-column-aggregation-function-and-multiple-additional-columns"></a>指定已切換資料行、彙總函式和多個其他資料行

針對每個事件種類、來源和狀態，加總直接裝死的數目。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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
|強大風|執法機關|1|0|
|熱|Newspaper|3|0|
