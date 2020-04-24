---
title: 查詢限制 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的查詢限制。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: 437e9781b9e29db7496292ba6a416f6e0c769e8b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523064"
---
# <a name="query-limits"></a>查詢限制

由於 Kusto 是一個臨時查詢引擎,它承載大型數據集並嘗試通過在記憶體中保存所有相關數據來滿足查詢,因此存在查詢將壟斷服務資源而不帶界限的內在風險。 Kusto 以默認查詢限制的形式提供了許多內置保護。

## <a name="limit-on-query-concurrency"></a>查詢併發限制

**查詢併發**是群集對同時運行的多個查詢施加的限制。
查詢並發限制的預設值取決於它執行的 SKU 群集,並計算`Cores-Per-Node x 10`為: 。 例如,對於在 D14v2 SKU 上設定的群集,每台電腦具有 16 個 vCore,`16 cores x10 = 160`預設查詢併發限制為 。
可以通過創建支援票證來更改預設值。 將來,此控件也將通過控制命令公開。

## <a name="limit-on-result-set-size-result-truncation"></a>限制結果集大小(結果截斷)

**默認情況下,結果截斷**是默認情況下對查詢返回的結果集設置的限制。 Kusto 將傳回客戶端的紀錄數限制為**500,000**個,這些紀錄的總記憶體限制為**64 MB**。 當超過這些限制之一時,查詢將失敗,出現"部分查詢失敗"。 超過總記憶體將產生訊息的異常:

```
The Kusto DataEngine has failed to execute a query: 'Query result set has exceeded the internal data size limit 67108864 (E_QUERY_RESULT_SET_TOO_LARGE).'
```

超過記錄數將失敗,但有一個例外,即:

```
The Kusto DataEngine has failed to execute a query: 'Query result set has exceeded the internal record count limit 500000 (E_QUERY_RESULT_SET_TOO_LARGE).'
```

處理錯誤有許多政策:

* 通過修改查詢以不返回無趣數據來減小結果集大小。 當初始(失敗)查詢過於"寬"時,這通常很有用(例如,它不投影不需要的數據列)。
* 通過將查詢后處理(如聚合)轉移到查詢本身來減小結果集大小。 這在將查詢輸出饋送到另一個處理系統(然後執行其他聚合)的情況下非常有用。 
* 從查詢切換到使用[資料匯出](../management/data-export/index.md)。
   當您要從服務匯出大型數據集時,這是合適的。
* 指示服務禁止顯示此查詢限制。

減少查詢產生的結果集大小的常用方法是:

* 使用[匯總運算子](../query/summarizeoperator.md)組並在查詢輸出中對類似記錄進行聚合,可能會使用[任何聚合函數](../query/any-aggfunction.md)對某些列進行採樣。
* 使用[take 運算符](../query/takeoperator.md)對查詢輸出進行採樣。
* 使用[子字串函數](../query/substringfunction.md)修剪寬自由文字列。
* 使用[專案運算符](../query/projectoperator.md)從結果集中刪除任何無趣列。

可以使用`notruncation`請求選項禁用結果截斷。 強烈建議在這種情況下,仍然實行某種形式的限制。 例如：

```kusto
set notruncation;
MyTable | take 1000000
```

還可以通過設置`truncationmaxsize`值 (以位元組為單位的最大資料大小、預設值設置為 64 MB)和`truncationmaxrecords`(最大記錄數、默認值為 500,000)對結果截斷進行更精細的控制。 例如,以下查詢將結果截斷設置在 1,105 條記錄或 1MB 時發生,以超出者為準:

```kusto
set truncationmaxsize=1048576;
set truncationmaxrecords=1105;
MyTable | where User=="Ploni"
```

Kusto 用戶端庫當前假定存在此限制。 雖然您可以無限制地增加限制,但最終您將達到當前不可配置的用戶端限制。 一種可能的解決方法是直接針對 REST API 協定進行程式設計,並為 Kusto 查詢結果實現流解析器。 讓 Kusto 團隊知道您是否遇到此問題,以便我們可以適當地確定流用戶端的優先順序。

默認情況下,將應用結果截斷,而不僅僅是返回到客戶端的結果流;默認情況下,它還應用於任何子查詢,即一個 Kusto 叢集在跨群集查詢中向另一個 Kusto 群集發出問題,具有類似的效果。

## <a name="limit-on-memory-per-iterator"></a>每個反覆運算器的記憶體限制

**每個結果集反覆運算器的最大記憶體**是 Kusto 用於防止「失控」查詢的另一個限制。 此限制(由請求選項`maxmemoryconsumptionperiterator`表示)設置單個查詢計劃結果集反覆運算器可以容納的記憶體量的上限。 (此限制適用於未按自然性質流式傳輸的特定反覆運算器,例如`join`.以下是一些錯誤消息,當發生這種情況時將返回:

```
The ClusterBy operator has exceeded the memory budget during evaluation. Results may be incorrect or incomplete E_RUNAWAY_QUERY.

The DemultiplexedResultSetCache operator has exceeded the memory budget during evaluation. Results may be incorrect or incomplete (E_RUNAWAY_QUERY).

The ExecuteAndCache operator has exceeded the memory budget during evaluation. Results may be incorrect or incomplete (E_RUNAWAY_QUERY).

The HashJoin operator has exceeded the memory budget during evaluation. Results may be incorrect or incomplete (E_RUNAWAY_QUERY).

The Sort operator has exceeded the memory budget during evaluation. Results may be incorrect or incomplete (E_RUNAWAY_QUERY).

The Summarize operator has exceeded the memory budget during evaluation. Results may be incorrect or incomplete (E_RUNAWAY_QUERY).

The TopNestedAggregator operator has exceeded the memory budget during evaluation. Results may be incorrect or incomplete (E_RUNAWAY_QUERY).

The TopNested operator has exceeded the memory budget during evaluation. Results may be incorrect or incomplete (E_RUNAWAY_QUERY).
```

預設情況下,此值設置為 5 GB。 一個可以增加此值高達機器物理記憶體的一半:

```kusto
set maxmemoryconsumptionperiterator=68719476736;
MyTable | ...
```

在考慮刪除這些限制時,首先確定是否通過這樣做實際上獲得了任何價值。 特別是,刪除結果截斷限制意味著您打算將批量數據移出 Kusto (用於匯出目的 (在這種情況下,請`.export`您改用 該命令), 或執行以後的聚合(在這種情況下,請考慮使用 Kusto 進行聚合)。
讓 Kusto 團隊知道,您是否有一個無法由這些建議的解決方案滿足的業務方案。  

在許多情況下,通過將數據集採樣到 10%可以避免超過此限制。 下面的兩個查詢顯示了如何執行此採樣。 第一個是統計採樣(使用隨機數產生器)。 第二個是確定性採樣(透過從資料集(通常是一些 ID)中散列):

```kusto
T | where rand() < 0.1 | ...

T | where hash(UserId, 10) == 1 | ...
```

## <a name="limit-on-memory-per-node"></a>每個節點的記憶體限制

**每個節點的最大記憶體**是 Kusto 用於防止「失控」查詢的另一個限制。 此限制(由請求選項`max_memory_consumption_per_query_per_node`表示)設置對可在單個節點上為特定查詢分配的內存量設置上限。 


```kusto
set max_memory_consumption_per_query_per_node=68719476736;
MyTable | ...
```

## <a name="limit-on-accumulated-string-sets"></a>對累積字串集的限制

在各種查詢操作中,Kusto 需要「收集」字串值並在內部緩衝它們,然後才能開始生成結果。 這些累積的字串集的大小和可以容納的項數是有限的。 此外,每個單獨的字串不能超過一定的限制。
超過這些限制之一將導致以下錯誤之一:

```
Runaway query (E_RUNAWAY_QUERY). (message: 'Accumulated string array getting too large and exceeds the limit of ...GB (see https://aka.ms/kustoquerylimits)')

Runaway query (E_RUNAWAY_QUERY). (message: 'Accumulated string array getting too large and exceeds the maximum count of 2G items (see http://aka.ms/kustoquerylimits)')

Runaway query (E_RUNAWAY_QUERY). (message: 'Single string size shouldn't exceed the limit of 2GB (see http://aka.ms/kustoquerylimits)')
```

當前沒有增加最大字串集大小的開關。
作為解決方法,請重新表述查詢以減少必須緩衝的數據量。 例如,通過在「輸入」運算符(如聯接和匯總)之前投影不需要的列。 或者,例如,通過使用[隨機查詢](../query/shufflequery.md)策略。

## <a name="limit-on-request-execution-time-timeout"></a>要求執行時間限制(逾時)

**伺服器超時**是應用於所有請求的服務端超時。
執行要求(查詢及控制命令)的逾時在多個點強制執行:

* 在 Kusto 用戶端庫中(如果使用)
* 在接受要求的 Kusto 服務終結點中
* 處理 Kusto 服務引擎

默認情況下,查詢超時設置為 4 分鐘,控件命令設置為 10 分鐘。 如果需要,此值可以增加(在一小時時封頂):

* 如果使用 Kusto.Explorer 查詢,請**使用工具**&gt;**選項*** &gt;**連線**&gt;**查詢伺服器超時**。
* 以程式設計方式設定`servertimeout`客戶端請求屬性`System.TimeSpan`(類型的值,最多一小時)。

關於超時的說明:

* 在用戶端,超時從創建的請求一直到回應開始到達客戶端的時間應用超時。 在用戶端讀取負載所需的時間不被視為超時的一部分(因為它取決於調用方從流中提取數據的速度)。
* 同樣在用戶端,使用的實際超時值略高於使用者請求的伺服器超時值。 這是為了允許網路延遲。
* 在服務端,並非所有查詢運算元都遵守超時值。
   我們正在逐步增加這種支援。
* 要讓 Kusto 自動使用允許的最大要求逾時,請將用戶端`norequesttimeout`請求屬性`true`設定為 。

<!--
  Request timeout can also be set using a set statement, but we don't mention
  it here as it should not be used in production scenarios.
-->

## <a name="limit-on-query-cpu-resource-usage"></a>限制查詢 CPU 資源使用

默認情況下,Kusto 允許正在運行的查詢使用與群集一樣多的 CPU 資源,嘗試在查詢之間執行公平的迴圈(如果有多個查詢正在運行)。 在許多情況下,這會產生臨時查詢的最佳性能。
在其他情況下,您可能希望限制為特定查詢分配的 CPU 資源。 例如,如果運行"後台作業",則可能會容忍較高的延遲,以便為併發臨時查詢提供高優先順序。

Kusto 支援在執行查詢時指定兩個[客戶端要求屬性](../api/netfx/request-properties.md)**,query_fanout_threads_percent**和**query_fanout_nodes_percent**。
兩者都是預設為最大值 (100) 的整數,但對於特定查詢,可能會減少到某個值。 第一個控制線程使用的扇出因數;當群集為 100% 時,群集將為每個節點(例如,部署在 Azure D14 節點上的群集、16 個 CPU 上的群集)分配給查詢,當它為 50% 超過將使用的 CPU 的一半時,等等(數位被四捨五入到整個 CPU,因此將其設置為 0 是安全的。第二個控制群集中每個子查詢分發操作要利用的節點數,並且以類似方式運行。

## <a name="limit-on-query-complexity"></a>查詢複雜性限制

在查詢執行期間,查詢文本將轉換為表示查詢的關係運算符樹。
如果樹深度超過內部閾值(數千個級別),則查詢被認為過於複雜,無法處理,並且將失敗,並且錯誤代碼指示關係運算符樹超過限制。
在大多數情況下,這是由包含連結在一起的一長串二進位運算符的查詢引起的,例如:

```kusto
T 
| where Column == "value1" or 
        Column == "value2" or 
        .... or
        Column == "valueN"
```

對於此特定情況 -[`in()`](../query/inoperator.md)建議使用 運算元重新編寫查詢。 

```kusto
T 
| where Column in ("value1", "value2".... "valueN")
```