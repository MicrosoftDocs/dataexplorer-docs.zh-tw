---
title: 搜尋運算子-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的搜尋運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 24e79b7feeb51a0626ed270a90c3d323fa94cbf3
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250273"
---
# <a name="search-operator"></a>search 運算子

搜尋運算子提供多資料表/多欄搜尋體驗。

## <a name="syntax"></a>語法

* [*TabularSource* `|` ]`search`[ `kind=` *CaseSensitivity*] [ `in` `(` *TableSources* `)` ] *SearchPredicate*

## <a name="arguments"></a>引數

* *TabularSource*：選擇性的表格式運算式，作為要搜尋的資料來源，例如資料表名稱、等位 [運算子](unionoperator.md)、表格式查詢的結果等等。不可與包含 *TableSources*的選擇性片語一起出現。

* *CaseSensitivity*：選擇性旗標，可控制所有純量運算子的行為（ `string` 區分大小寫）。 有效的值是兩個同義字 `default` 和 `case_insensitive` (，這是運算子的預設值，也就是不區分 `has` 大小寫的) 和 `case_sensitive` (，它會強制所有這類運算子符合區分大小寫的比對模式) 。

* *TableSources*：選擇性的逗號分隔清單，其中包含要參與搜尋的 "萬用字元" 資料表名稱。
  此清單具有與 [union 運算子](unionoperator.md)清單相同的語法。
  無法與選擇性的 *TabularSource*一起出現。

* *SearchPredicate*：定義要在其中搜尋 (的強制述詞，也就是針對輸入中的每一筆記錄評估的布林運算式，如果傳回 `true` ，則會輸出記錄。 ) 的語法可延伸和修改*SearchPredicate*布林運算式的一般 Kusto 語法：

  **字串**比對延伸：在 *SearchPredicate* 中顯示為詞彙的字串常值，會使用 `has` 、、以及 `hasprefix` `hassuffix` 反向 (`!`) 或區分大小寫 `sc` 的 () 版本的運算子，來表示所有資料行與常值之間的詞彙相符。 要套用、或是否要套用 `has` 、 `hasprefix` 或 `hassuffix` 取決於常值開始或結束 (，還是) 星號 (`*`) 。 不允許常值中的星號。

    |常值   |運算子   |
    |----------|-----------|
    |`billg`   |`has`      |
    |`*billg`  |`hassuffix`|
    |`billg*`  |`hasprefix`|
    |`*billg*` |`contains` |
    |`bi*lg`   |`matches regex`|

  資料**行限制**：根據預設，字串相符的副檔名會嘗試比對資料集的所有資料行。 您可以使用下列語法，將此比對限制為特定資料行： *ColumnName* `:` *StringLiteral*。

  **字串相等**：符合字串值的資料行完全相符 (而不是符合詞彙的) 可以使用 StringLiteral 語法 ColumnName 來完成*ColumnName* `==` * *。

  **其他布林運算式**：語法支援所有一般 Kusto 布林運算式。
    例如， `"error" and x==123` 表示搜尋具有 `error` 任何資料行中之詞彙的記錄，並在資料 `123` 行中包含值 `x` 。

  **Regex 相符**：正則運算式比對會使用資料 *行* `matches regex` *StringLiteral* 語法來表示，其中 *StringLiteral* 是 Regex 模式。

請注意，如果省略 *TabularSource* 和 *TableSources* ，則會對範圍中資料庫的所有不受限制的資料表和資料檢視執行搜尋。

## <a name="summary-of-string-matching-extensions"></a>字串相符擴充功能的摘要

  |# |語法                                 |表示 (相等的 `where`)            |註解|
  |--|---------------------------------------|---------------------------------------|--------|
  | 1|`search "err"`                         |`where * has "err"`                    ||
  | 2|`search in (T1,T2,A*) and "err"`       |<code>union T1,T2,A* &#124; where * has "err"<code>   ||
  | 3|`search col:"err"`                     |`where col has "err"`                  ||
  | 4|`search col=="err"`                    |`where col=="err"`                     ||
  | 5|`search "err*"`                        |`where * hasprefix "err"`              ||
  | 6|`search "*err"`                        |`where * hassuffix "err"`              ||
  | 7|`search "*err*"`                       |`where * contains "err"`               ||
  | 8|`search "Lab*PC"`                      |`where * matches regex @"\bLab.*PC\b"`||
  | 9|`search *`                             |`where 0==0`                           ||
  |10|`search col matches regex "..."`       |`where col matches regex "..."`        ||
  |11|`search kind=case_sensitive`           |                                       |所有字串比較都會區分大小寫|
  |12|`search "abc" and ("def" or "hij")`    |`where * has "abc" and (* has "def" or * has hij")`||
  |13|`search "err" or (A>a and A<b)`        |`where * has "err" or (A>a and A<b)`   ||

## <a name="remarks"></a>備註

**不同** 于 [find 運算子](findoperator.md)， `search` 運算子不支援下列各項：

1. `withsource=`：輸出一律會包含名為的資料行，其中的 `$table` `string` 值是從中抓取每一筆記錄的資料表名稱 (或某些系統產生的名稱（如果來源不是資料表，但複合運算式) ）。
2. `project=``project-smart`：輸出架構相當於 `project-smart` 輸出架構。

## <a name="examples"></a>範例

```kusto
// 1. Simple term search over all unrestricted tables and views of the database in scope
search "billg"

// 2. Like (1), but looking only for records that match both terms
search "billg" and ("steveb" or "satyan")

// 3. Like (1), but looking only in the TraceEvent table
search in (TraceEvent) and "billg"

// 4. Like (2), but performing a case-sensitive match of all terms
search "BillB" and ("SteveB" or "SatyaN")

// 5. Like (1), but restricting the match to some columns
search CEO:"billg" or CSA:"billg"

// 6. Like (1), but only for some specific time limit
search "billg" and Timestamp >= datetime(1981-01-01)

// 7. Searches over all the higher-ups
search in (C*, TF) "billg" or "davec" or "steveb"

// 8. A different way to say (7). Prefer to use (7) when possible
union C*, TF | search "billg" or "davec" or "steveb"
```

## <a name="performance-tips"></a>效能祕訣

  |# |提示                                                                                  |偏好                                        |超過                                                                    |
  |--|-------------------------------------------------------------------------------------|----------------------------------------------|------------------------------------------------------------------------|
  | 1| 偏好在 `search` 數個連續運算子上使用單一運算子 `search`|`search "billg" and ("steveb" or "satyan")`   |<code>search "billg" &#124; search "steveb" or "satyan"<code>           ||
  | 2| 偏好在運算子內篩選 `search`                                       |`search "billg" and "steveb"`                 |<code>search * &#124; where * has "billg" and * has "steveb"<code>      ||
