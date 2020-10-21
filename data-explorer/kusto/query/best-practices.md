---
title: 查詢最佳做法-Azure 資料總管
description: 本文說明 Azure 資料總管中的查詢最佳做法。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/03/2020
ms.openlocfilehash: b5d5ef5ba3b29bf1bc468a64baa159106b164604
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249375"
---
# <a name="query-best-practices"></a>查詢最佳做法

以下是一些最佳作法，讓您的查詢執行速度更快。

|動作  |使用  |請勿使用  |注意  |
|---------|---------|---------|---------|
| **時間篩選** | 先使用時間篩選器。 ||Kusto 已高度優化，可使用時間篩選。| 
|**字串運算子**      | 使用 `has` 運算子     | 請勿使用 `contains`     | 尋找完整權杖時， `has` 效果會更好，因為它不會尋找子字串。   |
|**區分大小寫的運算子**     |  使用`==`       | 請勿使用  `=~`       |  可能的話，請使用區分大小寫的運算子。       |
| | 使用`in` | 請勿使用 `in~`|
|  | 使用`contains_cs`         | 請勿使用 `contains`        | 如果您可以使用 `has` / `has_cs` 且不能使用 `contains` / `contains_cs` ，則更好。 |
| **搜尋文字**    |    查看特定資料行     |    請勿使用  `*`    |   `*` 在所有資料行上進行全文檢索搜尋。    |
| **將 [動態物件](./scalar-data-types/dynamic.md) 中的欄位解壓縮到數百萬個數據列**    |  如果您大部分的查詢將動態物件中的欄位解壓縮到數百萬個數據列，請在內嵌時具體化您的資料行。      |         | 如此一來，您只需支付一次資料行解壓縮。    |
| **`let` 具有一次以上使用值的語句** | 使用 [具體化 ( # A1 函數](./materializefunction.md) |  |   如需有關如何使用的詳細資訊 `materialize()` ，請參閱 [具體化 ( # B1 ](materializefunction.md)。|
| **對超過1000000000筆記錄套用轉換**| 調整查詢的大小，以減少送出至轉換的資料量。| 如果可以避免，請不要轉換大量的資料。 | |
| **新查詢** | `limit [small number]` `count` 在結尾使用或。 | |     對未知的資料集執行未系結的查詢可能會產生 Gb 的結果，以傳回給用戶端，而導致回應緩慢和忙碌的叢集。|
| **不區分大小寫的比較** | 使用`Col =~ "lowercasestring"` | 請勿使用 `tolower(Col) == "lowercasestring"` |
| **比較已在小寫 (或大寫) 中的資料 ** | `Col == "lowercasestring"` (或 `Col == "UPPERCASESTRING"`) | 避免使用不區分大小寫的比較。||
| **篩選資料行** |  篩選資料表資料行。|不要篩選計算結果欄。 | |
| | 使用`T | where predicate(<expression>)` | 請勿使用 `T | extend _value = <expression> | where predicate(_value)` ||
| **summarize 運算子** |  使用提示。當摘要運算子的具有高基數時， [策略 = 隨機播放](./shufflequery.md) `group by keys` 。 | | 高基數最好高於1000000。|
|**[join 運算子](./joinoperator.md)** | 選取資料列較少的資料列，以作為查詢) 最左邊的第 (一個資料表。 ||
| 跨叢集聯結 |在叢集的「右側」端執行查詢，這是大部分的資料所在位置。 ||
|當左邊是小型且右側很大時加入 | 使用 [提示。策略 = 廣播](./broadcastjoin.md) || Small 指的是最多100000筆記錄。 |
|當兩邊太大時聯結 | 使用 [提示。策略 = 隨機](./shufflequery.md) || 當聯結索引鍵具有高基數時使用。|
|**使用共用相同格式或模式的字串來將資料行的值解壓縮**|  使用 [parse 運算子](./parseoperator.md) | 請勿使用數個 `extract()` 語句。  | 例如，值，例如 `"Time = <time>, ResourceId = <resourceId>, Duration = <duration>, ...."`
|**[將 ( # A1 函數解壓縮](./extractfunction.md)**| 當剖析的字串並非全部都遵循相同的格式或模式時，請使用。| |使用 REGEX 將需要的值解壓縮。|
| **[具體化 ( # A1 函數](./materializefunction.md)** | 推送所有可能會降低具體化資料集的運算子，但仍然保留查詢的語法。 | |例如，篩選或僅限專案的必要資料行。

