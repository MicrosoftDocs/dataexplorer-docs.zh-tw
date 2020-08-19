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
ms.openlocfilehash: a9818f2efb6b48621c59619e89b3f2c9a4315e42
ms.sourcegitcommit: 5137a4291d70327b7bb874bbca74a4a386e57d32
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2020
ms.locfileid: "88566410"
---
# <a name="query-limits"></a>查詢限制

Kusto 是一種臨機操作查詢引擎，可裝載大型資料集，並藉由將所有相關資料保留在記憶體中，來嘗試滿足查詢。
有一種固有的風險，查詢會獨佔服務資源，而不會有界限。 Kusto 以預設查詢限制的形式提供一些內建保護。 如果您考慮移除這些限制，請先判斷您是否真的得到任何價值。

## <a name="limit-on-query-concurrency"></a>查詢平行存取限制

「**查詢並行**存取」是一種限制，可讓叢集在許多同時執行的查詢上強加。

* 查詢平行存取限制的預設值取決於其執行所在的 SKU 叢集，且計算方式如下： `Cores-Per-Node x 10` 。
  * 例如，針對在 D14v2 SKU 上設定的叢集，其中每部電腦都有16虛擬核心，預設的查詢平行存取限制為 `16 cores x10 = 160` 。
* 您可以藉由建立支援票證來變更預設值。 未來，此控制項也會透過 control 命令公開。

## <a name="limit-on-result-set-size-result-truncation"></a>結果集大小 (結果截斷的限制) 

**結果截斷** 是在查詢所傳回的結果集上預設設定的限制。 Kusto 會將傳回給用戶端的記錄數目限制為 **500000**，並將這些記錄的整體記憶體限制為 **64 MB**。 當超過其中一項限制時，查詢就會失敗，並出現「部分查詢失敗」。 超過整體記憶體將會產生例外狀況，並顯示下列訊息：

```
The Kusto DataEngine has failed to execute a query: 'Query result set has exceeded the internal data size limit 67108864 (E_QUERY_RESULT_SET_TOO_LARGE).'
```

超過記錄數目會失敗，並出現例外狀況，指出：

```
The Kusto DataEngine has failed to execute a query: 'Query result set has exceeded the internal record count limit 500000 (E_QUERY_RESULT_SET_TOO_LARGE).'
```

有許多策略可以處理這個錯誤。

* 藉由將查詢修改為只傳回感興趣的資料，來減少結果集大小。 當初始失敗查詢太「寬」時，此策略會很有用。 例如，查詢不會將不需要的資料行投射在外。
* 將後置查詢處理（例如匯總）移至查詢本身，以減少結果集大小。 當查詢的輸出會送到另一個處理系統，然後執行其他匯總時，此策略會很有用。
* 當您想要從服務匯出大量資料集時，請從查詢切換到使用 [資料匯出](../management/data-export/index.md) 。
* 指示服務抑制此查詢限制。

減少查詢所產生之結果集大小的方法包括：

* 使用「 [摘要運算子](../query/summarizeoperator.md) 」群組，並在查詢輸出中匯總類似的記錄。 可能會使用 [任何彙總函式](../query/any-aggfunction.md)來取樣一些資料行。
* 使用 [take 運算子](../query/takeoperator.md) 來取樣查詢輸出。
* 使用 [substring 函數](../query/substringfunction.md) 可修剪寬的任意文字資料行。
* 使用 [專案運算子](../query/projectoperator.md) ，從結果集中卸載任何不重要的資料行。

您可以使用 request 選項來停用結果截斷 `notruncation` 。
我們建議還是有某種形式的限制。

例如：

```kusto
set notruncation;
MyTable | take 1000000
```

您也可以藉由設定 [ `truncationmaxsize` (的資料大小上限（以位元組為單位）] 的值、預設為 64 MB) 和 `truncationmaxrecords` (最大記錄數目（預設為 500000) ），對結果截斷進行更精細的控制。 例如，下列查詢會將結果截斷設定為1105記錄或1MB （以超過者為准）。

```kusto
set truncationmaxsize=1048576;
set truncationmaxrecords=1105;
MyTable | where User=="Ploni"
```

移除結果截斷限制表示您想要將大量資料移出 Kusto。

您可以使用 `.export` 命令或針對稍後的匯總，移除結果截斷限制，以供匯出之用。 如果您選擇 [稍後匯總]，請考慮使用 Kusto 進行匯總。

讓 Kusto 小組知道您是否有任何一項建議解決方案無法滿足的商務案例。  

Kusto 用戶端程式庫目前假設有此限制存在。 雖然您可以在沒有界限的情況下增加限制，但最終您會達到目前無法設定的用戶端限制。

不想要將所有資料提取到單一大量的客戶可以嘗試下列因應措施：
* 在 KustoConnectionStringBuilder 上將某些 Sdk 切換至串流模式 (串流 = true 屬性) 
* 切換至 .NET v2 API

讓 Kusto 小組知道您是否遇到此問題，因此我們可以提高串流用戶端優先順序。

Kusto 提供數個用戶端程式庫，可處理「無限大」結果，方法是將它們串流至呼叫端。 使用其中一個程式庫，並將它設定為串流模式。 例如，使用 .NET Framework 用戶端 (Kusto) 並將連接字串的串流屬性設為 *true*，或使用一律串流結果的 *ExecuteQueryV2Async ( # B3 * 呼叫。

預設會套用結果截斷，而不只是傳回給用戶端的結果資料流程。 它也會預設套用至任何子查詢，其中一個叢集會在跨叢集查詢中對另一個叢集發出問題，並具有類似的效果。

## <a name="limit-on-memory-per-iterator"></a>每個反覆運算器的記憶體限制

**最大記憶體每個結果集反覆運算器** 是 Kusto 用來防止「失控」查詢的另一項限制。 這項限制（以 request 選項表示 `maxmemoryconsumptionperiterator` ）會設定單一查詢計劃結果集反覆運算器可保存之記憶體數量的上限。 這項限制適用于不是依本質進行資料流程處理的特定反覆運算器（例如） `join` ) 。在此情況發生時，以下是一些將會傳回的錯誤訊息：

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

依預設，此值設定為 5 GB。 您可以增加此值，最多可達電腦的一半實體記憶體：

```kusto
set maxmemoryconsumptionperiterator=68719476736;
MyTable | ...
```

在許多情況下，您可以藉由取樣資料集來避免超過此限制。 下列兩個查詢示範如何進行取樣。 第一個是統計取樣，) 使用亂數產生器。 第二種是具決定性的取樣，藉由從資料集的某個資料行進行雜湊處理（通常是一些識別碼）來完成。

```kusto
T | where rand() < 0.1 | ...

T | where hash(UserId, 10) == 1 | ...
```

## <a name="limit-on-memory-per-node"></a>每個節點的記憶體限制

**每個節點的 [每個查詢的記憶體上限** ] 是用來防止「失控」查詢的另一個限制。 這項限制（以 request 選項表示 `max_memory_consumption_per_query_per_node` ）會設定可在特定查詢的單一節點上使用的記憶體數量上限。

```kusto
set max_memory_consumption_per_query_per_node=68719476736;
MyTable | ...
```

## <a name="limit-on-accumulated-string-sets"></a>累積字串集的限制

在各種查詢作業中，Kusto 需要「收集」字串值，並在內部緩衝它們，才能開始產生結果。 這些累積字串集的大小和可保存的專案數有所限制。 此外，每個個別的字串不應超過特定的限制。
超過其中一項限制會導致下列其中一個錯誤：

```
Runaway query (E_RUNAWAY_QUERY). (message: 'Accumulated string array getting too large and exceeds the limit of ...GB (see https://aka.ms/kustoquerylimits)')

Runaway query (E_RUNAWAY_QUERY). (message: 'Accumulated string array getting too large and exceeds the maximum count of 2G items (see http://aka.ms/kustoquerylimits)')

Runaway query (E_RUNAWAY_QUERY). (message: 'Single string size shouldn't exceed the limit of 2GB (see http://aka.ms/kustoquerylimits)')
```

目前沒有任何開關可增加字串集大小上限。
因應措施是，改寫查詢來減少必須緩衝處理的資料量。 您可以先投射不必要的資料行，再由運算子（例如聯結和摘要）使用。 或者，您可以使用 [隨機的查詢](../query/shufflequery.md) 策略。

## <a name="limit-execution-timeout"></a>限制執行超時

**伺服器超時** 是套用至所有要求的服務端超時時間。
執行要求的超時時間 (查詢和控制命令) 在 Kusto 中的多個點強制執行：

* 使用的用戶端程式庫 () 
* 接受要求的服務端點
* 處理要求的服務引擎

根據預設，查詢的 timeout 設定為四分鐘，控制命令的時間為10分鐘。 如果需要 (上限為一小時) ，就可以增加這個值。

* 如果您使用 Kusto 進行查詢，請使用**工具** &gt; **選項** *  &gt; **連接** &gt; **查詢伺服器超時**。
* 以程式設計的方式，將 `servertimeout` 用戶端要求屬性設定為類型的值 `System.TimeSpan` ，最多可達一小時。

**有關超時的注意事項**

* 在用戶端上，會從所建立的要求套用超時時間，直到回應開始到達用戶端為止。 在用戶端上讀取回裝載所花費的時間，不會被視為超時時間的一部分。 這取決於呼叫端從資料流程提取資料的速度。
* 此外，在用戶端上，使用的實際超時值稍微高於使用者要求的伺服器超時值。 這項差異是為了允許網路延遲。
* 若要自動使用允許的要求超時上限，請將用戶端要求屬性設定 `norequesttimeout` 為 `true` 。

<!--
  Request timeout can also be set using a set statement, but we don't mention
  it here since it shouldn't be used in production scenarios.
-->

## <a name="limit-on-query-cpu-resource-usage"></a>查詢 CPU 資源使用量的限制

Kusto 可讓您執行查詢，並使用與叢集一樣多的 CPU 資源。 如果有多個執行中的查詢，它會嘗試在查詢之間進行公平的迴圈配置資源。 此方法可為特定查詢產生最佳效能。
有時，您可能會想要限制特定查詢所使用的 CPU 資源。 例如，如果您執行「背景工作」，系統可能會容許較高的延遲，以提供高優先順序的並行臨機操作查詢。

Kusto 支援在執行查詢時指定兩個 [用戶端要求屬性](../api/netfx/request-properties.md) 。 屬性為  *query_fanout_threads_percent* 和 *query_fanout_nodes_percent*。
這兩個屬性都是預設為最大值 (100) 的整數，但可能會針對特定查詢減少為某個其他值。 

第一個（ *query_fanout_threads_percent*）會控制執行緒使用的扇出因素。 當它是100% 時，叢集將會指派每個節點上的所有 Cpu。 例如，部署在 Azure D14 節點上的叢集上有16個 Cpu。 當它是50% 時，就會使用一半的 Cpu，依此類推。 數位會無條件進位至整個 CPU，因此可以安全地將它設定為0。 第二個 *query_fanout_nodes_percent*，則是控制叢集中要針對每個子查詢散發作業使用的查詢節點數目。 它會以類似的方式運作。

## <a name="limit-on-query-complexity"></a>查詢複雜度的限制

在查詢執行期間，會將查詢文字轉換成代表查詢的關聯式運算子樹狀結構。
如果樹狀目錄深度超過數千個層級的內部臨界值，則會將查詢視為太複雜而無法處理，並會失敗並出現錯誤碼。 失敗表示關聯式運算子樹狀結構超過其限制。
因為有大量二元運算子清單的查詢已連結在一起，所以已超過限制。 例如：

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
