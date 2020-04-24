---
title: 搜尋運算子 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的搜索運算符。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: de63aa3fde421996809334b8a746ea4dee8cb026
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509192"
---
# <a name="search-operator"></a>search 運算子

搜索運算符提供多表/多列搜索體驗。

## <a name="syntax"></a>語法

* [*表格來源*`|`]`search` [`kind=`*區分大小寫*`in``(`] [*表源*`)`]*搜尋謂詞*

## <a name="arguments"></a>引數

* *TabularSource*:充當要搜索的數據源的可選表格運算式,如表名、[聯合運算元](unionoperator.md)、表格查詢的結果等。不能與包含*表源*的可選短語一起顯示。

* *區分大小寫*:控制`string`所有 標量運算符在區分大小寫方面的行為的可選標誌。 有效值是兩個同義`default`詞`case_insensitive`和 (對於運算符(`has`如的預設值,即不區分大小寫)`case_sensitive`和 (這迫使所有此類運算符進入區分大小寫的匹配模式)。

* *表源*:要參與搜索的「通配符」表名稱的可選逗號分隔清單。
  該清單的語法與[聯合運算符](unionoperator.md)的清單相同。
  不能與可選的*表格源*一起出現。

* *Search謂詞*:一個強制謂詞,用於定義要搜索的內容(換句話說,為輸入中的每個記錄計算的布爾表達式,如果`true`返回 ,則輸出記錄)。*Search謂詞*的語法延伸並修改了布林表達式的正常 Kusto 語法:

  **字串匹配擴展**:在*SearchPredicate*中顯示為術語的字串文字指示所有列與`has`使用的字`hasprefix`面`hassuffix`() 和這些運算元的倒`!`() 或區分`sc`大小寫 () 版本之間的術語匹配。 `has`是應用`hasprefix``hassuffix`或取決於文本是否以星號 () 開始`*`還是結束( 或兩者)來決定。 不允許文本中的星號。

    |常值   |運算子   |
    |----------|-----------|
    |`billg`   |`has`      |
    |`*billg`  |`hassuffix`|
    |`billg*`  |`hasprefix`|
    |`*billg*` |`contains` |
    |`bi*lg`   |`matches regex`|

  **列限制**:默認情況下,字串匹配擴展嘗試與數據集的所有列匹配。 可以使用以下語法會將此符合限制為特定欄*位: 欄位*`:`*名稱 StringLiteral*。

  **字串相等**性:可以使用語法*列名稱*`==`*StringLiteral*完成列與字串值的精確匹配(而不是術語匹配)。

  **其他布爾運算式**:語法支援所有常規 Kusto 布爾運算式。
    例如,`"error" and x==123`表示:搜索在其任何列中具有該`error`項 並在列中具有`123`該值`x`的記錄。

  **Regex 匹配**: 正則表示式匹配使用*欄*`matches regex`*位字串文字*語法指示,其中*StringLiteral*是正則運算式模式。

請注意,如果省略*了 TabularSource*和*TableSource,* 則搜索將承載範圍中資料庫的所有無限制表和視圖。

## <a name="summary-of-string-matching-extensions"></a>字串符合延伸的摘要

  |# |語法                                 |含義(等效`where`)           |註解|
  |--|---------------------------------------|---------------------------------------|--------|
  | 1|`search "err"`                         |`where * has "err"`                    ||
  | 2|`search in (T1,T2,A*) and "err"`       |<code>union T1,T2,A* &#124; where * has "err"<code>   ||
  | 3|`search col:"err"`                     |`where col has "err"`                  ||
  | 4|`search col=="err"`                    |`where col=="err"`                     ||
  | 5|`search "err*"`                        |`where * hasprefix "err"`              ||
  | 6|`search "*err"`                        |`where * hassuffix "err"`              ||
  | 7|`search "*err*"`                       |`where * contains "err"`               ||
  | 8|`search "Lab*PC"`                      |`where * matches regex @"\bLab\w*PC\b"`||
  | 9|`search *`                             |`where 0==0`                           ||
  |10|`search col matches regex "..."`       |`where col matches regex "..."`        ||
  |11|`search kind=case_sensitive`           |                                       |所有字串比較都區分大小寫|
  |12|`search "abc" and ("def" or "hij")`    |`where * has "abc" and (* has "def" or * has hij")`||
  |13|`search "err" or (A>a and A<b)`        |`where * has "err" or (A>a and A<b)`   ||

## <a name="remarks"></a>備註

**與** [find 運算子](findoperator.md)不同`search`,運算元不支援以下內容:

1. `withsource=`:輸出將始終包含一個稱為`$table`類型`string`的列,其值是檢索每個記錄的表名稱(或者如果源不是表而是復合表達式,則某些系統生成的名稱)。
2. `project=``project-smart`:輸出架構等效於`project-smart`輸出架構。

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

  |# |秘訣                                                                                  |偏好                                        |超過                                                                    |
  |--|-------------------------------------------------------------------------------------|----------------------------------------------|------------------------------------------------------------------------|
  | 1| 預設使用單個`search`運算子而不是多個連續`search`運算子|`search "billg" and ("steveb" or "satyan")`   |<code>search "billg" &#124; search "steveb" or "satyan"<code>           ||
  | 2| 更喜歡在`search`操作員內部進行過濾                                       |`search "billg" and "steveb"`                 |<code>search * &#124; where * has "billg" and * has "steveb"<code>      ||
