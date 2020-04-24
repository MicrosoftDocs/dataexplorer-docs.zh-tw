---
title: diffpatterns_text外掛程式 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的diffpatterns_text外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 58f059e2346dfb3f15bff295126a14a97f10f317
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516009"
---
# <a name="diffpatterns_text-plugin"></a>diffpatterns_text外掛程式

比較兩個字串值數據集,並查找描述兩個數據集之間的差異的文本模式。

```kusto
T | evaluate diffpatterns_text(TextColumn, BooleanCondition)
```

返回`diffpatterns_text`一組文本模式,這些模式在兩集中捕獲數據的不同部分(即,在條件`true`為 時捕獲大量行的模式,在條件為 時捕獲大量行的模式,`false`在條件為 時捕獲大量行的百分比。 模式由連續標記(由空格分隔)構建,帶有文本列中的標記或`*`表示通配符。 每個模式會以結果中的一個資料列表示。

**語法**

`T | evaluate diffpatterns_text(`文字列, 布林條件 [, 最小權限, 閾值, 最大權杖 ]`)` 

**必要參數**

* 文字列 ─ *column_name*

    要分析的文字列,必須是類型字串。
    
* 布林條件 -*布林表示式*

    定義如何生成兩個記錄子集以與輸入表進行比較。 該演算法根據條件將查詢拆分為兩個數據集「True」和「False」,然後分析它們之間的(文本)差異。 

**替代參數**

所有其他引數皆為選用，但必須為下列順序。 

* MinTokens - 0 < *int* < 200 [預設值: 1]

    設置每個結果模式的最低非通配符權杖數。

* 下限 - 0.015 <*雙*< 1 [默認值: 0.05]

    設置兩個集之間的最小模式(比率)差值(請參閱[差異模式](diffpatternsplugin.md))。

* 最大權杖 - 0 < *int* [預設值: 20]

    設置每個結果模式的最大權杖數(從開始),指定較低的限制會減少查詢運行時。

**傳回**

diffpatterns_text的結果返回以下列:

* Count_of_True:條件為`true`時與模式匹配的行數。
* Count_of_False:條件為`false`時與模式匹配的行數。
* Percent_of_True:條件為 時,從行中匹配模式的行`true`的 百分比。
* Percent_of_False:條件為 時,從行中匹配模式的行`false`的 百分比。
* 模式:包含文字字串中的標記和通配元的''`*`的文本模式。 

> [!NOTE]
> 模式不一定不同,可能無法提供數據集的完整覆蓋範圍。 模式可能重疊,某些行可能與任何模式不匹配。

**範例**

```kusto
StormEvents     
| where EventNarrative != "" and monthofyear(StartTime) > 1 and monthofyear(StartTime) < 9
| where EventType == "Drought" or EventType == "Extreme Cold/Wind Chill"
| evaluate diffpatterns_text(EpisodeNarrative, EventType == "Extreme Cold/Wind Chill", 2)
```
|Count_of_True|Count_of_False|Percent_of_True|Percent_of_False|模式|
|---|---|---|---|---|
|11|0|6.29|0|風向西北移動 ~ 後起 • 表面槽帶來強湖效應降雪下風 • 湖優越從|
|9|0|5.14|0|加拿大高壓穩定 – 地區 – 產生了自 2006 年 2 月 - 2006 年以來最冷的溫度。 持續時間 = 凍結溫度|
|0|34|0|6.24|[ ] * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * *|
|0|42|0|7.71|[ ] [ ] [ ] [ ] 在科羅拉多州西部引起 * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * *|
|0|45|0|8.26|【 低於正常 】|
|0|110|0|20.18|低於正常 *|

