---
title: 查詢限制 - Azure 資料總管
description: 本文將說明 Azure 資料總管中的查詢限制。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.localizationpriority: high
ms.openlocfilehash: 3b230ea0ed8bba80741e18f24cd96cf271224f25
ms.sourcegitcommit: e278dae04f12658d0907f7b6ba46c6a34c53dcd7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2020
ms.locfileid: "96901098"
---
# <a name="query-limits"></a>查詢限制

Kusto 是一種可裝載大型資料集的隨選 (ad-hoc) 查詢引擎，可藉由將所有相關資料保留在記憶體中來嘗試滿足查詢。
但其固有風險是查詢會無上限地獨佔服務資源。 Kusto 會以預設查詢限制的形式提供數個內建保護。 如果您考慮移除這些限制，請先判斷您這麼做是否真的可獲得任何價值。

## <a name="limit-on-query-concurrency"></a>查詢並行的限制

叢集會將 **查詢並行** 限制強加於同時執行的多個查詢上。

* 查詢並行限制的預設值取決於其執行所在的 SKU 叢集，且計算方式為：`Cores-Per-Node x 10`。
  * 例如，針對在 D14v2 SKU 上設定的叢集，其中每部機器都有 16 個虛擬核心，預設的查詢並行限制為 `16 cores x10 = 160`。
* 設定[查詢節流原則](../management/query-throttling-policy.md)可以變更預設值。 
  * 可以在叢集上同時執行的實際查詢數目取決於各種因素。 最主要的因素包括叢集 SKU、叢集的可用資源和查詢模式。 您可以根據類似的生產查詢模式上執行的負載測試，來設定查詢節流原則。

## <a name="limit-on-result-set-size-result-truncation"></a>結果集大小的限制 (結果截斷)

**結果截斷** 是預設的限制，會限制查詢所傳回的結果集。 Kusto 會將傳回給用戶端的記錄數目限制為 **500,000**，而這些記錄的整體資料大小會限制為 **64 MB**。 當其中一項限制超過時，查詢就會失敗並出現「部分查詢失敗」。 超過整體資料大小將會產生例外狀況，並出現下列訊息：

```
The Kusto DataEngine has failed to execute a query: 'Query result set has exceeded the internal data size limit 67108864 (E_QUERY_RESULT_SET_TOO_LARGE).'
```

超過記錄數目將會失敗，並出現例外狀況，指出：

```
The Kusto DataEngine has failed to execute a query: 'Query result set has exceeded the internal record count limit 500000 (E_QUERY_RESULT_SET_TOO_LARGE).'
```

有數個策略可處理此錯誤。

* 將查詢修改為只傳回感興趣的資料，以減少結果集的大小。 當最初失敗的查詢太「寬」時，此策略非常有用。 例如，查詢不會在輸出中排除不需要的資料行。
* 將查詢後置處理 (例如彙總) 移至查詢本身，以減少結果集的大小。 針對查詢將輸出送至另一個處理系統，然後再執行其他彙總的案例，此策略非常有用。
* 當您想要從服務匯出大量資料集時，請將查詢切換為使用[資料匯出](../management/data-export/index.md)。
* 指示服務使用下面所列的 `set` 陳述式，或使用[用戶端要求屬性](../api/netfx/request-properties.md)中的旗標，來隱藏此查詢限制。

以下方法可減少查詢所產生的結果集大小：

* 使用 [summarize 運算子](../query/summarizeoperator.md)群組，並彙總查詢輸出中相似的記錄。 也可能可使用[任何彙總函式](../query/any-aggfunction.md)來對一些資料行進行取樣。
* 使用 [take 運算子](../query/takeoperator.md)來對查詢輸出進行取樣。
* 使用 [substring 函式](../query/substringfunction.md)來修剪範圍較寬的任意文字資料行。
* 使用 [project 運算子](../query/projectoperator.md)，從結果集中移除任何不必要的資料行。

您可以使用 `notruncation` 要求選項來停用結果截斷。
我們建議您仍然保留某種形式的限制。

例如：

```kusto
set notruncation;
MyTable | take 1000000
```

您也可以設定 `truncationmaxsize` 的值 (以位元組為單位的資料大小上限，預設為 64 MB) 和 `truncationmaxrecords` (記錄數目上限，預設為 500,000)，以更精確地控制結果截斷。 例如，下列查詢會將結果截斷設定為在超過 1,105 筆記錄或 1MB 時發生。

```kusto
set truncationmaxsize=1048576;
set truncationmaxrecords=1105;
MyTable | where User=="UserId1"
```

移除結果截斷限制表示您想要將大量資料移出 Kusto。

您可以使用 `.export` 命令來移除結果截斷限制，以用於匯出或之後的彙總。 如果您選擇稍後彙總，請考慮使用 Kusto 來彙總。

Kusto 提供許多用戶端程式庫，可藉由將結果串流至呼叫端來處理「無限大」的結果量。 使用其中一個程式庫，並將其設定為串流模式。 例如，使用 .NET Framework 用戶端 (Microsoft.Azure.Kusto.Data)，並將連接字串的串流屬性設定為 true，或使用一律會串流結果的 ExecuteQueryV2Async() 呼叫。

結果截斷會預設為套用狀態，而不只是套用至傳回給用戶端的結果串流。 根據預設，其也會套用至跨叢集查詢中一個叢集發給另一個叢集的所有子查詢，且效果類似。

### <a name="setting-multiple-result-truncation-properties"></a>設定多個結果截斷屬性

當使用 `set` 陳述式時，和/或在[用戶端要求屬性](../api/netfx/request-properties.md)中指定旗標時，適用下列情況。

* 如果已設定 `notruncation`，而且也設定任何 `truncationmaxsize`、`truncationmaxrecords` 或 `query_take_max_records`，則 `notruncation` 會遭忽略。
* 如果 `truncationmaxsize`、`truncationmaxrecords` 和/或 `query_take_max_records` 會設定多次，則會套用每個屬性的「較低」值。

## <a name="limit-on-memory-per-iterator"></a>每個迭代器的記憶體限制

**每個結果集迭代器的記憶體上限** 是 Kusto 用來防止「失控」查詢的另一個限制。 這項限制 (以要求選項 `maxmemoryconsumptionperiterator` 表示) 會針對單一查詢計劃結果集迭代器可保存的記憶體數量設定上限。 這項限制適用於本質上不進行串流的特定迭代器，例如 `join`。以下是發生這種情況時，將傳回的幾個錯誤訊息：

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

預設值為 5 GB。 您最多可以將此值增加到機器實體記憶體的一半：

```kusto
set maxmemoryconsumptionperiterator=68719476736;
MyTable | ...
```

在許多情況下，您可以藉由資料集取樣來避免超過此限制。 下列兩個查詢說明如何進行取樣。 第一個是使用亂數產生器的統計取樣。 第二個是明確取樣，其方式是從資料集的某些資料行進行雜湊處理，通常是某些識別碼資料行。

```kusto
T | where rand() < 0.1 | ...

T | where hash(UserId, 10) == 1 | ...
```

## <a name="limit-on-memory-per-node"></a>每個節點的記憶體限制

**每個節點的個別查詢記憶體上限** 是用來防止「失控」查詢的另一個限制。 這項限制 (以要求選項 `max_memory_consumption_per_query_per_node` 表示) 會針對特定查詢的單一節點可使用的記憶體數量設定上限。

```kusto
set max_memory_consumption_per_query_per_node=68719476736;
MyTable | ...
```

## <a name="limit-on-accumulated-string-sets"></a>累計字串集的限制

在各種查詢作業中，Kusto 都需要「收集」字串值，並在開始產生結果之前，在內部對這些值進行緩衝處理。 這些累計字串集的大小及其可以保存的項目數量都會受到限制。 此外，每個個別的字串都不應該超過特定限制。
超過這些限制的其中一個就會導致下列其中一個錯誤：

```
Runaway query (E_RUNAWAY_QUERY). (message: 'Accumulated string array getting too large and exceeds the limit of ...GB (see https://aka.ms/kustoquerylimits)')

Runaway query (E_RUNAWAY_QUERY). (message: 'Accumulated string array getting too large and exceeds the maximum count of ..GB items (see http://aka.ms/kustoquerylimits)')
```

目前沒有任何參數可增加字串集的大小上限。
因應措施是將查詢重新改寫，以減少必須緩衝處理的資料量。 您可以先將不需要的資料行從輸出中移除 (project away)，然後再讓 join 及 summarize 等運算子使用。 或者，您可以使用[隨機查詢](../query/shufflequery.md)策略。

## <a name="limit-execution-timeout"></a>執行逾時限制

**伺服器逾時** 是套用至所有要求的服務端逾時功能。
Kusto 中的多個點上都會強制對執行中的要求 (查詢和控制命令) 設定逾時功能：

* 用戶端程式庫 (如果有使用的話)
* 接受要求的服務端點
* 處理要求的服務引擎

根據預設，查詢的逾時會設定為 4 分鐘，而控制命令則設為 10 分鐘。 如有需要，可以增加此值 (上限為一小時)。

* 如果您使用 Kusto.Explorer 進行查詢，請使用 [工具]&gt; [選項] &gt;[連線]* &gt; [查詢伺服器逾時]。
* 以程式設計方式，將 `servertimeout` 用戶端要求屬性 (`System.TimeSpan` 類型的值) 設定為最多一小時。

**關於逾時的注意事項**

* 在用戶端上，套用逾時的範圍是從即將建立要求到回應開始到達用戶端的時間為止。 在用戶端上讀回承載所花費的時間，並不會被視為逾時的一部分。 這取決於呼叫端從串流中提取資料的速度。
* 此外，在用戶端上使用的實際逾時值會稍微高於使用者要求的伺服器逾時值。 這種差異是將網路延遲納入考量。
* 若要自動使用允許的要求逾時上限，請將用戶端要求屬性 `norequesttimeout` 設定為 `true`。

## <a name="limit-on-query-cpu-resource-usage"></a>查詢 CPU 資源使用量的限制

Kusto 可讓您執行查詢，並使用與叢集一樣多的 CPU 資源。 如果有一個以上的執行，其會嘗試在查詢之間進行公平的循環配置資源。 此方法可讓隨選查詢發揮最佳效能。
有時候，您可能會想要限制特定查詢所使用的 CPU 資源。 例如，如果您執行「背景作業」，系統可能會容許較高的延遲，好讓並行的隨選查詢具有高優先順序。

Kusto 支援在執行查詢時指定兩個[用戶端要求屬性](../api/netfx/request-properties.md)。 這些屬性為 query_fanout_threads_percent 和 query_fanout_nodes_percent。
這兩個屬性都是預設為最大值 (100) 的整數，但可能會針對特定查詢減少到某個其他值。 

第一個屬性 query_fanout_threads_percent 會控制執行緒使用的扇出 (fanout) 因素。 當此屬性是 100% 時，叢集將會在每個節點上指派所有 CPU。 例如，部署在 Azure D14 節點上的叢集上有 16 個 CPU。 當此屬性是 50% 時，則會使用一半的 CPU，依此類推。 數字會無條件進位到一整個 CPU，因此可以放心地將其設定為 0。 第二個屬性 query_fanout_nodes_percent 會控制叢集中可供每個子查詢散發作業使用的查詢節點數。 其運作方式也類似。

## <a name="limit-on-query-complexity"></a>查詢複雜度的限制

在查詢執行期間，查詢文字會轉換成代表查詢的關係運算子樹狀結構。
如果樹狀結構的深度超過數千個層級的內部閾值，則查詢會被視為太複雜而無法處理，而且將會失敗並產生錯誤碼。 此失敗表示關係運算子樹狀結構超過其限制。
由於查詢中有太多連結在一起的二元運算子，所以超出了限制。 例如：

```kusto
T 
| where Column == "value1" or 
        Column == "value2" or 
        .... or
        Column == "valueN"
```

在此特定案例中，請使用 [`in()`](../query/inoperator.md) 運算子來重寫查詢。

```kusto
T 
| where Column in ("value1", "value2".... "valueN")
```
