---
title: diffpatterns_text 外掛程式-Azure 資料總管
description: 本文說明 Azure 資料總管中的 diffpatterns_text 外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7ef4bf5607979cc02976d00250e8754f3a0c4e69
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225168"
---
# <a name="diffpatterns_text-plugin"></a>diffpatterns_text 外掛程式

比較字串值的兩個資料集，並尋找描述兩個資料集之間差異的文字模式。

```kusto
T | evaluate diffpatterns_text(TextColumn, BooleanCondition)
```

`diffpatterns_text`會傳回一組文字模式，以在兩個集合中捕捉資料的不同部分（也就是當條件為時，會捕捉大量百分比的資料列 `true` ，而當條件為時，則會使用較低百分比的資料列 `false` ）。 這些模式是以連續標記（以空白字元分隔）來建立，其中包含來自文字資料行的 token，或 `*` 代表萬用字元。 每個模式會以結果中的一個資料列表示。

**語法**

`T | evaluate diffpatterns_text(`TextColumn，BooleanCondition [，MinTokens，閾值，MaxTokens]`)` 

**必要的引數**

* TextColumn- *column_name*

    要分析的文字資料行必須是字串類型。
    
* BooleanCondition-*布林運算式*

    定義如何產生要與輸入資料表比較的兩個記錄子集。 演算法會根據條件將查詢分割成兩個資料集 "True" 和 "False"，然後分析兩者之間的（文字）差異。 

**選擇性引數**

所有其他引數皆為選用，但必須為下列順序。 

* MinTokens-0 < *int* < 200 [預設值： 1]

    為每個結果模式設定非萬用字元標記的最小數目。

* 閾值-0.015 < *double* < 1 [預設值： 0.05]

    設定兩個集合之間的最小模式（比率）差異（請參閱[diffpatterns](diffpatternsplugin.md)）。

* MaxTokens-0 < *int* [預設值： 20]

    設定每個結果模式的最大標記數目（從一開始），指定較低的限制會減少查詢執行時間。

**傳回**

Diffpatterns_text 的結果會傳回下列資料行：

* Count_of_True：當條件為時符合模式的資料列數目 `true` 。
* Count_of_False：當條件為時符合模式的資料列數目 `false` 。
* Percent_of_True：當條件為時，與資料列中符合模式的資料列百分比 `true` 。
* Percent_of_False：當條件為時，與資料列中符合模式的資料列百分比 `false` 。
* Pattern：包含文字字串之標記和萬用字元之 ' ' 的文字模式 `*` 。 

> [!NOTE]
> 模式不一定是相異的，而且可能不會提供完整的資料集涵蓋範圍。 模式可能會重迭，而且某些資料列可能不會符合任何模式。

**範例**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents     
| where EventNarrative != "" and monthofyear(StartTime) > 1 and monthofyear(StartTime) < 9
| where EventType == "Drought" or EventType == "Extreme Cold/Wind Chill"
| evaluate diffpatterns_text(EpisodeNarrative, EventType == "Extreme Cold/Wind Chill", 2)
```

|Count_of_True|Count_of_False|Percent_of_True|Percent_of_False|模式|
|---|---|---|---|---|
|11|0|6.29|0|股在 * 喚醒 * a surface 透過數目中的西北轉變 snowfall downwind * Lake 卓越|
|9|0|5.14|0|加拿大高壓力已結算 * * 區域 * 自2006年2月起產生最冷溫度。 持續時間 * 凍結溫度|
|0|34|0|6.24|* * * * * * * * * * * * * * * * * * West 田納西州，|
|0|42|0|7.71|* * * * * * * * * * * * * * * * * * * * * * * 在西歐。 *|
|0|45|0|8.26|* * 低於正常 *|
|0|110|0|20.18|低於一般 *|

