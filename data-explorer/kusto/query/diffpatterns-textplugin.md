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
ms.openlocfilehash: b0a71f9db9062d83f55ebf9db1efabb6d86f9786
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/05/2020
ms.locfileid: "87803279"
---
# <a name="diffpatterns_text-plugin"></a>diffpatterns_text 外掛程式

比較字串值的兩個資料集，並尋找描述兩個資料集之間差異的文字模式。

```kusto
T | evaluate diffpatterns_text(TextColumn, BooleanCondition)
```

`diffpatterns_text`會傳回一組文字模式，以在兩個集合中捕捉資料的不同部分 (也就是當條件為時，會以一種模式來捕捉大量百分比的資料列 `true` ，而當條件為) 時，則是資料列的百分比偏低 `false` 。 這些模式是從連續的 token 建立而成， (以空白字元分隔) 、文字資料行中的標記，或是 `*` 代表萬用字元的。 每個模式會以結果中的一個資料列表示。

## <a name="syntax"></a>語法

`T | evaluate diffpatterns_text(`TextColumn，BooleanCondition [，MinTokens，閾值，MaxTokens]`)` 

## <a name="arguments"></a>引數

### <a name="required-arguments"></a>必要的引數

* TextColumn- *column_name*

    要分析的文字資料行必須是字串類型。
    
* BooleanCondition-*布林運算式*

    定義如何產生要與輸入資料表比較的兩個記錄子集。 演算法會根據條件將查詢分割成兩個資料集 "True" 和 "False"，然後分析 (的文字) 兩者之間的差異。 

### <a name="optional-arguments"></a>選擇性引數

所有其他引數皆為選用，但必須為下列順序。 

* MinTokens-0 < *int* < 200 [預設值： 1]

    為每個結果模式設定非萬用字元標記的最小數目。

* 閾值-0.015 < *double* < 1 [預設值： 0.05]

    設定兩個集合之間) 差異的最小模式 (比例 (參閱[diffpatterns](diffpatternsplugin.md)) 。

* MaxTokens-0 < *int* [預設值： 20]

    設定從每個結果模式開始)  (的最大權杖數目，指定較低的限制會減少查詢執行時間。

## <a name="returns"></a>傳回

Diffpatterns_text 的結果會傳回下列資料行：

* Count_of_True：當條件為時符合模式的資料列數目 `true` 。
* Count_of_False：當條件為時符合模式的資料列數目 `false` 。
* Percent_of_True：當條件為時，與資料列中符合模式的資料列百分比 `true` 。
* Percent_of_False：當條件為時，與資料列中符合模式的資料列百分比 `false` 。
* Pattern：包含文字字串之標記和萬用字元之 ' ' 的文字模式 `*` 。 

> [!NOTE]
> 模式不一定是相異的，而且可能不會提供完整的資料集涵蓋範圍。 模式可能會重迭，而且某些資料列可能不會符合任何模式。

## <a name="example"></a>範例

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
