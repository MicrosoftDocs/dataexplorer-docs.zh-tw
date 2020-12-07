---
title: 查詢最佳做法 - Azure 資料總管
description: 本文說明 Azure 資料總管中的查詢最佳做法。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/03/2020
ms.localizationpriority: high
ms.openlocfilehash: 762c3075c162ba35bdba539d0e86460c78f3297e
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "95511783"
---
# <a name="query-best-practices"></a>查詢最佳做法

以下有數個可供遵循的最佳做法，讓您更快速執行查詢。

|動作  |使用  |請勿使用  |注意  |
|---------|---------|---------|---------|
| **時間篩選條件** | 先使用時間篩選器。 ||Kusto 已高度最佳化為使用時間篩選條件。| 
|**字串運算子**      | 使用 `has` 運算子     | 請勿使用 `contains`     | 尋找完整權杖時，`has` 的效果更好，因為其不會尋找子字串。   |
|**區分大小寫的運算子**     |  使用`==`       | 請勿使用 `=~`       |  可能的話，請使用區分大小寫的運算子。       |
| | 使用`in` | 請勿使用 `in~`|
|  | 使用`contains_cs`         | 請勿使用 `contains`        | 如果您可使用 `has`/`has_cs`，而不使用 `contains`/`contains_cs`，甚至更好。 |
| **搜尋文字**    |    查看特定資料行     |    請勿使用 `*`    |   `*` 會在所有資料行中進行全文檢索搜尋。    |
| **從跨數百萬個資料列的 [dynamic 物件](./scalar-data-types/dynamic.md)中擷取欄位**    |  如果大部分查詢都會從跨數百萬個資料列的 dynamic 物件中擷取欄位，請在擷取時將您的資料行具體化。      |         | 如此一來，您只需針對資料行擷取付費一次。    |
| **`let` 陳述式，其中包含您多次使用的值** | 使用 [materialize() 函式](./materializefunction.md) |  |   如需如何使用 `materialize()` 的詳細資訊，請參閱 [materialize()](materializefunction.md)。|
| **對 10 億筆以上的記錄套用轉換**| 調整查詢，以減少送入轉換的資料量。| 若可避免，請不要轉換大量資料。 | |
| **新查詢** | 在結尾使用 `limit [small number]` 或 `count`。 | |     在未知的資料集上執行未繫結的查詢可能會產生要傳回給用戶端的數 GB 結果，進而導致回應緩慢及叢集忙碌。|
| **不區分大小寫的比較** | 使用`Col =~ "lowercasestring"` | 請勿使用 `tolower(Col) == "lowercasestring"` |
| **比較已經是小寫 (或大寫) 的資料** | `Col == "lowercasestring"` (或 `Col == "UPPERCASESTRING"`) | 避免使用不區分大小寫的比較。||
| **依據資料行篩選** |  依據資料表資料行篩選。|請勿依據計算結果欄篩選。 | |
| | 使用`T | where predicate(<expression>)` | 請勿使用 `T | extend _value = <expression> | where predicate(_value)` ||
| **summarize 運算子** |  當 summarize 運算子的 `group by keys` 具有高基數時使用 [hint.strategy=shuffle](./shufflequery.md)。 | | 高基數理想上高於 1 百萬。|
|**[join 運算子](./joinoperator.md)** | 選取具有較少資料列的資料表作為第一個資料表 (查詢最左邊)。 ||
| 跨叢集聯結 |跨叢集，在聯結的「右」側執行查詢，大部分的資料都位於聯結中。 ||
|當左側較小而右側較大時聯結 | 使用 [hint.strategy=broadcast](./broadcastjoin.md) || 較小是指最多 100,000 筆記錄。 |
|當兩邊都太大時聯結 | 使用 [hint.strategy=shuffle](./shufflequery.md) || 當聯結索引鍵具有高基數時使用。|
|**在有字串共用相同格式或模式的資料行上擷取值**|  使用 [parse 運算子](./parseoperator.md) | 請勿使用數個 `extract()` 陳述式。  | 例如，`"Time = <time>, ResourceId = <resourceId>, Duration = <duration>, ...."` 之類的值
|**[extract() 函式](./extractfunction.md)**| 當剖析的字串並未全部遵循相同的格式或模式時使用。| |使用 REGEX 來擷取必要的值。|
| **[materialize() 函式](./materializefunction.md)** | 推送所有可能會減少具體化資料集的運算子，並繼續保留查詢的語意。 | |例如，使用篩選條件，或僅投射必要的資料行。

