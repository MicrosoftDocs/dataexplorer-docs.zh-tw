---
title: diffpatterns_text 外掛程式-Azure 資料總管
description: 本文說明 Azure 資料總管中 diffpatterns_text 外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9efa144335369c3d450fadb7ac92e3dec4c87865
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249257"
---
# <a name="diffpatterns_text-plugin"></a>diffpatterns_text 外掛程式

比較兩個字串值資料集，並尋找描述兩個資料集之間差異的文字模式。

```kusto
T | evaluate diffpatterns_text(TextColumn, BooleanCondition)
```

`diffpatterns_text`會傳回一組文字模式，以在兩個集合中捕獲資料的不同部分 (亦即，當條件為時，會將大量百分比的資料列 `true` ，以及) 條件的資料列的低百分比 `false` 。 這些模式是從連續的權杖建立， (以泛空白字元分隔) ，以及來自文字資料行或 `*` 代表萬用字元的標記。 每個模式會以結果中的一個資料列表示。

## <a name="syntax"></a>語法

`T | evaluate diffpatterns_text(`TextColumn、BooleanCondition [、MinTokens、閾值、MaxTokens]`)` 

## <a name="arguments"></a>引數

### <a name="required-arguments"></a>必要的引數

* TextColumn- *column_name*

    要分析的文字資料行必須是字串類型。
    
* BooleanCondition- *布林運算式*

    定義如何產生兩個記錄子集來與輸入資料表進行比較。 演算法會根據條件將查詢分割成兩個資料集（"True" 和 "False"），然後分析 (的文字) 兩者之間的差異。 

### <a name="optional-arguments"></a>選擇性引數

所有其他引數皆為選用，但必須為下列順序。 

* MinTokens-0 < *int* < 200 [default： 1]

    針對每個結果模式設定非萬用字元標記的最少數目。

* 閾值-0.015 < *雙精度* < 1 [預設值： 0.05]

    設定兩個集合之間) 差異的最短模式 (比例 (請參閱 [diffpatterns](diffpatternsplugin.md)) 。

* MaxTokens-0 < *int* [default： 20]

    設定從每個結果模式開始)  (的最大權杖數目，指定較低的限制會減少查詢執行時間。

## <a name="returns"></a>傳回

Diffpatterns_text 的結果會傳回下列資料行：

* Count_of_True：條件為時符合模式的資料列數目 `true` 。
* Count_of_False：條件為時符合模式的資料列數目 `false` 。
* Percent_of_True：條件為時，符合模式的資料列百分比 `true` 。
* Percent_of_False：條件為時，符合模式的資料列百分比 `false` 。
* 模式：包含文字字串之標記的文字模式，以及萬用字元的 ' `*` '。 

> [!NOTE]
> 這些模式不一定是唯一的，而且可能不會提供資料集的完整涵蓋範圍。 這些模式可能會重迭，且某些資料列可能不符合任何模式。

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
|11|0|6.29|0|接近尾聲右移了 * 喚醒 * 表面透過數目帶來了繁重的 lake 效果 snowfall downwind * Lake 自|
|9|0|5.14|0|加拿大高壓力已結算 * * 區域 * 自2006年2月起產生最冷溫度。 持續時間 * 凍結溫度|
|0|34|0|6.24|* * * * * * * * * * * * * * * * * * 西部田納西州，|
|0|42|0|7.71|* * * * * * 在各西方的科羅拉多上導致 * * * * * * * *。 *|
|0|45|0|8.26|* * 低於一般 *|
|0|110|0|20.18|一般 *|
