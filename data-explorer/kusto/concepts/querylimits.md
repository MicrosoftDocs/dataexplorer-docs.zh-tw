---
title: 查詢限制-Azure 資料總管
description: 本文說明 Azure 資料總管中的查詢限制。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: 20b1b87778451245e7a886255ce3e83493b0d9e1
ms.sourcegitcommit: 7dd20592bf0e08f8b05bd32dc9de8461d89cff14
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85901940"
---
# <a name="query-limits"></a>查詢限制

Kusto 是一種臨機操作查詢引擎，它會裝載大型資料集，並藉由將所有相關的資料保留在記憶體中來嘗試滿足查詢。
有一種固有的風險，就是查詢會獨佔不含界限的服務資源。 Kusto 提供數個內建的保護，格式為預設查詢限制。

## <a name="limit-on-query-concurrency"></a>查詢並行的限制

**查詢並行**的限制是叢集會同時對多個執行中的查詢強加。

* 查詢並行限制的預設值取決於其執行所在的 SKU 叢集，且計算方式為： `Cores-Per-Node x 10` 。
  * 例如，針對在 D14v2 SKU 上設定的叢集，其中每台機器都有16個虛擬核心，預設的查詢並行限制是 `16 cores x10 = 160` 。
* 您可以藉由建立支援票證來變更預設值。 未來，此控制項也會透過 control 命令公開。

## <a name="limit-on-result-set-size-result-truncation"></a>限制結果集大小（結果截斷）

**結果截斷**是查詢所傳回的結果集上預設設定的限制。 Kusto 會將傳回給用戶端的記錄數目限制為**500000**，而將這些記錄的整體記憶體設為**64 MB**。 當超過其中一項限制時，查詢就會失敗並出現「部分查詢失敗」。 超過整體記憶體將會產生例外狀況，其中包含下列訊息：

```
The Kusto DataEngine has failed to execute a query: 'Query result set has exceeded the internal data size limit 67108864 (E_QUERY_RESULT_SET_TOO_LARGE).'
```

超過記錄數目將會失敗，並出現例外狀況，指出：

```
The Kusto DataEngine has failed to execute a query: 'Query result set has exceeded the internal record count limit 500000 (E_QUERY_RESULT_SET_TOO_LARGE).'
```

有數個策略可處理此錯誤。

* 藉由修改查詢只傳回感興趣的資料，來減少結果集的大小。 當初始失敗的查詢太「寬」時，此策略非常有用。 例如，查詢不會將不需要的資料行投影出來。
* 將查詢後處理（例如匯總）移至查詢本身，以減少結果集的大小。 此策略適用于將查詢輸出送至另一個處理系統，然後再執行其他匯總的案例。
* 當您想要從服務匯出大量資料集時，請從查詢切換到使用[資料匯出](../management/data-export/index.md)。
* 指示服務抑制此查詢限制。

減少查詢所產生之結果集大小的方法包括：

* 使用[摘要運算子](../query/summarizeoperator.md)群組，並匯總查詢輸出中相似的記錄。 可能會使用[任何彙總函式](../query/any-aggfunction.md)來取樣一些資料行。
* 使用[take 運算子](../query/takeoperator.md)來取樣查詢輸出。
* 使用[substring 函數](../query/substringfunction.md)來修剪寬的自由文字資料行。
* 使用[專案運算子](../query/projectoperator.md)，從結果集中卸載任何不必要的資料行。

您可以使用 [要求] 選項來停用結果截斷 `notruncation` 。
我們建議您仍然將某種形式的限制放在原地。

例如：

```kusto
set notruncation;
MyTable | take 1000000
```

您也可以設定的值 `truncationmaxsize` （最大資料大小（以位元組為單位，預設為 64 MB）和 `truncationmaxrecords` （記錄數目上限，預設為500000），以更精確地控制結果截斷。 例如，下列查詢會將結果截斷設定為在1105記錄或1MB 發生，取兩者中已超過。

```kusto
set truncationmaxsize=1048576;
set truncationmaxrecords=1105;
MyTable | where User=="Ploni"
```

Kusto 用戶端程式庫目前會假設此限制存在。 雖然您可以增加不含界限的限制，但最終您會到達目前無法設定的用戶端限制。

不想要在單一大量提取所有資料的客戶，可以嘗試下列解決方法：
* 將某些 Sdk 切換至串流模式（KustoConnectionStringBuilder 上的串流 = true 屬性）
* 切換至 .NET v2 API，讓 Kusto 小組知道您是否遇到此問題，因此我們可以提高串流用戶端的優先順序。

Kusto 提供許多用戶端程式庫，可處理「無限大」的結果，方法是將它們串流至呼叫者。 使用其中一個程式庫，並將它設定為串流模式。 例如，使用 .NET Framework 用戶端（Kusto），並將連接字串的串流屬性設為*true*，或使用一律會串流結果的*ExecuteQueryV2Async （）* 呼叫。

預設會套用結果截斷，而不只是傳回給用戶端的結果資料流程。 根據預設，它也會套用至任何一個叢集在跨叢集查詢中發出至另一個叢集的子查詢，其效果類似。

## <a name="limit-on-memory-per-iterator"></a>每個反覆運算器的記憶體限制

「**每個結果集的記憶體上限」反覆運算器**是 Kusto 用來防止「失控」查詢的另一個限制。 這個限制（由 request 選項表示 `maxmemoryconsumptionperiterator` ）會設定單一查詢計劃結果集反覆運算器可以保存的記憶體數量上限。 這項限制適用于不是由本質進行資料流程處理的特定反覆運算器，例如 `join` 。）以下是發生這種情況時，將傳回的幾個錯誤訊息：

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

根據預設，此值設定為 5 GB。 您最多可以將這個值增加到電腦的一半實體記憶體：

```kusto
set maxmemoryconsumptionperiterator=68719476736;
MyTable | ...
```

考慮移除這些限制時，請先判斷您是否真的取得任何價值。 特別是，移除結果截斷限制表示您想要將大量資料移出 Kusto。
您可以使用命令來移除結果截斷限制， `.export` 或是在稍後進行匯總，在這種情況下，請考慮使用 Kusto 進行匯總。
讓 Kusto 小組知道您是否有任何一項建議的解決方案無法滿足的商務案例。  

在許多情況下，您可以藉由取樣資料集來避免超過此限制。 下列兩個查詢顯示如何進行取樣。 第一個是使用亂數字產生器的統計取樣。 第二種是具決定性的取樣，其方式是從資料集的某些資料行進行雜湊處理，通常是某些識別碼。

```kusto
T | where rand() < 0.1 | ...

T | where hash(UserId, 10) == 1 | ...
```

## <a name="limit-on-memory-per-node"></a>每個節點的記憶體限制

**每個節點的最大記憶體每個查詢**都是用來防止「失控」查詢的另一個限制。 這項限制（以要求選項表示 `max_memory_consumption_per_query_per_node` ）會設定可在特定查詢的單一節點上使用的記憶體數量上限。

```kusto
set max_memory_consumption_per_query_per_node=68719476736;
MyTable | ...
```

## <a name="limit-on-accumulated-string-sets"></a>累計字串集的限制

在各種查詢作業中，Kusto 需要「收集」字串值，並在開始產生結果之前于內部緩衝。 這些累積的字串集會有大小限制，以及它們可以保存的專案數。 此外，每個個別的字串都不應該超過特定限制。
超過這些限制的其中一個會導致下列其中一個錯誤：

```
Runaway query (E_RUNAWAY_QUERY). (message: 'Accumulated string array getting too large and exceeds the limit of ...GB (see https://aka.ms/kustoquerylimits)')

Runaway query (E_RUNAWAY_QUERY). (message: 'Accumulated string array getting too large and exceeds the maximum count of 2G items (see http://aka.ms/kustoquerylimits)')

Runaway query (E_RUNAWAY_QUERY). (message: 'Single string size shouldn't exceed the limit of 2GB (see http://aka.ms/kustoquerylimits)')
```

目前沒有任何參數可增加字串集大小上限。
因應措施是將查詢重新加以改寫，以減少必須緩衝處理的資料量。 您可以在不需要的資料行之前，先將其投影出來，例如聯結和摘要等運算子。 或者，您可以使用[隨機查詢](../query/shufflequery.md)策略。

## <a name="limit-execution-timeout"></a>限制執行超時

**伺服器超時**是套用至所有要求的服務端超時。
在 Kusto 中的多個點強制執行要求的時間（查詢和控制命令）：

* 用戶端程式庫（如果使用）
* 接受要求的服務端點
* 處理要求的服務引擎

根據預設，查詢的 timeout 設定為4分鐘，而控制命令則設為10分鐘。 如有需要，可以增加此值（上限為一小時）。

* 如果您使用 Kusto 進行查詢，請使用 [**工具**] [選項] [ &gt; **Options** *  &gt; **連接** &gt; **查詢伺服器超時**]。
* 以程式設計方式，將 `servertimeout` 用戶端要求屬性（類型的值 `System.TimeSpan` ）設定為一小時。

**有關超時的注意事項**

* 在用戶端上，會從所建立的要求中套用超時時間，直到回應開始到達用戶端為止。 在用戶端上讀取承載所花費的時間，並不會被視為超時的一部分。 這取決於呼叫端從資料流程提取資料的速度。
* 此外，在用戶端上，所使用的實際超時值稍微高於使用者要求的伺服器超時值。 這種差異是允許網路延遲。
* 若要自動使用允許的要求超時上限，請將用戶端要求屬性設定 `norequesttimeout` 為 `true` 。

<!--
  Request timeout can also be set using a set statement, but we don't mention
  it here since it shouldn't be used in production scenarios.
-->

## <a name="limit-on-query-cpu-resource-usage"></a>查詢 CPU 資源使用量的限制

Kusto 可讓您執行查詢，並使用與叢集一樣多的 CPU 資源。 如果有一個以上的執行，它會嘗試在查詢之間進行公平的迴圈配置資源。 此方法可為特定查詢產生最佳效能。
有時候，您可能會想要限制用於特定查詢的 CPU 資源。 例如，如果您執行「背景作業」，系統可能會容許較高的延遲，讓並行的臨機操作查詢具有高優先順序。

Kusto 支援在執行查詢時指定兩個[用戶端要求屬性](../api/netfx/request-properties.md)。 屬性為*query_fanout_threads_percent*和*query_fanout_nodes_percent*。
這兩個屬性都是預設為最大值（100）的整數，但可能會針對某個其他值的特定查詢減少。 

第一個*query_fanout_threads_percent*會控制執行緒使用的扇出因素。 當它是100% 時，叢集將會指派每個節點上的所有 Cpu。 例如，部署在 Azure D14 節點上的叢集上有16個 Cpu。 當它是50% 時，將會使用一半的 Cpu，依此類推。 數位會無條件進位到整個 CPU，因此可以安全地將它設定為0。 第二個是*query_fanout_nodes_percent*，它會控制叢集中有多少查詢節點，以供每個子查詢散發作業使用。 它的運作方式類似。

## <a name="limit-on-query-complexity"></a>查詢複雜度的限制

在查詢執行期間，查詢文字會轉換成代表查詢的關聯式運算子樹狀結構。
如果樹狀結構深度超過數千個層級的內部臨界值，則查詢會被視為太複雜而無法處理，而且會失敗並產生錯誤碼。 此失敗表示關聯式運算子樹狀結構超過其限制。
因為查詢中有多個連結在一起的二元運算子清單，所以超出了限制。 例如：

```kusto
T 
| where Column == "value1" or 
        Column == "value2" or 
        .... or
        Column == "valueN"
```

在此特定案例中，請使用運算子來重寫查詢 [`in()`](../query/inoperator.md) 。

```kusto
T 
| where Column in ("value1", "value2".... "valueN")
```
