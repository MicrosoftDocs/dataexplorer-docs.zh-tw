---
title: 適用于 Azure 資料總管和 Azure 監視器的 Kusto 對應 Splunk
description: 熟悉 Splunk 的使用者概念對應，以瞭解 Kusto 查詢語言來撰寫記錄查詢。
ms.service: data-explorer
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2018
ms.openlocfilehash: 8679b47a2c698c88c4d1773f9c50dee495532ae7
ms.sourcegitcommit: faa747df81c49b96d173dbd5a28d2ca4f3a2db5f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95783228"
---
# <a name="splunk-to-kusto-query-language-map"></a>Splunk 至 Kusto 查詢語言對應

本文旨在協助熟悉 Splunk 的使用者瞭解 Kusto 查詢語言，以使用 Kusto 撰寫記錄查詢。 在兩者之間進行直接比較，以反白顯示主要差異和相似之處，讓您可以根據現有知識來打造。

## <a name="structure-and-concepts"></a>結構和概念

下表比較 Splunk 和 Kusto 記錄之間的概念和資料結構：

 | 概念 | Splunk | Kusto |  註解 |
 |:---|:---|:---|:---|
 | 部署單位  | 叢集 |  叢集 |  Kusto 允許任意的跨叢集查詢。 Splunk 則無法。 |
 | 資料快取 |  貯體  |  快取和保留原則 |  控制資料的期間和快取層級。 這項設定會直接影響查詢的效能和部署成本。 |
 | 資料的邏輯分割區  |  索引  |  [資料庫]  |  可允許資料的邏輯分隔。 兩種實作皆允許分割區的集合聯集和聯結。 |
 | 結構化事件中繼資料 | N/A | table |  Splunk 不會將事件中繼資料的概念公開給搜尋語言。 Kusto 記錄具有資料表的概念，而資料表有資料行。 每個事件執行個體會對應至一個資料列。 |
 | 資料記錄 | event | 列 |  僅限詞彙變更。 |
 | 資料記錄屬性 | field |  column |  在 Kusto 中，此設定會預先定義為數據表結構的一部分。 在 Splunk 中，每個事件都有自己的欄位集。 |
 | types | datatype |  datatype |  Kusto 資料類型更明確，因為它們是在資料行上設定的。 兩者都能夠以動態方式使用資料類型，以及大致上相等的資料類型集合，包括 JSON 支援。 |
 | 查詢和搜尋  | 搜尋 | 查詢 |  本質上的概念在 Kusto 和 Splunk 之間是一樣的。 |
 | 事件內嵌時間 | 系統時間 | `ingestion_time()` |  在 Splunk 中，每個事件都會取得事件索引時間的系統時間戳記。 在 Kusto 中，您可以定義稱為 [ingestion_time](../management/ingestiontimepolicy.md) 的原則，該原則會公開可透過 [Ingestion_time ( # B1 ](ingestiontimefunction.md) 函數來參考的系統資料行。 |

## <a name="functions"></a>函數

下表指定 Kusto 中相當於 Splunk 函數的函數。

|Splunk | Kusto |註解
|:---|:---|:---
| `strcat` | `strcat()` | (1) |
| `split`  | `split()` | (1) |
| `if`     | `iff()`   | (1) |
| `tonumber` | `todouble()`<br />`tolong()`<br />`toint()` | (1) |
| `upper`<br />`lower` |toupper()<br />`tolower()`|(1) |
| `replace` | `replace()` | (1)<br /> 也請注意，雖然 `replace()` 這兩個產品都採用三個參數，但參數不同。 |
| `substr` | `substring()` | (1)<br />也請注意，Splunk 使用以一為基底的索引。 Kusto 注意以零為基底的索引。 |
| `tolower` |  `tolower()` | (1) |
| `toupper` | `toupper()` | (1) |
| `match` | `matches regex` |  (2)  |
| `regex` | `matches regex` | 在 Splunk 中，`regex` 是運算子。 在 Kusto 中，這是關係運算子。 |
| `searchmatch` | == | 在 Splunk 中，`searchmatch` 可允許搜尋完全相符的字串。
| `random` | rand()<br />rand(n) | Splunk 的函數會傳回介於0到 2<sup>31</sup>-1 之間的數位。 Kusto 的會傳回介於0.0 和1.0 之間的數位，或如果提供參數，則介於0到 n-1 之間。
| `now` | `now()` | (1)
| `relative_time` | `totimespan()` | (1)<br />在 Kusto 中，Splunk 的對等專案 `relative_time(datetimeVal, offsetVal)` 為 `datetimeVal + totimespan(offsetVal)` 。<br />例如， `search` &#124; `eval n=relative_time(now(), "-1d@d")` 會變成 `...`  &#124; `extend myTime = now() - totimespan("1d")` 。

 (1) 在 Splunk 中，則會使用運算子叫用 `eval` 函式。 在 Kusto 中，它會當做或的一部分使用 `extend` `project` 。<br /> (2) 在 Splunk 中，則會使用運算子叫用 `eval` 函式。 在 Kusto 中，它可以與運算子搭配使用 `where` 。


## <a name="operators"></a>運算子

下列各節提供如何在 Splunk 和 Kusto 中使用不同運算子的範例。

> [!NOTE]
> 在下列範例中，[Splunk] 欄位會 `rule` 對應至 Kusto 中的資料表，而 Splunk 的預設時間戳記會對應到 [記錄分析] 資料 `ingestion_time()` 行。

### <a name="search"></a>搜尋

在 Splunk 中，您可以省略 `search` 關鍵字，並指定不具引號的字串。 在 Kusto 中，您必須使用來啟動每個查詢，不具 `find` 引號的字串是資料行名稱，且查閱值必須是加上引號的字串。 

| 產品 | 運算子 | 範例 |
|:---|:---|:---|
| Splunk | `search` | `search Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" earliest=-24h` |
| Kusto | `find` | `find Session.Id=="c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time()> ago(24h)` |


### <a name="filter"></a>篩選

Kusto 記錄查詢會從套用的表格式結果集開始 `filter` 。 Splunk 的篩選則是在目前索引上的預設作業。 您也可以 `where` 在 Splunk 中使用運算子，但不建議使用。

| 產品 | 運算子 | 範例 |
|:---|:---|:---|
| Splunk | `search` | `Event.Rule="330009.2" Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" _indextime>-24h` |
| Kusto | `where` | `Office_Hub_OHubBGTaskError`<br /> &#124; `where Session_Id == "c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time() > ago(24h)` |

### <a name="get-n-events-or-rows-for-inspection"></a>取得 *n* 個事件或資料列進行檢查

Kusto 記錄查詢也支援 `take` 做為的別名 `limit` 。 在 Splunk 中，如果已排序結果，會傳回 `head` 前 *n* 個結果。 在 Kusto 中， `limit` 不會排序，但會傳回找到的前 *n* 個數據列。

| 產品 | 運算子 | 範例 |
|:---|:---|:---|
| Splunk | `head` | `Event.Rule=330009.2`<br /> &#124; `head 100` |
| Kusto | `limit` | `Office_Hub_OHubBGTaskError`<br /> &#124; `limit 100` |

### <a name="get-the-first-n-events-or-rows-ordered-by-a-field-or-column"></a>取得依欄位或資料行排序的前 *n* 個事件或資料列

針對底部的結果，您可以使用 Splunk 中的 `tail` 。 在 Kusto 中，您可以使用指定排序方向 `asc` 。

| 產品 | 運算子 | 範例 |
|:---|:---|:---|
| Splunk | `head` |  `Event.Rule="330009.2"`<br /> &#124; `sort Event.Sequence`<br /> &#124; `head 20` |
| Kusto | `top` | `Office_Hub_OHubBGTaskError`<br /> &#124; `top 20 by Event_Sequence` |

### <a name="extend-the-result-set-with-new-fields-or-columns"></a>以新的欄位或資料行擴充結果集

Splunk 具有 `eval` 函式，但無法與 `eval` Kusto 中的運算子比較。 Splunk 中的 `eval` 運算子和 `extend` Kusto 中的運算子都只支援純量函數和算術運算子。

| 產品 | 運算子 | 範例 |
|:---|:---|:---|
| Splunk | `eval` |  `Event.Rule=330009.2`<br /> &#124; `eval state= if(Data.Exception = "0", "success", "error")` |
| Kusto | `extend` | `Office_Hub_OHubBGTaskError`<br /> &#124; `extend state = iif(Data_Exception == 0,"success" ,"error")` |

### <a name="rename"></a>重新命名

Kusto 使用 `project-rename` 運算子來重新命名欄位。 在 `project-rename` 運算子中，查詢可利用針對某個欄位預建的任何索引。 Splunk 具有 `rename` 執行相同操作的運算子。

| 產品 | 運算子 | 範例 |
|:---|:---|:---|
| Splunk | `rename` |  `Event.Rule=330009.2`<br /> &#124; `rename Date.Exception as execption` |
| Kusto | `project-rename` | `Office_Hub_OHubBGTaskError`<br /> &#124; `project-rename exception = Date_Exception` |

### <a name="format-results-and-projection"></a>格式化結果和投射

Splunk 似乎沒有類似的運算子 `project-away` 。 您可以使用 UI 來篩選出欄位。

| 產品 | 運算子 | 範例 |
|:---|:---|:---|
| Splunk | `table` |  `Event.Rule=330009.2`<br /> &#124; `table rule, state` |
| Kusto | `project`<br />`project-away` | `Office_Hub_OHubBGTaskError`<br /> &#124; `project exception, state` |

### <a name="aggregation"></a>彙總

查看可用的匯總 [函數清單](summarizeoperator.md#list-of-aggregation-functions) 。

| 產品 | 運算子 | 範例 |
|:---|:---|:---|
| Splunk | `stats` |  `search (Rule=120502.*)`<br /> &#124; `stats count by OSEnv, Audience` |
| Kusto | `summarize` | `Office_Hub_OHubBGTaskError`<br /> &#124; `summarize count() by App_Platform, Release_Audience` |


### <a name="join"></a>Join

`join` 在 Splunk 中有相當大的限制。 子查詢的限制為10000個結果 (在部署設定檔) 中設定，而且可以使用有限數目的聯結類別。

| 產品 | 運算子 | 範例 |
|:---|:---|:---|
| Splunk | `join` |  `Event.Rule=120103* &#124; stats by Client.Id, Data.Alias` <br /> &#124; `join Client.Id max=0 [search earliest=-24h Event.Rule="150310.0" Data.Hresult=-2147221040]` |
| Kusto | `join` | `cluster("OAriaPPT").database("Office PowerPoint").Office_PowerPoint_PPT_Exceptions`<br /> &#124; `where  Data_Hresult== -2147221040`<br /> &#124; `join kind = inner (Office_System_SystemHealthMetadata`<br /> &#124; `summarize by Client_Id, Data_Alias)on Client_Id`   |

### <a name="sort"></a>Sort

在 Splunk 中，若要以遞增順序排序，您必須使用 `reverse` 運算子。 Kusto 也支援定義在開頭或結尾放置 null 的位置。

| 產品 | 運算子 | 範例 |
|:---|:---|:---|
| Splunk | `sort` |  `Event.Rule=120103`<br /> &#124; `sort Data.Hresult` <br /> &#124; `reverse` |
| Kusto | `order by` | `Office_Hub_OHubBGTaskError`<br /> &#124; `order by Data_Hresult,  desc` |

### <a name="multivalue-expand"></a>展開多重值

多值展開運算子在 Splunk 和 Kusto 中都很類似。

| 產品 | 運算子 | 範例 |
|:---|:---|:---|
| Splunk | `mvexpand` |  `mvexpand solutions` |
| Kusto | `mv-expand` | `mv-expand solutions` |

### <a name="result-facets-interesting-fields"></a>結果 facet，有趣的欄位

在 Azure 入口網站中的 Log Analytics，只會公開第一個資料行。 所有資料行可透過 API 提供。

| 產品 | 運算子 | 範例 |
|:---|:---|:---|
| Splunk | `fields` |  `Event.Rule=330009.2`<br /> &#124; `fields App.Version, App.Platform` |
| Kusto | `facets` | `Office_Excel_BI_PivotTableCreate`<br /> &#124; `facet by App_Branch, App_Version` |

### <a name="deduplicate"></a>刪除

在 Kusto 中，您可以使用 `summarize arg_min()` 來反轉選擇記錄的順序。

| 產品 | 運算子 | 範例 |
|:---|:---|:---|
| Splunk | `dedup` |  `Event.Rule=330009.2`<br /> &#124; `dedup device_id sortby -batterylife` |
| Kusto | `summarize arg_max()` | `Office_Excel_BI_PivotTableCreate`<br /> &#124; `summarize arg_max(batterylife, *) by device_id` |

## <a name="next-steps"></a>後續步驟

- 逐步解說 [Kusto 查詢語言](tutorial.md?pivots=azuremonitor)的教學課程。
