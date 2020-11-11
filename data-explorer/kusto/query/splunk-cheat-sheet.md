---
title: Splunk 至 Kusto log 查詢 language in Azure 監視器
description: 在 learning Kusto 記錄查詢中熟悉 Splunk 的使用者說明。
ms.service: data-explorer
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2018
ms.openlocfilehash: f574493f2029d08fd71b44c858592a6fd8467c82
ms.sourcegitcommit: b6f0f112b6ddf402e97c011a902bd70ba408e897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2020
ms.locfileid: "94499096"
---
# <a name="splunk-to-kusto-query-language"></a>Splunk 至 Kusto 查詢語言

本文旨在協助熟悉 Splunk 的使用者瞭解 Kusto 查詢語言，以在 Kusto 中撰寫記錄查詢。 我們會直接比較這兩者的各項內容，以了解主要差異及相似之處，以便您運用現有的知識。

## <a name="structure-and-concepts"></a>結構和概念

下表比較 Splunk 和 Kusto 記錄之間的概念和資料結構。

 | 概念 | Splunk | Kusto |  註解 |
 |:---|:---|:---|:---|
 | 部署單位  | 叢集 |  叢集 |  Kusto 允許任意的跨叢集查詢。 Splunk 則無法。 |
 | 資料快取 |  貯體  |  快取與保留原則 |  控制資料的期間和快取層級。 此設定會直接影響查詢效能和部署成本。 |
 | 資料的邏輯分割區  |  索引  |  [資料庫]  |  可允許資料的邏輯分隔。 兩種實作皆允許分割區的集合聯集和聯結。 |
 | 結構化的事件中繼資料 | N/A | 資料表 |  Splunk 沒有事件中繼資料的搜尋語言概念。 Kusto 記錄有資料表的概念，而資料表有資料行。 每個事件執行個體會對應至一個資料列。 |
 | 資料記錄 | event | 列 |  僅限詞彙變更。 |
 | 資料記錄屬性 | field |  column |  在 Kusto 中，這會預先定義為數據表結構的一部分。 在 Splunk 中，每個事件都有自己的欄位集。 |
 | 型別 | datatype |  datatype |  Kusto 資料類型在資料行上設定時更明確。 兩者都能夠以動態方式使用資料類型，且擁有大致相當的資料類型集，包括 JSON 支援。 |
 | 查詢和搜尋  | 搜尋 | 查詢 |  Kusto 和 Splunk 之間的概念基本上是相同的。 |
 | 事件擷取時間 | 系統時間 | ingestion_time() |  在 Splunk 中，每個事件都會取得事件編製索引時間的系統時間戳記。 在 Kusto 中，您可以定義稱為 ingestion_time 的原則，該原則會公開可透過 ingestion_time ( # A1 函式參考的系統資料行。 |

## <a name="functions"></a>Functions

下表指定 Kusto 中相當於 Splunk 函數的函數。

|Splunk | Kusto |註解
|:---|:---|:---
| `strcat` | `strcat()` | (1) |
| `split`  | `split()` | (1) |
| `if`     | `iff()`   | (1) |
| `tonumber` | `todouble()`<br>`tolong()`<br>`toint()` | (1) |
| `upper`<br>`lower` |toupper()<br>`tolower()`|(1) |
| `replace` | `replace()` | (1)<br> 也請注意，雖然 `replace()` 在兩個產品中都採用三個參數，但參數是不同的。 |
| `substr` | `substring()` | (1)<br>也請注意，Splunk 使用以一為基底的索引。 Kusto 注意以零為基底的索引。 |
| `tolower` |  `tolower()` | (1) |
| `toupper` | `toupper()` | (1) |
| `match` | `matches regex` |  (2)  |
| `regex` | `matches regex` | 在 Splunk 中，`regex` 是運算子。 在 Kusto 中，這是關係運算子。 |
| `searchmatch` | == | 在 Splunk 中，`searchmatch` 可允許搜尋完全相符的字串。
| `random` | rand()<br>rand(n) | Splunk 的函式會傳回 0 到 2<sup>31</sup>-1 的數字。 Kusto ' 會傳回介於0.0 和1.0 之間的數位，或如果提供參數，則介於0到 n-1 之間。
| `now` | `now()` | (1)
| `relative_time` | `totimespan()` | (1)<br>在 Kusto 中，Splunk 等同于 relative_time (datetimeVal，offsetVal) 是 datetimeVal + totimespan (offsetVal) 。<br>例如，<code>search &#124; eval n=relative_time(now(), "-1d@d")</code> 會成為 <code>...  &#124; extend myTime = now() - totimespan("1d")</code>。

(1) 在 Splunk 中，會使用 `eval` 運算子叫用函式。 在 Kusto 中，它會當做或的一部分使用 `extend` `project` 。<br>(2) 在 Splunk 中，會使用 `eval` 運算子叫用函式。 在 Kusto 中，它可以與運算子搭配使用 `where` 。


## <a name="operators"></a>運算子

下列各節提供在 Splunk 與 Kusto 之間使用不同運算子的範例。

> [!NOTE]
> 基於下列範例的目的，Splunk 欄位 _規則_ 會對應到 Kusto 中的資料表，而 Splunk 的預設時間戳記會對應到 Logs Analytics _Ingestion_time ( # B1_ 資料行。

### <a name="search"></a>搜尋
在 Splunk 中，您可以省略 `search` 關鍵字，並指定不具引號的字串。 在 Kusto 中，您必須使用來啟動每個查詢，不具 `find` 引號的字串是資料行名稱，且查閱值必須是加上引號的字串。 

| 產品 | 運算子 | 範例 |
|:---|:---|:---|
| Splunk | `search` | <code>search Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" earliest=-24h</code> |
| Kusto | `find` | <code>find Session.Id=="c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time()> ago(24h)</code> |


### <a name="filter"></a>Filter
Kusto 記錄查詢會從篩選的表格式結果集開始。 Splunk 的篩選則是在目前索引上的預設作業。 您也可以使用 Splunk 中的 `where` 運算子，但我們不建議這樣做。

| 產品 | 運算子 | 範例 |
|:---|:---|:---|
| Splunk | `search` | <code>Event.Rule="330009.2" Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" _indextime>-24h</code> |
| Kusto | `where` | <code>Office_Hub_OHubBGTaskError<br>&#124; where Session_Id == "c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time() > ago(24h)</code> |

### <a name="getting-n-eventsrows-for-inspection"></a>取得 n 個事件/資料列以進行檢查 
Kusto 記錄查詢也支援 `take` 做為的別名 `limit` 。 在 Splunk 中，如果結果已進行排序，`head` 會傳回前 n 個結果。 在 Kusto 中，限制不會排序，但會傳回找到的前 n 個數據列。

| 產品 | 運算子 | 範例 |
|:---|:---|:---|
| Splunk | `head` | <code>Event.Rule=330009.2<br>&#124; head 100</code> |
| Kusto | `limit` | <code>Office_Hub_OHubBGTaskError<br>&#124; limit 100</code> |

### <a name="getting-the-first-n-eventsrows-ordered-by-a-fieldcolumn"></a>取得依欄位/資料行排序的前 n 個事件/資料列
對於底部結果，Splunk 中可以使用 `tail`。 在 Kusto 中，您可以使用指定排序方向 `asc` 。

| 產品 | 運算子 | 範例 |
|:---|:---|:---|
| Splunk | `head` |  <code>Event.Rule="330009.2"<br>&#124; sort Event.Sequence<br>&#124; head 20</code> |
| Kusto | `top` | <code>Office_Hub_OHubBGTaskError<br>&#124; top 20 by Event_Sequence</code> |

### <a name="extending-the-result-set-with-new-fieldscolumns"></a>以新的欄位/資料行擴充結果集
Splunk 也有 `eval` 函式，其無法與 `eval` 運算子相比較。 Splunk 中的 `eval` 運算子和 `extend` Kusto 中的運算子只支援純量函數和算術運算子。

| 產品 | 運算子 | 範例 |
|:---|:---|:---|
| Splunk | `eval` |  <code>Event.Rule=330009.2<br>&#124; eval state= if(Data.Exception = "0", "success", "error")</code> |
| Kusto | `extend` | <code>Office_Hub_OHubBGTaskError<br>&#124; extend state = iif(Data_Exception == 0,"success" ,"error")</code> |

### <a name="rename"></a>重新命名 
Kusto 使用 `project-rename` 運算子來重新命名欄位。 `project-rename` 允許查詢利用預建給欄位的任何索引。 Splunk 有 `rename` 運算子可進行相同的動作。

| 產品 | 運算子 | 範例 |
|:---|:---|:---|
| Splunk | `rename` |  <code>Event.Rule=330009.2<br>&#124; rename Date.Exception as execption</code> |
| Kusto | `project-rename` | <code>Office_Hub_OHubBGTaskError<br>&#124; project-rename exception = Date_Exception</code> |

### <a name="format-resultsprojection"></a>格式結果/投影
Splunk 似乎沒有類似 `project-away` 的運算子。 您可以使用 UI 篩選欄位。

| 產品 | 運算子 | 範例 |
|:---|:---|:---|
| Splunk | `table` |  <code>Event.Rule=330009.2<br>&#124; table rule, state</code> |
| Kusto | `project`<br>`project-away` | <code>Office_Hub_OHubBGTaskError<br>&#124; project exception, state</code> |

### <a name="aggregation"></a>彙總
請參閱不同彙總函式的 [彙總函式清單](summarizeoperator.md#list-of-aggregation-functions) 。

| 產品 | 運算子 | 範例 |
|:---|:---|:---|
| Splunk | `stats` |  <code>search (Rule=120502.*)<br>&#124; stats count by OSEnv, Audience</code> |
| Kusto | `summarize` | <code>Office_Hub_OHubBGTaskError<br>&#124; summarize count() by App_Platform, Release_Audience</code> |


### <a name="join"></a>Join
Splunk 中的聯結具有重大限制。 子查詢的限制為 10000 筆結果 (設定在部署組態檔中)，且聯結類別的數目有限。

| 產品 | 運算子 | 範例 |
|:---|:---|:---|
| Splunk | `join` |  <code>Event.Rule=120103* &#124; stats by Client.Id, Data.Alias \| join Client.Id max=0 [search earliest=-24h Event.Rule="150310.0" Data.Hresult=-2147221040]</code> |
| Kusto | `join` | <code>cluster("OAriaPPT").database("Office PowerPoint").Office_PowerPoint_PPT_Exceptions<br>&#124; where  Data_Hresult== -2147221040<br>&#124; join kind = inner (Office_System_SystemHealthMetadata<br>&#124; summarize by Client_Id, Data_Alias)on Client_Id</code>   |

### <a name="sort"></a>Sort
在 Splunk 中，若要以遞增順序排序，您必須使用 `reverse` 運算子。 Kusto 也支援定義在開頭或結尾放置 null 的位置。

| 產品 | 運算子 | 範例 |
|:---|:---|:---|
| Splunk | `sort` |  <code>Event.Rule=120103<br>&#124; sort Data.Hresult<br>&#124; reverse</code> |
| Kusto | `order by` | <code>Office_Hub_OHubBGTaskError<br>&#124; order by Data_Hresult,  desc</code> |

### <a name="multivalue-expand"></a>展開多重值
這是 Splunk 和 Kusto 中的相同運算子。

| 產品 | 運算子 | 範例 |
|:---|:---|:---|
| Splunk | `mvexpand` |  `mvexpand foo` |
| Kusto | `mvexpand` | `mvexpand foo` |

### <a name="results-facets-interesting-fields"></a>結果 Facet，關鍵值組欄位
在 Azure 入口網站中的 Log Analytics，只會公開第一個資料行。 所有資料行可透過 API 提供。

| 產品 | 運算子 | 範例 |
|:---|:---|:---|
| Splunk | `fields` |  <code>Event.Rule=330009.2<br>&#124; fields App.Version, App.Platform</code> |
| Kusto | `facets` | <code>Office_Excel_BI_PivotTableCreate<br>&#124; facet by App_Branch, App_Version</code> |

### <a name="de-duplicate"></a>De-duplicate
您可以改用 `summarize arg_min()`，反轉已選擇的記錄順序。

| 產品 | 運算子 | 範例 |
|:---|:---|:---|
| Splunk | `dedup` |  <code>Event.Rule=330009.2<br>&#124; dedup device_id sortby -batterylife</code> |
| Kusto | `summarize arg_max()` | <code>Office_Excel_BI_PivotTableCreate<br>&#124; summarize arg_max(batterylife, *) by device_id</code> |

## <a name="next-steps"></a>後續步驟

- 逐步解說 [Kusto 查詢語言](tutorial.md?pivots=azuremonitor)的教學課程。
