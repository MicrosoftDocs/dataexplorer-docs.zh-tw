---
title: 具體化視圖-Azure 資料總管
description: 本文說明 Azure 資料總管中的具體化觀點。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/30/2020
ms.openlocfilehash: 407db347d4d21450d5648fe8716e2d82553a9669
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96320650"
---
# <a name="materialized-views-preview"></a> (預覽) 具體化視圖

[具體化視圖](../../query/materialized-view-function.md) 會對來源資料表公開 *聚合* 查詢。 具體化視圖一律會傳回匯總查詢的最新結果， (一律是最新的) 。 [查詢具體化視圖](#materialized-views-queries) 比直接在來源資料表上執行匯總（執行每個查詢）的效能更高。

> [!NOTE]
> 具體化視圖有一些 [限制](materialized-view-create.md#limitations-on-creating-materialized-views)，而且不保證適用于所有案例。 使用此功能之前，請先參閱 [效能考慮](#performance-considerations) 。

使用下列命令來管理具體化視圖：
* [`.create materialized-view`](materialized-view-create.md)
* [`.alter materialized-view`](materialized-view-alter.md)
* [`.drop materialized-view`](materialized-view-drop.md)
* [`.disable | .enable materialized-view`](materialized-view-enable-disable.md)
* [`.show materialized-views commands`](materialized-view-show-commands.md)

## <a name="why-use-materialized-views"></a>為何要使用具體化視圖？

藉由投資資源 (資料存放區、背景 CPU 週期) ，以針對常用匯總的具體化視圖，獲得下列優點：

* **效能改進：** 查詢具體化視圖通常比查詢相同彙總函式 (s) 的來源資料表更好。

* **時效性：** 具體化視圖查詢一律會傳回最新的結果，而不受具體化最後一次發生的時間影響。 此查詢會將視圖的具體化部分與來源資料表中的記錄結合，而這些記錄尚未具體化 (`delta` 元件) ，一律提供最新的結果。

* **減少成本：** [查詢具體化視圖](#materialized-views-queries) 會從叢集取用較少的資源，而不是對來源資料表進行匯總。 如果只需要匯總，則可以減少來源資料表的保留原則。 此設定可減少來源資料表的經常性存取快取成本。

## <a name="materialized-views-use-cases"></a>具體化視圖使用案例

以下是使用具體化 view 可以解決的常見案例：

* 使用[ `arg_max()` (彙總函式) ](../../query/arg-max-aggfunction.md)，查詢每個實體的最後一筆記錄。
* 使用[ `any()` (彙總函式) ](../../query/any-aggfunction.md)，在資料表中取消重複的記錄。
* 藉由計算原始資料的定期統計資料，來減少資料的解決方式。 依時間週期使用各種 [彙總函式](materialized-view-create.md#supported-aggregation-functions) 。
    * 例如，用 `T | summarize dcount(User) by bin(Timestamp, 1d)` 來維護每天不同使用者的最新快照集。

如需所有使用案例的範例，請參閱 [具體化 view create 命令](materialized-view-create.md#examples)。

## <a name="how-materialized-views-work"></a>具體化視圖的運作方式

具體化視圖是由兩個元件所組成：

* *具體化* 元件-包含來源資料表中已處理之匯總記錄的 Azure 資料總管資料表。  此資料表一律會依匯總的群組保存單一記錄。
* *差異*-來源資料表中尚未處理的新內嵌記錄。

查詢具體化視圖會結合具體化元件與 delta 部分，提供匯總查詢的最新結果。 離線具體化進程會從 *差異* 內嵌至具體化資料表的新記錄，並取代現有的記錄。 取代是藉由重建保存要取代之記錄的範圍來完成。 如果 *差異* 中的記錄與 *具體化* 元件中的所有資料分區持續相交，則每個具體化迴圈都需要重建整個 *具體化* 元件，而且可能不會跟上內建的速率。 在此情況下，此視圖會變成狀況不良，且 *差異* 會不斷成長。
[ [監視](#materialized-views-monitoring) ] 區段說明如何針對這類情況進行疑難排解。

## <a name="materialized-views-queries"></a>具體化視圖查詢

查詢具體化視圖的主要方式是使用它的名稱，例如查詢資料表參考。 查詢具體化視圖時，會將視圖的具體化部分與尚未具體化的來源資料表中的記錄結合。 根據內嵌到來源資料表的所有記錄，查詢具體化視圖一律會傳回最新的結果。 如需具體化視圖元件細目的詳細資訊，請參閱 [具體化視圖的運作方式](#how-materialized-views-work)。 

查詢檢視的另一種方式是使用[ `materialized_view()` 函數](../../query/materialized-view-function.md)。 此選項支援只查詢檢視的具體化部分，同時指定使用者願意容忍的延遲上限。 此選項不保證會傳回最新的記錄，但其效能應該比查詢整個視圖的效能更高。 如果您願意犧牲某些有效的效能（例如，遙測儀表板），此函式很有用。

此視圖可以參與跨叢集或跨資料庫的查詢，但不會包含在萬用字元等位或搜尋中。

### <a name="examples"></a>範例

1. 查詢整個視圖。 來源資料表中的最新記錄包含：
    
    <!-- csl -->
    ```kusto
    ViewName
    ```

1. 只查詢檢視的具體化部分，不論它上次具體化的時間。 

    <!-- csl -->
    ```kusto
    materialized_view("ViewName")
    ```
  
## <a name="performance-considerations"></a>效能考量

可能影響具體化 view health 的主要參與者如下：

* 叢集 **資源：** 如同在叢集上執行的任何其他進程，具體化視圖會使用叢集 (CPU、記憶體) 的資源。 如果叢集已超載，將具體化視圖新增至其中可能會導致叢集的效能降低。 使用叢集 [健康情況計量](../../../using-metrics.md#cluster-metrics)來監視叢集的健康情況。 [優化自動](../../../manage-cluster-horizontal-scaling.md#optimized-autoscale) 調整目前不會在自動調整規則中納入考慮的具體化 view health。

* **與具體化資料重迭：** 在具體化期間，自從上一次具體化 (差異) 之後，所有新的記錄都會內嵌到來源資料表。 新記錄與已具體化記錄之間的交集越高，具體化視圖的效能就越糟。 如果要更新的記錄數目 (例如，在 `arg_max` view) 是來源資料表的一小部分，則具體化視圖的效果最佳。 如果所有或大部分的具體化視圖記錄都必須在每個具體化迴圈中更新，具體化視圖就無法正常執行。 使用 [範圍重建計量](../../../using-metrics.md#materialized-view-metrics) 來識別這種情況。

* 內嵌 **速率：** 在具體化視圖的來源資料表中，資料量或內嵌速率沒有硬式編碼的限制。 不過，具體化視圖的建議內嵌速率不超過每秒 1 GB。較高的內嵌速率可能仍能順利執行。 效能取決於叢集大小、可用的資源，以及與現有資料的交集數量。

* 叢集中 **的具體化視圖數目：** 上述考慮適用于叢集中定義的每個個別具體化 view。 每個視圖都會取用自己的資源，而許多視圖會在可用的資源上互相競爭。 叢集中的具體化視圖數目沒有硬式編碼的限制。 不過，一般建議是在叢集上沒有超過10個具體化視圖。 如果叢集中定義了一個以上的具體化視圖，則可以調整 [容量原則](../capacitypolicy.md#materialized-views-capacity-policy) 。

* **具體化視圖定義**：具體化 view 定義必須根據查詢最佳做法來定義，以獲得最佳查詢效能。 如需詳細資訊，請參閱 [建立命令效能提示](materialized-view-create.md#performance-tips)。

## <a name="materialized-views-policies"></a>具體化視圖原則

您可以定義具體化視圖的 [保留原則](../retentionpolicy.md) 和快取 [原則](../cachepolicy.md) ，就像任何 Azure 資料總管資料表一樣。

具體化視圖預設會衍生資料庫保留和快取原則。 您可以使用 [保留原則控制命令](../retention-policy.md) 或快取 [原則控制命令](../cache-policy.md)來變更原則。
   
   * 具體化視圖的保留原則與來源資料表的保留原則無關。
   * 如果未使用來源資料表記錄，則可以將來源資料表的保留原則降至最低。 具體化視圖仍會根據在 view 上設定的保留原則來儲存資料。 
   * 當具體化視圖處於預覽模式時，建議您至少允許最少七天，並將復原能力設定為 true。 此設定可讓您快速復原錯誤和診斷用途。
    
> [!NOTE]
> 目前不支援來源資料表的保留原則為零。

## <a name="materialized-views-monitoring"></a>具體化視圖監視

以下列方式監視具體化視圖的健康情況：

* 監視 Azure 入口網站中的 [具體化 view 度量](../../../using-metrics.md#materialized-view-metrics) 。
* 監視 `IsHealthy` 從傳回的屬性 [`.show materialized-view`](materialized-view-show-commands.md#show-materialized-view) 。
* 使用來檢查失敗 [`.show materialized-view failures`](materialized-view-show-commands.md#show-materialized-view-failures) 。

> [!NOTE]
> 具體化永遠不會略過任何資料，即使有常數失敗也是一樣。 根據來源資料表中的所有記錄，一律保證會傳回查詢的最新快照集。 常數失敗會大幅降低查詢效能，但不會在 view 查詢中造成不正確的結果。

### <a name="track-resource-consumption"></a>追蹤資源耗用量

**具體化視圖資源耗用量：** 可使用命令追蹤具體化 view 具體化進程所耗用的資源 [`.show commands-and-queries`](../commands-and-queries.md#show-commands-and-queries) 。 使用下列 (取代和) 來篩選特定視圖的記錄 `DatabaseName` `ViewName` ：

<!-- csl -->
```
.show commands-and-queries 
| where Database  == "DatabaseName" and ClientActivityId startswith "DN.MaterializedViews;ViewName;"
```

### <a name="troubleshooting-unhealthy-materialized-views"></a>針對狀況不良的具體化視圖進行疑難排解

`MaterializedViewHealth`度量指出具體化視圖是否狀況良好。 具體化視圖可能會因為下列任何或所有原因而變成狀況不良：
* 具體化進程失敗。
* 叢集沒有足夠的容量可以即時具體化所有傳入的資料。 執行失敗。 不過，此視圖仍會處於狀況不良的狀態，因為它將會延遲後，且無法跟上內嵌速率。

在具體化視圖變成狀況不良之前，其存留期（依計量注明 `MaterializedViewAgeMinutes` ）將逐漸增加。

### <a name="troubleshooting-examples"></a>疑難排解範例

下列範例可協助您診斷及修正狀況不良的視圖：

* **失敗：** 來源資料表已變更或刪除、view 未設定為 `autoUpdateSchema` ，或自動更新不支援來源資料表中的變更。 <br>
   **診斷：** `MaterializedViewResult`會引發計量，並將 `Result` 維度設定為 `SourceTableSchemaChange` / `SourceTableNotFound` 。

* **失敗：** 具體化程式因為叢集資源不足而失敗，並達到查詢限制。 <br>
  **診斷：** `MaterializedViewResult` 度量 `Result` 維度設定為 `InsufficientResources` 。 Azure 資料總管會嘗試從此狀態自動復原，因此此錯誤可能是暫時性的。 但是，如果 view 狀況不良，而且經常發出此錯誤，則目前叢集的設定可能無法趕上內嵌速率，且需要相應增加或相應放大叢集。

* **失敗：** 具體化進程因為任何其他 (未知的) 原因而失敗。 <br> 
   **診斷**：計量 `MaterializedViewResult` 將會 `Result` 是 `UnknownError` 。 如果經常發生此失敗，請為 Azure 資料總管小組開啟支援票證，以進一步調查。

如果沒有具體化失敗， `MaterializedViewResult` 則會在每次成功執行時引發計量，並使用 `Result` = `Success` 。 具體化視圖可能會處於狀況不良的狀態，但如果成功執行，則為延遲後的 (高於 `Age` 閾值) 。 這種情況可能會在下列情況發生：
   * 具體化的速度很慢，因為每個具體化迴圈中有太多要重建的範圍。 若要深入瞭解範圍重建對視圖效能有何影響，請參閱 [具體化視圖的運作方式](#how-materialized-views-work)。 
   * 如果每個具體化週期都需要重建接近視野中的範圍（100%），則該視圖可能不會保持啟動，而且會變成狀況不良。 度量中提供每個迴圈中重建的範圍數目 `MaterializedViewExtentsRebuild` 。 在 [具體化視圖容量原則](../capacitypolicy.md#materialized-views-capacity-policy) 中增加平行存取的範圍，在此情況下也可能有所説明。 
   * 叢集中有其他具體化視圖，而且叢集沒有足夠的容量來執行所有的視圖。 請參閱 [具體化視圖容量原則](../capacitypolicy.md#materialized-views-capacity-policy) ，以變更並存執行之具體化視圖數目的預設設定。

## <a name="next-steps"></a>後續步驟

* [`.create materialized view`](materialized-view-create.md)
* [`.alter materialized-view`](materialized-view-alter.md)
* [具體化視圖顯示命令](materialized-view-show-commands.md)
