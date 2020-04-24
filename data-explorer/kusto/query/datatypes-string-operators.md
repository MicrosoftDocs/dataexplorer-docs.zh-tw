---
title: 字串運算子 ─ Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的字串運算元。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 91955ef5877e6c054917f70c655fc4b7cd3f277b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516468"
---
# <a name="string-operators"></a>字串運算子

下表總結了字串上的運算元:

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
`hasprefix`     |RHS 是 LHS 中的術語前置字串                                       |否            |`"North America" hasprefix "ame"`
`!hasprefix`    |RHS 不是 LHS 中的術語前置字串                                   |否            |`"North America" !hasprefix "mer"` 
`hasprefix_cs`  |RHS 是 LHS 中的術語前置字串                                       |是           |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs` |RHS 不是 LHS 中的術語前置字串                                   |是           |`"North America" !hasprefix_cs "CA"` 
`hassuffix`     |RHS 是 LHS 中的術語後綴                                       |否            |`"North America" hassuffix "ica"`
`!hassuffix`    |RHS 不是 LHS 中的術語後綴                                   |否            |`"North America" !hassuffix "americ"`
`hassuffix_cs`  |RHS 是 LHS 中的術語後綴                                       |是           |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs` |RHS 不是 LHS 中的術語後綴                                   |是           |`"North America" !hassuffix_cs "icA"`
`contains`      |RHS 是 LHS 的子序列                                |否            |`"FabriKam" contains "BRik"`
`!contains`     |RHS 未出現在 LHS                                         |否            |`"Fabrikam" !contains "xyz"`
`contains_cs`   |RHS 是 LHS 的子序列                                |是           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |RHS 未出現在 LHS                                         |是           |`"Fabrikam" !contains_cs "Kam"`
`startswith`    |RHS 是 LHS 的起始子序列                              |否            |`"Fabrikam" startswith "fab"`
`!startswith`   |RHS 不是 LHS 初始子序列                          |否            |`"Fabrikam" !startswith "kam"`
`startswith_cs` |RHS 是 LHS 的起始子序列                              |是           |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`|RHS 不是 LHS 初始子序列                          |是           |`"Fabrikam" !startswith_cs "fab"`
`endswith`      |RHS 是 LHS 的右子序列                               |否            |`"Fabrikam" endswith "Kam"`
`!endswith`     |RHS 不是 LHS 關閉子序列                           |否            |`"Fabrikam" !endswith "brik"`
`endswith_cs`   |RHS 是 LHS 的右子序列                               |是           |`"Fabrikam" endswith "Kam"`
`!endswith_cs`  |RHS 不是 LHS 關閉子序列                           |是           |`"Fabrikam" !endswith "brik"`
`matches regex` |LHS 包含 RHS 的相符項目                                      |是           |`"Fabrikam" matches regex "b.*k"`
`in`            |等於其中一個元素                                     |是           |`"abc" in ("123", "345", "abc")`
`!in`           |不等於任何元素                                 |是           |`"bca" !in ("123", "345", "abc")`
`in~`           |等於其中一個元素                                     |否            |`"abc" in~ ("123", "345", "ABC")`
`!in~`          |不等於任何元素                                 |否            |`"bca" !in~ ("123", "345", "ABC")`
`has_any`       |與`has`適用於任何元素相同,但適用於任何元素                    |否            |`"North America" has_any("south", "north")`

## <a name="performance-tips"></a>效能秘訣

為了更好的性能,當有兩個運算符執行同一任務時,請使用區分大小寫的運算符。
例如：

* 而不是`=~`, 使用`==`
* 而不是`in~`, 使用`in`
* 而不是`contains`, 使用`contains_cs`

為了取得更快的結果,如果要測試符號或字母數字單字是否存在,該符號或字母數位單字由非字母數位字元(或欄位的開頭或結尾)綁定,請使用`has`或`in`。 `has`執行速度比`contains``startswith``endswith`或更快。

例如,第一個查詢運行得更快:

```kusto
EventLog | where continent has "North" | count;
EventLog | where continent contains "nor" | count
```

## <a name="understanding-string-terms"></a>瞭解字串術語

預設情況下,Kusto 對所有欄進行索引,包括`string`類型欄位 。
事實上,根據實際數據,為此類列生成多個索引。 對於用戶,這些索引不會直接公開(當然,除了它們對查詢性能的積極影響外),但名稱中作為`string``has`名稱`has``!has``hasprefix``!hasprefix`的一部分的運算符除外。這些運算元是特殊的,因為它們的語義由列的編碼方式決定;這些運算元與**術語**匹配,而不是執行"純"子字串匹配。

要理解基於術語的匹配,首先需要了解什麼是術語。 默認情況下,Kusto 將`string`每個值分解為 ASCII 字母數位字元的最大序列,並且每個值都轉換為術語。 `string`例如,在以下項目中,術語是`Kusto`,`WilliamGates3rd`與以下子字串: `ad67d136` `c1db` `4f9f`, `88ef` `d94f3b6b0b5a`、 、 、 、 、 、

```
Kusto:  ad67d136-c1db-4f9f-88ef-d94f3b6b0b5a;;WilliamGates3rd
```

默認情況下,Kusto 生成一個術語索引,其中包含所有四個字元或更多的術語,並且此索引在查找同樣為`has`四`!has`個字元 或以上的術語時由、等使用。 (如果查詢查找小於四個字元的術語,或者使用`contains`運算符,則 Kusto 將恢復為掃描列中的值(如果無法確定匹配項,則該匹配值比在術語索引中查找該術語要慢得多)。

