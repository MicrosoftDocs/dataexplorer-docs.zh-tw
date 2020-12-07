---
title: 使用計量來監視 Azure 資料總管效能、健康情況 & 使用量
description: 瞭解如何使用 Azure 資料總管計量來監視叢集的效能、健康情況和使用量。
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: how-to
ms.date: 09/19/2020
ms.custom: contperfq1
ms.openlocfilehash: 153b4265aade03e4059db0b38362d217cdad90df
ms.sourcegitcommit: 2804e3fe40f6cf8e65811b00b7eb6a4f59c88a99
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2020
ms.locfileid: "96748411"
---
# <a name="monitor-azure-data-explorer-performance-health-and-usage-with-metrics"></a>使用計量來監視 Azure 資料總管效能、健康情況和使用量

Azure 資料總管計量提供 Azure 資料總管叢集資源的健康情況和效能的關鍵指標。 使用本文詳述的計量，在您的特定案例中監視 Azure 資料總管叢集使用量、健全狀況和效能，以作為獨立的計量。 您也可以使用計量作為操作 [Azure 儀表板](/azure/azure-portal/azure-portal-dashboards) 和 [Azure 警示](/azure/azure-monitor/platform/alerts-metric-overview)的基礎。

如需 Azure 計量瀏覽器的詳細資訊，請參閱 [計量瀏覽器](/azure/azure-monitor/platform/metrics-getting-started)。

## <a name="prerequisites"></a>先決條件

* Azure 訂用帳戶。 如果您沒有帳戶，可以建立一個免費的 [Azure 帳戶](https://azure.microsoft.com/free/)。
* 叢集 [和資料庫](create-cluster-database-portal.md)。

## <a name="use-metrics-to-monitor-your-azure-data-explorer-resources"></a>使用計量來監視您的 Azure 資料總管資源

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. 在 Azure 資料總管叢集的左側窗格中，搜尋 *度量*。
1. 選取 [ **計量** ] 以開啟 [計量] 窗格，並開始分析您的叢集。
    :::image type="content" source="media/using-metrics/select-metrics.gif" alt-text="搜尋並選取 Azure 入口網站中的計量":::

## <a name="work-in-the-metrics-pane"></a>在 [計量] 窗格中工作

在 [計量] 窗格中，選取要追蹤的特定度量、選擇如何匯總資料，以及建立要在儀表板上查看的度量圖表。

**資源** 和計量 **命名空間** 選擇器會針對您的 Azure 資料總管叢集預先選取。 下圖中的數位會對應至下列編號清單。 這些步驟會引導您完成設定和查看度量的不同選項。

![計量窗格](media/using-metrics/metrics-pane.png)

1. 若要 **建立度量圖表，請選取計量** 名稱和每個度量的相關 **匯總** 。 如需不同計量的詳細資訊，請參閱 [支援的 Azure 資料總管計量](#supported-azure-data-explorer-metrics)。
1. 選取 [ **新增度量** ] 以查看在相同圖表中繪製的多個度量。
1. 選取 [ **+ 新增圖表** ] 可在單一視圖中查看多個圖表。
1. 使用時間選擇器，將時間範圍變更 (預設：過去24小時) 。
1. 針對具有維度的度量，請使用 [ [**加入篩選** ] 和 [套用 **分割**](/azure/azure-monitor/platform/metrics-getting-started#apply-dimension-filters-and-splitting) ]。
1. 選取 [ **釘選到儀表板** ]，將您的圖表設定新增至儀表板，讓您可以再次查看。
1. 設定 **新的警示規則** ，以使用設定準則將計量視覺化。 新的警示規則將包含來自您圖表的目標資源、計量、分割及篩選維度。 在 [ [警示規則建立] 窗格](/azure/azure-monitor/platform/metrics-charts#create-alert-rules)中修改這些設定。

## <a name="supported-azure-data-explorer-metrics"></a>支援的 Azure 資料總管計量

Azure 資料總管計量讓您深入瞭解資源的整體效能和使用方式，以及特定動作（例如內嵌或查詢）的相關資訊。 本文中的計量已依使用量類型分組。 

計量的類型包括： 
* [群集計量](#cluster-metrics) 
* [匯出度量](#export-metrics) 
* [內嵌計量](#ingestion-metrics) 
* [串流內嵌計量](#streaming-ingest-metrics)
* [查詢計量](#query-metrics) 
* [具體化視圖計量](#materialized-view-metrics)

如需以字母順序列出 Azure 資料瀏覽器的 Azure 監視器計量清單，請參閱 [支援的 azure 資料總管叢集計量](/azure/azure-monitor/platform/metrics-supported#microsoftkustoclusters)。

## <a name="cluster-metrics"></a>群集計量

叢集計量會追蹤叢集的一般健全狀況。 例如，資源和內嵌使用和回應性。

|**計量** | **單位** | **聚集** | **度量說明** | **維度** |
|---|---|---|---|---|
| 快取使用率 | 百分比 | Avg、Max、Min | 叢集目前正在使用的已配置快取資源百分比。 快取是根據已定義的快取原則，配置給使用者活動的 SSD 大小。 <br> <br> 叢集可維持的狀態為 80% 或更少的平均快取使用率。 如果平均快取使用率高於80%，則叢集應該是 <br> [擴充](manage-cluster-vertical-scaling.md) 至儲存體優化定價層或 <br> [相應放大](manage-cluster-horizontal-scaling.md) 至多個實例。 或者，調整快取原則 (快取中的較少天數)。 如果快取使用率超過 100%，則根據快取原則所要快取的資料大小會大於叢集上快取的總大小。 | 無 |
| CPU | 百分比 | Avg、Max、Min | 叢集中的電腦目前正在使用的已配置計算資源百分比。 <br> <br> 叢集可維持的平均 CPU 為 80% 或更少。 CPU 的最大值是 100%，這表示沒有任何額外的計算資源可處理資料。 <br> 當叢集未正常執行時，請檢查 CPU 的最大值，以判斷是否有特定的 Cpu 被封鎖。 | 無 |
| 擷取使用率 | 百分比 | Avg、Max、Min | 用於從配置的總資源 (在容量原則中) 擷取資料以執行擷取的實際資源百分比。 預設容量原則是投入在擷取不超過 512 個並行擷取作業或 75% 的叢集資源。 <br> <br> 叢集可維持的狀態為 80% 或更少的平均擷取使用率。 擷取使用率的最大值是 100%，這表示會使用全部的叢集擷取能力，而且可能產生擷取佇列。 | 無 |
| 保持運作 | Count | 平均 | 追蹤叢集的回應性。 <br> <br> 完全回應的叢集傳回的值為 1，而已封鎖或已中斷連線的叢集則會傳回 0。 |
| 節流命令的總數 | 計數 | Avg、Max、Min、Sum | 因為已達到允許的並行 (平行) 命令數目上限，所以 (在叢集中拒絕) 命令的節流數目。 | 無 |
| 延伸區總數 | 計數 | Avg、Max、Min、Sum | 叢集中的資料範圍總數。 <br> <br> 此計量中的變更可能表示大量資料結構變更和叢集上的高負載，因為合併資料範圍是大量 CPU 的活動。 | 無 |

## <a name="export-metrics"></a>匯出度量

匯出計量會追蹤匯出作業的一般健全狀況和效能，例如延遲、結果、記錄數目和使用率。

|**計量** | **單位** | **聚集** | **度量說明** | **維度** |
|---|---|---|---|---|
匯出記錄的連續匯出數目    | 計數 | Sum | 所有連續匯出作業中匯出的記錄數目。 | ContinuousExportName |
連續匯出最大延遲 |    Count   | 最大值   | 延遲 (（分鐘），) 由叢集中的連續匯出工作所報告。 | 無 |
連續匯出暫止計數 | Count | 最大值   | 暫止連續匯出作業的數目。 這些作業已準備好執行，但在佇列中等候，可能是因為容量不足) 。 
連續匯出結果    | Count |   Count   | 每個連續匯出執行的失敗/成功結果。 | ContinuousExportName |
匯出使用率 |    百分比 | 最大值   | 已使用的匯出容量，在叢集中的總匯出容量 (介於0與100之間) 。 | 無 |

## <a name="ingestion-metrics"></a>內嵌計量

內嵌計量會追蹤像是延遲、結果和磁片區等內嵌作業的一般健全狀況和效能。

|**計量** | **單位** | **聚集** | **度量說明** | **維度** |
|---|---|---|---|---|
| Batch blob 計數 | 計數 | Avg、Max、Min | 已完成之批次中內嵌的資料來源數目。 | 資料庫 |
| 批次持續時間 | 秒 | Avg、Max、Min | 內嵌流程中批次處理階段的持續時間  | 資料庫 |
| 批次大小 | 位元組 | Avg、Max、Min | 內嵌的匯總批次中，未壓縮的預期資料大小。 | 資料庫 |
| 已處理的批次 | 計數 | Avg、Max、Min | 針對內嵌完成的批次數目。 `Batching Type`：批次是否達到批次處理時間、資料大小或檔案數目限制（依 [批次處理原則](./kusto/management/batchingpolicy.md)設定）。 | 資料庫，批次處理類型 |
| 探索延遲 | 秒 | Avg、Max、Min | 從資料佇列到資料連線探索為止的時間。 此時間未包含在 **Azure 資料總管的內嵌持續時間總數** 或 **KustoEventAge (內嵌延遲)** | 資料庫、資料表、資料連線類型、資料連線名稱 |
| 已處理的事件 (針對事件/IoT 中樞) | Count | 最大值、最小值、總和 | 從事件中樞讀取並由叢集處理的事件總數。 這些事件會分割成被拒絕的事件，以及叢集引擎所接受的事件。 | EventStatus |
| 內嵌延遲 | 秒 | Avg、Max、Min | 擷取資料時的延遲，從在叢集中收到資料的時間直到準備好進行查詢為止。 擷取延遲期間取決於擷取狀況。 | 無 |
| 擷取結果 | Count | Count | 失敗和成功的內嵌作業總數。 <br> <br> 使用 [套用 **分割**] 來建立成功和失敗結果的 bucket，並分析維度 (**值**  >  **狀態**) 。| IngestionResultDetails |
| 擷取量 (以 MB 為單位) | Count | Max、Sum | 在壓縮之前，內嵌至叢集 (的資料大小總計（MB）) 。 | 資料庫 |
| 階段延遲 | 秒 | Avg、Max、Min | 特定元件處理此批次資料的持續時間。 所有資料批次元件的階段延遲總計等於其內嵌延遲。 | 資料庫、資料連線類型、資料連線名稱|

## <a name="streaming-ingest-metrics"></a>串流內嵌計量

串流內嵌計量會追蹤串流內嵌資料和要求速率、持續時間和結果。

|**計量** | **單位** | **聚集** | **度量說明** | **維度** |
|---|---|---|---|---|
串流內嵌資料速率 |    Count   | RateRequestsPerSecond | 內嵌至叢集的總數據量。 | 無 |
串流內嵌持續時間   | 毫秒  | Avg、Max、Min | 所有串流內嵌要求的總持續時間。 | 無 |
串流內嵌要求率   | Count | Count、Avg、Max、Min、Sum | 串流內嵌要求的總數。 | 無 |
串流內嵌結果 | Count | 平均   | 依結果類型的串流內嵌要求總數。 | 結果 |

## <a name="query-metrics"></a>查詢計量

查詢效能計量會追蹤查詢持續時間，以及並行或節流查詢的總數。

|**計量** | **單位** | **聚集** | **度量說明** | **維度** |
|---|---|---|---|---|
| 查詢持續時間 | 毫秒 | Avg、Min、Max、Sum | 收到查詢結果之前的總時間 (不包括) 的網路延遲。 | QueryStatus |
| 並行查詢總數 | 計數 | Avg、Max、Min、Sum | 在叢集中平行執行的查詢數目。 此計量是估計叢集負載的好方法。 | 無 |
| 節流查詢總數 | 計數 | Avg、Max、Min、Sum | 在叢集中 (拒絕) 查詢的節流數目。 並行查詢原則中定義了允許的並行 (平行) 查詢數目上限。 | 無 |

## <a name="materialized-view-metrics"></a>具體化視圖計量

|**計量** | **單位** | **聚集** | **度量說明** | **維度** |
|---|---|---|---|---|
|MaterializedViewHealth                    | 1，0    | 平均     |  如果視圖被視為狀況良好，則值為1，否則為0。 | Database、MaterializedViewName |
|MaterializedViewAgeMinutes                | 分鐘 | 平均     | `age`視圖的是由目前時間所定義，減去 view 所處理的最後一個內嵌時間。 計量值是以分鐘為單位的時間， (值越低，則 view 是 "健康" ) 。 | Database、MaterializedViewName |
|MaterializedViewResult                    | 1       | 平均     | 度量包含的 `Result` 維度指出最後具體化週期的結果 (查看下列可能的值) 。 度量值一律等於1。 | 資料庫、MaterializedViewName、結果 |
|MaterializedViewRecordsInDelta            | 記錄計數 | 平均 | 目前在來源資料表未處理之部分中的記錄數目。 如需詳細資訊，請參閱 [具體化視圖的運作方式](./kusto/management/materialized-views/materialized-view-overview.md#how-materialized-views-work)| Database、MaterializedViewName |
|MaterializedViewExtentsRebuild            | 範圍計數 | 平均 | 具體化迴圈中重建的範圍數目。 | Database、MaterializedViewName|
|MaterializedViewDataLoss                  | 1       | 最大值    | 當未處理的來源資料接近保留時，就會引發度量。 | Database、MaterializedViewName、Kind |

## <a name="next-steps"></a>後續步驟

* [教學課程：在 Azure 資料總管中擷取和查詢監視資料](ingest-data-no-code.md)
* [使用診斷記錄來監視 Azure 資料總管擷取作業](using-diagnostic-logs.md)
* [快速入門：在 Azure 資料總管中查詢資料](web-query-data.md)
