---
title: 查詢最佳做法-Azure 資料總管
description: 本文說明 Azure 資料總管中的查詢最佳做法。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/03/2020
ms.openlocfilehash: e37d3733909b84a2991c54a9242d7f44b18d64cb
ms.sourcegitcommit: de81b57b6c09b6b7442665e5c2932710231f0773
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87264676"
---
# <a name="query-best-practices"></a>查詢最佳做法

以下是幾個可讓您的查詢執行速度更快的最佳作法。

|動作  |用途  |不使用  |注意  |
|---------|---------|---------|---------|
| **時間篩選** | 先使用時間篩選器。 ||Kusto 已高度優化，可使用時間篩選準則。| 
|**字串運算子**      | 使用 `has` 運算子     | 不使用`contains`     | 尋找完整權杖時， `has` 效果較佳，因為它不會尋找子字串。   |
|**區分大小寫的運算子**     |  使用`==`       | 不使用`=~`       |  可能的話，請使用區分大小寫的運算子。       |
| | 使用`in` | 不使用`in~`|
|  | 使用`contains_cs`         | 不使用`contains`        | 如果您可以使用 `has` / `has_cs` 且不使用 `contains` / `contains_cs` ，這甚至更好。 |
| **搜尋文字**    |    查看特定資料行     |    不使用`*`    |   `*`在所有資料行上進行全文檢索搜尋。    |
| **從跨數百萬個數據列的[動態物件](./scalar-data-types/dynamic.md)中解壓縮欄位**    |  如果大部分的查詢都從動態物件中的數百萬個數據列提取欄位，請在內嵌時具體化您的資料行。      |         | 如此一來，您只需要針對資料行解壓縮付費一次。    |
| **`let`語句，其中包含您多次使用的值** | 使用[具體化（）函數](./materializefunction.md) |  |   如需如何使用的詳細資訊 `materialize()` ，請參閱[具體化（）](materializefunction.md)。|
| **對超過1000000000筆記錄套用轉換**| 改變查詢的大小，以減少送入轉換的資料量。| 若可以避免，請不要轉換大量資料。 | |
| **新查詢** | `limit [small number]` `count` 在結尾使用或。 | |     在未知的資料集上執行未系結的查詢，可能會產生要傳回給用戶端的 Gb 結果，因而導致回應緩慢和忙碌的叢集。|
| **不區分大小寫比較** | 使用`Col =~ "lowercasestring"` | 不使用`tolower(Col) == "lowercasestring"` |
| **比較已經是小寫（或大寫）的資料** | `Col == "lowercasestring"` (或 `Col == "UPPERCASESTRING"`) | 避免使用不區分大小寫的比較。||
| **篩選資料行** |  篩選資料表資料行。|不要篩選計算結果欄。 | |
| | 使用`T | where predicate(<expression>)` | 不使用`T | extend _value = <expression> | where predicate(_value)` ||
| **summarize 運算子** |  使用提示。當摘要運算子的具有高基數時，[策略 = 隨機播放](./shufflequery.md) `group by keys` 。 | | 高基數最理想的是1000000以上。|
|**[join 運算子](./joinoperator.md)** | 選取資料列較少的資料表（查詢最左邊）。 ||
| 跨叢集聯結 |跨叢集執行查詢，在聯結的「右側」端，其中大部分的資料都位於其中。 ||
|當左邊為小型且右側為大時加入 | 使用[提示。策略 = 廣播](./broadcastjoin.md) || 小型指的是最多100000筆記錄。 |
|當兩邊太大時加入 | 使用[提示。策略 = 隨機播放](./shufflequery.md) || 當聯結索引鍵具有高基數時，請使用。|
|**使用共用相同格式或模式的字串，來解壓縮資料行上的值**|  使用[parse 運算子](./parseoperator.md) | 請勿使用數個 `extract()` 語句。  | 例如，之類的值`"Time = <time>, ResourceId = <resourceId>, Duration = <duration>, ...."`
|**[解壓縮（）函數](./extractfunction.md)**| 當剖析的字串不是全部遵循相同的格式或模式時，請使用。| |使用 REGEX 來解壓縮必要的值。|
| **[具體化（）函數](./materializefunction.md)** | 推送所有可能會減少具體化資料集的運算子，並繼續保留查詢的語法。 | |例如，篩選或僅專案必要的資料行。

