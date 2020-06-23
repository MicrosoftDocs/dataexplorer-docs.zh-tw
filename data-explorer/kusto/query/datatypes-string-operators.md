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
ms.openlocfilehash: 6718ad614166d5328dcd412d09405c1db8cc14dc
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2020
ms.locfileid: "85265077"
---
# <a name="string-operators"></a>字串運算子

下表摘要說明字串上的運算子：

運算子        |描述                                                       |區分大小寫|範例 (結果為 `true`)
----------------|------------------------------------------------------------------|--------------|-----------------------
`==`            |Equals                                                            |Yes           |`"aBc" == "aBc"`
`!=`            |不等於                                                        |Yes           |`"abc" != "ABC"`
`=~`            |Equals                                                            |No            |`"abc" =~ "ABC"`
`!~`            |不等於                                                        |No            |`"aBc" !~ "xyz"`
`has`           |右側 (RHS) 是左側 (LHS) 中的完整詞彙     |No            |`"North America" has "america"`
`!has`          |RHS 不是 LHS 中的完整詞彙                                     |No            |`"North America" !has "amer"` 
`has_cs`        |右側 (RHS) 是左側 (LHS) 中的完整詞彙     |Yes           |`"North America" has_cs "America"`
`!has_cs`       |RHS 不是 LHS 中的完整詞彙                                     |Yes           |`"North America" !has_cs "amer"` 
`hasprefix`     |RHS 是 LHS 中的詞彙前置詞                                       |No            |`"North America" hasprefix "ame"`
`!hasprefix`    |RHS 不是 LHS 中的詞彙前置詞                                   |No            |`"North America" !hasprefix "mer"` 
`hasprefix_cs`  |RHS 是 LHS 中的詞彙前置詞                                       |Yes           |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs` |RHS 不是 LHS 中的詞彙前置詞                                   |Yes           |`"North America" !hasprefix_cs "CA"` 
`hassuffix`     |RHS 是 LHS 中的字詞尾碼                                       |No            |`"North America" hassuffix "ica"`
`!hassuffix`    |RHS 不是 LHS 中的字詞尾碼                                   |No            |`"North America" !hassuffix "americ"`
`hassuffix_cs`  |RHS 是 LHS 中的字詞尾碼                                       |Yes           |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs` |RHS 不是 LHS 中的字詞尾碼                                   |Yes           |`"North America" !hassuffix_cs "icA"`
`contains`      |RHS 作為 LHS 的子序列發生                                |No            |`"FabriKam" contains "BRik"`
`!contains`     |RHS 未在 LHS 中發生                                         |No            |`"Fabrikam" !contains "xyz"`
`contains_cs`   |RHS 作為 LHS 的子序列發生                                |Yes           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |RHS 未在 LHS 中發生                                         |Yes           |`"Fabrikam" !contains_cs "Kam"`
`startswith`    |RHS 是 LHS 的起始子序列                              |No            |`"Fabrikam" startswith "fab"`
`!startswith`   |RHS 不是 LHS 的起始子序列                          |No            |`"Fabrikam" !startswith "kam"`
`startswith_cs` |RHS 是 LHS 的起始子序列                              |Yes           |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`|RHS 不是 LHS 的起始子序列                          |Yes           |`"Fabrikam" !startswith_cs "fab"`
`endswith`      |RHS 是 LHS 的右子序列                               |No            |`"Fabrikam" endswith "Kam"`
`!endswith`     |RHS 不是 LHS 的右子序列                           |No            |`"Fabrikam" !endswith "brik"`
`endswith_cs`   |RHS 是 LHS 的右子序列                               |Yes           |`"Fabrikam" endswith "Kam"`
`!endswith_cs`  |RHS 不是 LHS 的右子序列                           |Yes           |`"Fabrikam" !endswith "brik"`
`matches regex` |LHS 包含 RHS 的相符項目                                      |Yes           |`"Fabrikam" matches regex "b.*k"`
`in`            |等於其中一個元素                                     |Yes           |`"abc" in ("123", "345", "abc")`
`!in`           |不等於任何元素                                 |Yes           |`"bca" !in ("123", "345", "abc")`
`in~`           |等於其中一個元素                                     |No            |`"abc" in~ ("123", "345", "ABC")`
`!in~`          |不等於任何元素                                 |No            |`"bca" !in~ ("123", "345", "ABC")`
`has_any`       |與相同， `has` 但適用于任何元素                    |No            |`"North America" has_any("south", "north")`

## <a name="performance-tips"></a>效能秘訣

為獲得較佳的效能，當有兩個運算子執行相同的工作時，請使用區分大小寫的。
例如：

* 而不是 `=~` 使用`==`
* 而不是 `in~` 使用`in`
* 而不是 `contains` 使用`contains_cs`

為加快結果，如果您要測試的是不是以非英數位元或欄位的開頭或結尾所系結的符號或英數位元的存在，請使用 `has` 或 `in` 。 
`has`的運作速度比 `contains` 、或更快 `startswith` `endswith` 。

例如，這些查詢的第一個執行速度會更快：

```kusto
EventLog | where continent has "North" | count;
EventLog | where continent contains "nor" | count
```

## <a name="understanding-string-terms"></a>瞭解字串詞彙

根據預設，Kusto 會為所有資料行編制索引，包括類型的資料行 `string` 。
視實際資料而定，會針對這類資料行建立多個索引。 這些索引不會直接公開（除了其對查詢效能的正面影響以外），但 `string` 包含在 `has` 其名稱中的運算子（例如 `has` 、 `!has` 、 `hasprefix` 、 `!hasprefix` ）除外。
這些運算子是特殊的，因為它們的語義是由資料行的編碼方式所決定。 這些運算子會比對**詞彙**，而不是執行「純」子字串相符。

若要瞭解以詞彙為基礎的相符，您必須先瞭解什麼是詞彙。 根據預設，每個 `string` 值會細分成 ASCII 英數位元的最大序列，而每個序列都會變成一個詞彙。

例如，在下列中， `string` 這些詞彙是 `Kusto` 、 `WilliamGates3rd` 和下列子字串： `ad67d136` 、 `c1db` 、 `4f9f` 、 `88ef` 、 `d94f3b6b0b5a` 。

```
Kusto:  ad67d136-c1db-4f9f-88ef-d94f3b6b0b5a;;WilliamGates3rd
```

根據預設，Kusto 會建立一個詞彙索引，其中包含**四個字元或更多**的所有詞彙，而且 `has` `!has` 當查閱的字詞也是四個字元或更多時，會使用這個索引，依此類推。
如果查詢尋找的字詞小於四個字元，或使用 `contains` 運算子，則 Kusto 會在無法判斷相符的情況下，還原成掃描資料行中的值。 這個方法比查閱詞彙索引中的詞彙慢很多。
