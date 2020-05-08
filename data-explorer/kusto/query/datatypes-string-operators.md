---
title: 字串運算子-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的字串運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e16d90c8307392536b5971758ce7df15a9ea1b46
ms.sourcegitcommit: ef009294b386cba909aa56d7bd2275a3e971322f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2020
ms.locfileid: "82977128"
---
# <a name="string-operators"></a>字串運算子

下表摘要說明字串上的運算子：

運算子        |描述                                                       |區分大小寫|範例 (結果為 `true`)
----------------|------------------------------------------------------------------|--------------|-----------------------
`==`            |Equals                                                            |是           |`"aBc" == "aBc"`
`!=`            |不等於                                                        |是           |`"abc" != "ABC"`
`=~`            |Equals                                                            |否            |`"abc" =~ "ABC"`
`!~`            |不等於                                                        |否            |`"aBc" !~ "xyz"`
`has`           |右側 (RHS) 是左側 (LHS) 中的完整詞彙     |否            |`"North America" has "america"`
`!has`          |RHS 不是 LHS 中的完整詞彙                                     |否            |`"North America" !has "amer"` 
`has_cs`        |右側 (RHS) 是左側 (LHS) 中的完整詞彙     |是           |`"North America" has_cs "America"`
`!has_cs`       |RHS 不是 LHS 中的完整詞彙                                     |是           |`"North America" !has_cs "amer"` 
`hasprefix`     |RHS 是 LHS 中的詞彙前置詞                                       |否            |`"North America" hasprefix "ame"`
`!hasprefix`    |RHS 不是 LHS 中的詞彙前置詞                                   |否            |`"North America" !hasprefix "mer"` 
`hasprefix_cs`  |RHS 是 LHS 中的詞彙前置詞                                       |是           |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs` |RHS 不是 LHS 中的詞彙前置詞                                   |是           |`"North America" !hasprefix_cs "CA"` 
`hassuffix`     |RHS 是 LHS 中的字詞尾碼                                       |否            |`"North America" hassuffix "ica"`
`!hassuffix`    |RHS 不是 LHS 中的字詞尾碼                                   |否            |`"North America" !hassuffix "americ"`
`hassuffix_cs`  |RHS 是 LHS 中的字詞尾碼                                       |是           |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs` |RHS 不是 LHS 中的字詞尾碼                                   |是           |`"North America" !hassuffix_cs "icA"`
`contains`      |RHS 是 LHS 的子序列                                |否            |`"FabriKam" contains "BRik"`
`!contains`     |RHS 未出現在 LHS                                         |否            |`"Fabrikam" !contains "xyz"`
`contains_cs`   |RHS 是 LHS 的子序列                                |是           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |RHS 未出現在 LHS                                         |是           |`"Fabrikam" !contains_cs "Kam"`
`startswith`    |RHS 是 LHS 的起始子序列                              |否            |`"Fabrikam" startswith "fab"`
`!startswith`   |RHS 不是 LHS 的初始子序列                          |否            |`"Fabrikam" !startswith "kam"`
`startswith_cs` |RHS 是 LHS 的起始子序列                              |是           |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`|RHS 不是 LHS 的初始子序列                          |是           |`"Fabrikam" !startswith_cs "fab"`
`endswith`      |RHS 是 LHS 的右子序列                               |否            |`"Fabrikam" endswith "Kam"`
`!endswith`     |RHS 不是 LHS 的關閉子序列                           |否            |`"Fabrikam" !endswith "brik"`
`endswith_cs`   |RHS 是 LHS 的右子序列                               |是           |`"Fabrikam" endswith "Kam"`
`!endswith_cs`  |RHS 不是 LHS 的關閉子序列                           |是           |`"Fabrikam" !endswith "brik"`
`matches regex` |LHS 包含 RHS 的相符項目                                      |是           |`"Fabrikam" matches regex "b.*k"`
`in`            |等於其中一個元素                                     |是           |`"abc" in ("123", "345", "abc")`
`!in`           |不等於任何元素                                 |是           |`"bca" !in ("123", "345", "abc")`
`in~`           |等於其中一個元素                                     |否            |`"abc" in~ ("123", "345", "ABC")`
`!in~`          |不等於任何元素                                 |否            |`"bca" !in~ ("123", "345", "ABC")`
`has_any`       |與相同`has` ，但適用于任何元素                    |否            |`"North America" has_any("south", "north")`

## <a name="performance-tips"></a>效能秘訣

為獲得較佳的效能，當有兩個運算子執行相同的工作時，請使用區分大小寫的。
例如：

* `=~`而不是使用`==`
* `in~`而不是使用`in`
* `contains`而不是使用`contains_cs`

若要加快結果速度，如果您要測試的符號或英數位元是否與非英數位元（或欄位的開頭或結尾）系結，請使用`has`或。 `in` `has`執行速度比`contains`、 `startswith`或`endswith`更快。

例如，這些查詢的第一個執行速度會更快：

```kusto
EventLog | where continent has "North" | count;
EventLog | where continent contains "nor" | count
```

## <a name="understanding-string-terms"></a>瞭解字串詞彙

根據預設，Kusto 會為所有資料行編制索引， `string`包括類型的資料行。
事實上，這類資料行的多個索引是根據實際的資料而建立的。 對使用者而言，這些索引不會直接公開（而不是其對查詢效能的正面影響），唯一的例外是`string`其名稱中`has`包含的運算子： `has`、 `!has`、 `hasprefix`、 `!hasprefix`等。這些運算子的特殊之處在于其語義是由資料行的編碼方式所決定;這些運算子會比對**詞彙**，而不是執行「純」子字串相符。

若要瞭解以詞彙為基礎的相符，首先必須瞭解什麼是詞彙。 根據預設，Kusto 會將`string`每個值分解成 ASCII 英數位元的最大序列，而每一個都是在一個詞彙中進行。 例如，在`string`下列中，這些詞彙是`Kusto`、 `WilliamGates3rd`和下列子字串： `ad67d136`、 `c1db`、 `4f9f`、 `88ef`、： `d94f3b6b0b5a`

```
Kusto:  ad67d136-c1db-4f9f-88ef-d94f3b6b0b5a;;WilliamGates3rd
```

根據預設，Kusto 會建立一個詞彙索引，其中包含**四個字元或更多**的所有詞彙，而且當查閱`has`的`!has`字詞也是四個字元或更多時，會使用此索引。 （如果查詢尋找的字詞小於四個字元，或使用`contains`運算子，例如，如果無法判斷相符項，則 Kusto 會還原為掃描資料行中的值，這比在詞彙索引中查看詞彙慢很多）。

