---
title: 字串運算子-Azure 資料總管
description: 本文說明 Azure 資料總管中的字串運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8ad104b7802bde2355b46bc31e74e63a6708d4f4
ms.sourcegitcommit: d2edf654f71f8686d1f03d8ec16200f84e671b12
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88659258"
---
# <a name="string-operators"></a>字串運算子

Kusto 提供各種查詢運算子來搜尋字串資料類型。 下列文章說明如何編制字串詞彙的索引、列出字串查詢運算子，以及提供優化效能的秘訣。

## <a name="understanding-string-terms"></a>瞭解字串詞彙

Kusto 會編制所有資料行的索引，包括類型的資料行 `string` 。 針對這類資料行，會根據實際資料建立多個索引。 這些索引不會直接公開，但會在具有作為其名稱一部分之運算子的查詢中使用， `string` `has` 例如 `has` 、、 `!has` `hasprefix` 、 `!hasprefix` 。 這些運算子的語法是以資料行的編碼方式來決定。 這些運算子不會執行「純」子字串比對，而是與 *詞彙*相符。

### <a name="what-is-a-term"></a>什麼是詞彙？ 

根據預設，每個 `string` 值會分成 ASCII 英數位元的最大序列，且每個順序都會變成詞彙。
例如，在下列內容中， `string` 詞彙是 `Kusto` 、 `WilliamGates3rd` 和下列子字串： `ad67d136` 、 `c1db` 、 `4f9f` 、 `88ef` 、 `d94f3b6b0b5a` 。

```
Kusto:  ad67d136-c1db-4f9f-88ef-d94f3b6b0b5a;;WilliamGates3rd
```

Kusto 會建立一個詞彙索引，其中包含 *四個字元*以上的所有詞彙，而且此索引會由 `has` 、 `!has` 等等使用。 如果查詢尋找的詞彙小於四個字元，或使用 `contains` 運算子，則 Kusto 將會還原為在資料行中的值無法判斷相符的情況下進行掃描。 這個方法比查詢詞彙索引中的詞彙慢很多。

## <a name="operators-on-strings"></a>字串上的運算子

> [!NOTE]
> 下表使用下列縮寫：
> * RHS = 運算式的右手邊
> * LHS = 運算式的左側

運算子        |描述                                                       |區分大小寫|範例 (結果為 `true`)
----------------|------------------------------------------------------------------|--------------|-----------------------
`==`            |等於                                                            |是           |`"aBc" == "aBc"`
`!=`            |不等於                                                        |是           |`"abc" != "ABC"`
`=~`            |等於                                                            |否            |`"abc" =~ "ABC"`
`!~`            |不等於                                                        |否            |`"aBc" !~ "xyz"`
`has`           |右側 (RHS) 是左側 (LHS) 中的完整詞彙     |否            |`"North America" has "america"`
`!has`          |RHS 不是 LHS 中的完整詞彙                                     |否            |`"North America" !has "amer"` 
`has_cs`        |RHS 是 LHS 中的完整詞彙                                        |是           |`"North America" has_cs "America"`
`!has_cs`       |RHS 不是 LHS 中的完整詞彙                                     |是           |`"North America" !has_cs "amer"` 
`hasprefix`     |RHS 是 LHS 中的詞彙前置詞                                       |否            |`"North America" hasprefix "ame"`
`!hasprefix`    |RHS 不是 LHS 中的詞彙前置詞                                   |否            |`"North America" !hasprefix "mer"` 
`hasprefix_cs`  |RHS 是 LHS 中的詞彙前置詞                                       |是           |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs` |RHS 不是 LHS 中的詞彙前置詞                                   |是           |`"North America" !hasprefix_cs "CA"` 
`hassuffix`     |RHS 是 LHS 中的字詞尾碼                                       |否            |`"North America" hassuffix "ica"`
`!hassuffix`    |RHS 不是 LHS 中的字詞尾碼                                   |否            |`"North America" !hassuffix "americ"`
`hassuffix_cs`  |RHS 是 LHS 中的字詞尾碼                                       |是           |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs` |RHS 不是 LHS 中的字詞尾碼                                   |是           |`"North America" !hassuffix_cs "icA"`
`contains`      |RHS 作為 LHS 的子序列發生                                |否            |`"FabriKam" contains "BRik"`
`!contains`     |RHS 未在 LHS 中發生                                         |否            |`"Fabrikam" !contains "xyz"`
`contains_cs`   |RHS 作為 LHS 的子序列發生                                |是           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |RHS 未在 LHS 中發生                                         |是           |`"Fabrikam" !contains_cs "Kam"`
`startswith`    |RHS 是 LHS 的起始子序列                              |否            |`"Fabrikam" startswith "fab"`
`!startswith`   |RHS 不是 LHS 的起始子序列                          |否            |`"Fabrikam" !startswith "kam"`
`startswith_cs` |RHS 是 LHS 的起始子序列                              |是           |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`|RHS 不是 LHS 的起始子序列                          |是           |`"Fabrikam" !startswith_cs "fab"`
`endswith`      |RHS 是 LHS 的右子序列                               |否            |`"Fabrikam" endswith "Kam"`
`!endswith`     |RHS 不是 LHS 的右子序列                           |否            |`"Fabrikam" !endswith "brik"`
`endswith_cs`   |RHS 是 LHS 的右子序列                               |是           |`"Fabrikam" endswith "Kam"`
`!endswith_cs`  |RHS 不是 LHS 的右子序列                           |是           |`"Fabrikam" !endswith "brik"`
`matches regex` |LHS 包含 RHS 的相符項目                                      |是           |`"Fabrikam" matches regex "b.*k"`
`in`            |等於其中一個元素                                     |是           |`"abc" in ("123", "345", "abc")`
`!in`           |不等於任何元素                                 |是           |`"bca" !in ("123", "345", "abc")`
`in~`           |等於其中一個元素                                     |否            |`"abc" in~ ("123", "345", "ABC")`
`!in~`          |不等於任何元素                                 |否            |`"bca" !in~ ("123", "345", "ABC")`
`has_any`       |與相同， `has` 但適用于任何元素                    |否            |`"North America" has_any("south", "north")`

> [!TIP]
> 所有運算子都包含 `has` 搜尋四個或更多字元的索引 *字詞* ，而不是子字串相符專案。 建立詞彙的方法是將字串分解成 ASCII 英數位元的序列。 請參閱 [瞭解字串字詞](#understanding-string-terms)。

## <a name="performance-tips"></a>效能秘訣

為了獲得更好的效能，當有兩個運算子執行相同的工作時，請使用區分大小寫的。
例如：

* 請改用 `=~``==`
* 請改用 `in~``in`
* 請改用 `contains``contains_cs`

為了更快得到結果，如果您要測試的是以非英數位元或欄位開頭或結尾所系結的符號或英數位元字組是否存在，請使用 `has` 或 `in` 。 
`has` 的運作速度比 `contains` 、或更快 `startswith` `endswith` 。

例如，這些查詢的第一個執行速度會更快：

```kusto
EventLog | where continent has "North" | count;
EventLog | where continent contains "nor" | count
```
