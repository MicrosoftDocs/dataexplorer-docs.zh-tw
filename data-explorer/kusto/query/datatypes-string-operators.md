---
title: 字串運算子 - Azure 資料總管
description: 本文說明 Azure 資料總管中的字串運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/19/2020
ms.localizationpriority: high
ms.openlocfilehash: 9e8197d3af25da0b0e2488b4a5c70f5cfa2dde17
ms.sourcegitcommit: abbcb27396c6d903b608e7b19edee9e7517877bb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/15/2021
ms.locfileid: "100528066"
---
# <a name="string-operators"></a>字串運算子

Kusto 提供各種查詢運算子供您搜尋字串資料類型。 下列文章會說明字串字詞編製索引的方式、列出字串查詢運算子，並提供用於將效能最佳化的秘訣。

## <a name="understanding-string-terms"></a>了解字串字詞

Kusto 會為所有資料行編製索引，包括 `string` 類型的資料行在內。 視實際資料而定，這類資料行會建置多個索引。 這些索引不會直接公開，而是用於具有 `string` 運算子，並以 `has` 作為其名稱一部分的查詢中，例如 `has`、`!has`、`hasprefix`、`!hasprefix`。 這些運算子的語義會由資料行的編碼方式來加以規定。 這些運算子會比對 *字詞*，而不會執行「純文字」子字串比對。

### <a name="what-is-a-term"></a>何謂字詞？ 

根據預設，每個 `string` 值都會細分成 ASCII 英數字元的最大數量，而每個數量都會成為一個字詞。
例如，在下列 `string` 中，這些字詞是 `Kusto`、`KustoExplorerQueryRun` 和下列子字串：`ad67d136`、`c1db`、`4f9f`、`88ef`、`d94f3b6b0b5a`。

```
Kusto: ad67d136-c1db-4f9f-88ef-d94f3b6b0b5a;KustoExplorerQueryRun
```

Kusto 會建立一個詞彙索引，其中包含 *三個字元以上* 的所有字詞，而此索引則由字串運算子（例如 `has` 、 `!has` 等）使用。  如果查詢尋找的詞彙小於三個字元，或使用 `contains` 運算子，則查詢會還原為掃描資料行中的值。 掃描速度比查詢詞彙索引中的詞彙慢很多。

> [!NOTE]
> 在 EngineV2 中，詞彙是由四個或更多個字元所組成。

## <a name="operators-on-strings"></a>字串上的運算子

> [!NOTE]
> 下表會使用下列縮寫：
> * RHS = 運算式的右手邊
> * LHS = 運算式的左手邊
> 
> 具有 `_cs` 尾碼的運算子會區分大小寫。

> [!NOTE]
> 目前只有 ASCII 文字支援不區分大小寫的運算子。 若為非 ASCII 比較，請使用 [tolower ()](tolowerfunction.md) 函數。

運算子        |描述                                                       |區分大小寫|範例 (結果為 `true`)
----------------|------------------------------------------------------------------|--------------|-----------------------
`==`            |等於                                                            |是           |`"aBc" == "aBc"`
`!=`            |不等於                                                        |是           |`"abc" != "ABC"`
`=~`            |等於                                                            |否            |`"abc" =~ "ABC"`
`!~`            |不等於                                                        |否            |`"aBc" !~ "xyz"`
`has`           |右側 (RHS) 是左側 (LHS) 中的完整詞彙     |否            |`"North America" has "america"`
`!has`          |RHS 不是 LHS 中的完整字詞                                     |否            |`"North America" !has "amer"` 
[`has_all`](has-all-operator.md)       |與相同， `has` 但適用于所有元素                    |否            |`"North and South America" has_all("south", "north")`
[`has_any`](has-anyoperator.md)       |與 `has` 相同，但適用於任何元素                    |否            |`"North America" has_any("south", "north")`
`has_cs`        |RHS 是 LHS 中的完整字詞                                        |是           |`"North America" has_cs "America"`
`!has_cs`       |RHS 不是 LHS 中的完整字詞                                     |是           |`"North America" !has_cs "amer"` 
`hasprefix`     |RHS 是 LHS 中的字詞前置詞                                       |否            |`"North America" hasprefix "ame"`
`!hasprefix`    |RHS 不是 LHS 中的字詞前置詞                                   |否            |`"North America" !hasprefix "mer"` 
`hasprefix_cs`  |RHS 是 LHS 中的字詞前置詞                                       |是           |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs` |RHS 不是 LHS 中的字詞前置詞                                   |是           |`"North America" !hasprefix_cs "CA"` 
`hassuffix`     |RHS 是 LHS 中的字詞後置詞                                       |否            |`"North America" hassuffix "ica"`
`!hassuffix`    |RHS 不是 LHS 中的字詞後置詞                                   |否            |`"North America" !hassuffix "americ"`
`hassuffix_cs`  |RHS 是 LHS 中的字詞後置詞                                       |是           |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs` |RHS 不是 LHS 中的字詞後置詞                                   |是           |`"North America" !hassuffix_cs "icA"`
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
`endswith_cs`   |RHS 是 LHS 的右子序列                               |是           |`"Fabrikam" endswith_cs "kam"`
`!endswith_cs`  |RHS 不是 LHS 的右子序列                           |是           |`"Fabrikam" !endswith_cs "brik"`
`matches regex` |LHS 包含 RHS 的相符項目                                      |是           |`"Fabrikam" matches regex "b.*k"`
[`in`](inoperator.md)            |等於其中一個元素                                     |是           |`"abc" in ("123", "345", "abc")`
[`!in`](inoperator.md)           |不等於任何元素                                 |是           |`"bca" !in ("123", "345", "abc")`
`in~`           |等於其中一個元素                                     |否            |`"abc" in~ ("123", "345", "ABC")`
`!in~`          |不等於任何元素                                 |否            |`"bca" !in~ ("123", "345", "ABC")`


> [!TIP]
> 包含 `has` 在內的所有運算子都會搜尋四個以上字元的已編製索引 *字詞*，而不會搜尋子字串相符項目。 將字串細分成 ASCII 英數字元的數列，就會建立一個字詞。 請參閱[了解字串字詞](#understanding-string-terms)。

## <a name="performance-tips"></a>效能秘訣

為獲得較佳效能，當有兩個運算子可執行相同的工作時，請使用會區分大小寫的運算子。
例如：

* 不要使用 `=~`，而是使用 `==`
* 不要使用 `in~`，而是使用 `in`
* 不要使用 `contains`，而是使用 `contains_cs`

為了更快獲得結果，如果您要測試由非英數字元所繫結的符號或英數字元字組是否存在，或要測試欄位的開頭或結尾，請使用 `has` 或 `in`。 
`has` 的運作速度快過 `contains`、`startswith` 或 `endswith`。

例如，這些查詢的第一個會執行得更快：

```kusto
EventLog | where continent has "North" | count;
EventLog | where continent contains "nor" | count
```
