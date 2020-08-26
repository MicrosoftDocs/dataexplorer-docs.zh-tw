---
title: 使用計量來監視 Azure 資料總管效能、健康情況 & 使用量
description: 瞭解如何使用 Azure 資料總管計量來監視叢集的效能、健康情況和使用量。
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: how-to
ms.date: 01/19/2020
ms.openlocfilehash: 0d1b570bc1fac901af67d9dd9602ef6864cb8336
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88875339"
---
# <a name="monitor-azure-data-explorer-performance-health-and-usage-with-metrics"></a>使用計量來監視 Azure 資料總管效能、健康情況和使用量

Azure 資料總管是快速、完全受控的資料分析服務，可即時分析來自應用程式、網站、IoT 裝置等的大量資料流。 若要使用 Azure 資料總管，請先建立叢集，然後在該叢集中建立一或多個資料庫。 然後將資料內嵌 (載入) 至資料庫，讓您可以對資料執行查詢。 Azure 資料總管計量提供叢集資源健康情況和效能的關鍵指標。 使用本文詳述的計量，在您的特定案例中監視 Azure 資料總管叢集健康情況和效能，作為獨立的計量。 您也可以使用計量作為操作 [Azure 儀表板](/azure/azure-portal/azure-portal-dashboards) 和 [Azure 警示](/azure/azure-monitor/platform/alerts-metric-overview)的基礎。

## <a name="prerequisites"></a>先決條件

* Azure 訂用帳戶。 如果您沒有帳戶，可以建立一個免費的 [Azure 帳戶](https://azure.microsoft.com/free/)。
* 叢集 [和資料庫](create-cluster-database-portal.md)。

## <a name="using-metrics"></a>使用計量

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. 在您的 Azure 資料總管叢集中，選取 [ **計量** ] 以開啟 [計量] 窗格，並開始分析您的叢集。
    ![選取 [計量] ](media/using-metrics/select-metrics.png) 。
1. 在 [計量] 窗格中： [  ![ 計量] 窗格](media/using-metrics/metrics-pane.png)
    1. 若要 **建立度量圖表，請選取計量** 名稱和每個度量的相關 **匯總** 。 **資源**和計量**命名空間**選擇器會針對您的 Azure 資料總管叢集預先選取。 如需不同計量的詳細資訊，請參閱 [支援的 Azure 資料總管計量](#supported-azure-data-explorer-metrics)。
    1. 選取 [ **新增度量** ] 以查看在相同圖表中繪製的多個度量。
    1. 選取 [ **+ 新增圖表** ] 可在單一視圖中查看多個圖表。
    1. 使用時間選擇器，將時間範圍變更 (預設：過去24小時) 。
    1. 針對具有維度的度量，請使用 [ [**加入篩選** ] 和 [套用 **分割**](/azure/azure-monitor/platform/metrics-getting-started#apply-dimension-filters-and-splitting) ]。
    1. 選取 [ **釘選到儀表板** ]，將您的圖表設定新增至儀表板，讓您可以再次查看。
    1. 設定 **新的警示規則** ，以使用設定準則將計量視覺化。 新的警示規則將包含來自您圖表的目標資源、計量、分割及篩選維度。 在 [ [警示規則建立] 窗格](/azure/azure-monitor/platform/metrics-charts#create-alert-rules)中修改這些設定。

使用 [計量瀏覽器](/azure/azure-monitor/platform/metrics-getting-started)的其他資訊。

## <a name="supported-azure-data-explorer-metrics"></a>支援的 Azure 資料總管計量

根據使用量，支援的 Azure 資料總管計量會分成各種不同的類別。 

### <a name="cluster-health-metrics"></a>叢集健康情況計量

叢集健全狀況計量會追蹤叢集的一般健全狀況。 這包括資源和內嵌的使用率和回應性。

**計量** | **單位** | **聚集** | **度量說明** | **維度** |
|---|---|---|---|---|
| 快取使用率 | 百分比 | Avg、Max、Min | 叢集目前正在使用的已配置快取資源百分比。 Cache 是根據定義的快取原則，為使用者活動配置的 SSD 大小。 平均快取使用率80% 或更低是叢集的持續性狀態。 如果平均快取使用率高於80%，則應將叢集相應 [增加](manage-cluster-vertical-scaling.md) 到儲存體優化定價層 [，或相應放大至](manage-cluster-horizontal-scaling.md) 更多實例。 或者，在快取) 中調整快取原則 (較少的天數。 如果快取使用率超過100%，則根據快取原則快取的資料大小，會大於叢集上的快取大小總計。 | 無 |
| CPU | 百分比 | Avg、Max、Min | 叢集中的電腦目前正在使用的已配置計算資源百分比。 叢集的平均 CPU （80% 或更低）是可持續的。 CPU 的最大值是100%，這表示沒有額外的計算資源可處理資料。 當叢集未正常執行時，請檢查 CPU 的最大值，以判斷是否有特定的 Cpu 被封鎖。 | 無 |
| 擷取使用率 | 百分比 | Avg、Max、Min | 實際資源百分比，用來從配置的總資源（在容量原則中）內嵌資料，以執行內嵌。 預設容量原則不超過512的並行內嵌作業，或投資于內嵌的叢集資源75%。 80% 或更低的平均內嵌使用率是叢集的持續性狀態。 內嵌使用率的最大值為100%，這表示會使用所有叢集內嵌功能，並可能產生內嵌佇列。 | 無 |
| 保持運作 | Count | Avg | 追蹤叢集的回應性。 完全回應的叢集會傳回值1，且封鎖或中斷連線的叢集會傳回0。 |
| 節流命令的總數 | Count | Avg、Max、Min、Sum | 因為已達到允許的並行 (平行) 命令數目上限，所以 (在叢集中拒絕) 命令的節流數目。 | 無 |
| 延伸區總數 | Count | Avg、Max、Min、Sum | 叢集中的資料範圍總數。 此計量中的變更可能表示大量資料結構變更和叢集上的高負載，因為合併資料範圍是大量 CPU 的活動。 | 無 |
| | | | |

### <a name="export-health-and-performance-metrics"></a>匯出健康情況和效能計量

匯出健康情況和效能計量會追蹤匯出作業的一般健全狀況和效能，例如延遲、結果、記錄數目和使用率。

**計量** | **單位** | **聚集** | **度量說明** | **維度** |
|---|---|---|---|---|
匯出記錄的連續匯出數目    | Count | Sum | 所有連續匯出作業中匯出的記錄數目。 | 無 |
連續匯出最大延遲 |    Count   | 最大值   | 延遲 (（分鐘），) 由叢集中的連續匯出工作所報告。 | 無 |
連續匯出暫止計數 | Count | 最大值   | 暫止連續匯出作業的數目。 這些作業已準備好執行，但在佇列中等候，可能是因為容量不足) 。 
連續匯出結果    | Count |   Count   | 每個連續匯出執行的失敗/成功結果。 | ContinuousExportName |
匯出使用率 |    百分比 | 最大值   | 已使用的匯出容量，在叢集中的總匯出容量 (介於0與100之間) 。 | 無 |
| | | | |

### <a name="ingestion-health-and-performance-metrics"></a>內嵌健康情況和效能計量

內嵌健全狀況和效能計量會追蹤內嵌作業的一般健全狀況和效能，例如延遲、結果和磁片區。

**計量** | **單位** | **聚集** | **度量說明** | **維度** |
|---|---|---|---|---|
| Batch blob 計數 | Count | Avg、Max、Min | 已完成之批次中內嵌的資料來源數目。 | 資料庫 |
| 批次持續時間 | 秒 | Avg、Max、Min | 內嵌流程中批次處理階段的持續時間  | 資料庫 |
| 批次大小 | 位元組 | Avg、Max、Min | 內嵌的匯總批次中，未壓縮的預期資料大小。 | 資料庫 |
| 已處理的批次 | Count | Avg、Max、Min | 針對內嵌完成的批次數目。 `BatchCompletionReason`：批次是否達到批次處理時間、資料大小或檔案數目限制（依 [批次處理原則](/azure/data-explorer/kusto/management/batchingpolicy)設定）。 | Database、BatchCompletionReason |
| 已處理的事件 (針對事件/IoT 中樞) | Count | 最大值、最小值、總和 | 從事件中樞讀取並由叢集處理的事件總數。 這些事件會分割成被拒絕的事件，以及叢集引擎所接受的事件。 | EventStatus |
| 內嵌延遲 | 秒 | Avg、Max、Min | 資料內嵌的延遲時間，從叢集中收到資料到準備進行查詢為止。 內嵌延遲期間取決於內嵌案例。 | 無 |
| 擷取結果 | Count | Count | 失敗和成功的內嵌作業總數。 使用 [套用**分割**] 來建立成功和失敗結果的 bucket，並分析維度 (**值**  >  **狀態**) 。| IngestionResultDetails |
| 擷取量 (以 MB 為單位) | Count | Max、Sum | 在壓縮之前，內嵌至叢集 (的資料大小總計（MB）) 。 | 資料庫 |
| | | | |  

### <a name="query-performance"></a>查詢效能

查詢效能計量會追蹤查詢持續時間，以及並行或節流查詢的總數。

**計量** | **單位** | **聚集** | **度量說明** | **維度** |
|---|---|---|---|---|
| 查詢持續時間 | 毫秒 | Avg、Min、Max、Sum | 收到查詢結果之前的總時間 (不包括) 的網路延遲。 | QueryStatus |
| 並行查詢總數 | Count | Avg、Max、Min、Sum | 在叢集中平行執行的查詢數目。 此計量是估計叢集負載的好方法。 | 無 |
| 節流查詢總數 | Count | Avg、Max、Min、Sum | 在叢集中 (拒絕) 查詢的節流數目。 並行查詢原則中定義了允許的並行 (平行) 查詢數目上限。 | 無 |
| | | | |

### <a name="streaming-ingest-metrics"></a>串流內嵌計量

串流內嵌計量會追蹤串流內嵌資料和要求速率、持續時間和結果。

**計量** | **單位** | **聚集** | **度量說明** | **維度** |
|---|---|---|---|---|
串流內嵌資料速率 |    Count   | RateRequestsPerSecond | 內嵌至叢集的總數據量。 | 無 |
串流內嵌持續時間   | 毫秒  | Avg、Max、Min | 所有串流內嵌要求的總持續時間。 | 無 |
串流內嵌要求率   | Count | Count、Avg、Max、Min、Sum | 串流內嵌要求的總數。 | 無 |
串流內嵌結果 | Count | Avg   | 依結果類型的串流內嵌要求總數。 | 結果 |
| | | | |

有關支援的 [Azure 資料總管叢集計量](/azure/azure-monitor/platform/metrics-supported#microsoftkustoclusters)的其他資訊。


## <a name="next-steps"></a>後續步驟

* [教學課程：在 Azure 資料總管中內嵌和查詢監視資料](ingest-data-no-code.md)
* [使用診斷記錄來監視 Azure 資料總管擷取作業](using-diagnostic-logs.md)
* [快速入門：在 Azure 資料總管中查詢資料](web-query-data.md)
