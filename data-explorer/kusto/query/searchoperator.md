---
title: 搜尋運算子-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的搜尋運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: edd35e5e259666e8ce4360c072aaac6717e6f8c3
ms.sourcegitcommit: f9d3f54114fb8fab5c487b6aea9230260b85c41d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "85071874"
---
# <a name="search-operator"></a>search 運算子

搜尋運算子提供多資料表/多欄搜尋體驗。

## <a name="syntax"></a>語法

* [*TabularSource* `|` ]`search`[ `kind=` *CaseSensitivity*] [ `in` `(` *TableSources* `)` ] *SearchPredicate*

## <a name="arguments"></a>引數

* *TabularSource*：選擇性的表格式運算式，做為要搜尋的資料來源，例如資料表名稱、聯[集運算子](unionoperator.md)、表格式查詢的結果等等。不能與包含*TableSources*的選擇性片語一起出現。

* *CaseSensitivity*：選擇性的旗標，可控制所有純量 `string` 運算子與區分大小寫相關的行為。 有效值為兩個同義字 `default` 和 `case_insensitive` （這是運算子的預設值，也就是不 `has` 區分大小寫）和 `case_sensitive` （這會強制所有這類運算子都符合區分大小寫的比對模式）。

* *TableSources*：以逗號分隔的選擇性清單，其中包含要在搜尋中參與的「萬用字元」資料表名稱。
  此清單與聯[集運算子](unionoperator.md)的清單具有相同的語法。
  不能與選擇性的*TabularSource*一起出現。

* *SearchPredicate*：必要的述詞，定義要搜尋的內容（換句話說，是針對輸入中每一筆記錄評估的布林運算式，如果傳回 `true` ，則會輸出記錄）。*SearchPredicate*的語法會擴充及修改布林運算式的一般 Kusto 語法：

  **字串符合延伸**模組：在*SearchPredicate*中顯示為詞彙的字串常值，會使用 `has` 、 `hasprefix` 、 `hassuffix` 和這些運算子的反向（ `!` ）或區分大小寫（ `sc` ）版本，來表示所有資料行與常值之間的字詞相符。 決定要套用、或，取決於常值是以 `has` `hasprefix` `hassuffix` 星號（）開頭或結尾（或兩者） `*` 。 不允許常值內的星號。

    |常值   |運算子   |
    |----------|-----------|
    |`billg`   |`has`      |
    |`*billg`  |`hassuffix`|
    |`billg*`  |`hasprefix`|
    |`*billg*` |`contains` |
    |`bi*lg`   |`matches regex`|

  資料**行限制**：根據預設，字串相符的延伸會嘗試比對資料集的所有資料行。 您可以使用下列語法，將此比對限制為特定資料行： *ColumnName* `:` *StringLiteral*。

  **字串相等**：可以使用 StringLiteral 語法*ColumnName*來完成資料行與字串值的完全相符（而不是符合字詞） `==` * *。

  **其他布林運算式**：語法支援所有一般的 Kusto 布林運算式。
    例如， `"error" and x==123` 表示：搜尋其任何資料行中具有詞彙的記錄 `error` ，並在資料行中具有值 `123` `x` 。

  **Regex match**：使用*Column* StringLiteral 語法表示正則運算式比 `matches regex` *StringLiteral*對，其中*StringLiteral*是 Regex 模式。

請注意，如果省略*TabularSource*和*TableSources* ，則會在範圍內的資料庫所有不受限制的資料表和視圖上執行搜尋。

## <a name="summary-of-string-matching-extensions"></a>字串符合延伸模組的摘要

  |# |語法                                 |意義（對等 `where` ）           |註解|
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

**不同**于[find 運算子](findoperator.md)， `search` 運算子不支援下列各項：

1. `withsource=`：輸出一律會包含類型的資料行， `$table` `string` 其值為從中抓取每一筆記錄的資料表名稱（如果來源不是資料表，則為某些系統產生的名稱）。
2. `project=`、 `project-smart` ：輸出架構相當於 `project-smart` 輸出架構。

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

## <a name="performance-tips"></a>效能提示

  |# |提示                                                                                  |偏好                                        |超過                                                                    |
  |--|-------------------------------------------------------------------------------------|----------------------------------------------|------------------------------------------------------------------------|
  | 1| 偏好在 `search` 數個連續運算子上使用單一運算子 `search`|`search "billg" and ("steveb" or "satyan")`   |<code>search "billg" &#124; search "steveb" or "satyan"<code>           ||
  | 2| 偏好在運算子內進行篩選 `search`                                       |`search "billg" and "steveb"`                 |<code>search * &#124; where * has "billg" and * has "steveb"<code>      ||
