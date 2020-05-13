---
title: 使用計量監視 Azure 資料總管效能、健康情況 & 使用量
description: 瞭解如何使用 Azure 資料總管計量來監視叢集的效能、健康情況和使用量。
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/19/2020
ms.openlocfilehash: ffe641c6e96db1378be7a8ce845685fb2b66715f
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83374301"
---
# <a name="monitor-azure-data-explorer-performance-health-and-usage-with-metrics"></a>使用計量監視 Azure 資料總管效能、健康情況和使用量

Azure 資料總管是快速、完全受控的資料分析服務，可即時分析來自應用程式、網站、IoT 裝置等的大量資料流。 若要使用 Azure 資料總管，請先建立叢集，然後在該叢集中建立一或多個資料庫。 然後將資料內嵌 (載入) 至資料庫，讓您可以對資料執行查詢。 Azure 資料總管計量提供叢集資源健全狀況和效能的關鍵指標。 使用本文中詳述的計量，以獨立計量的形式監視 Azure 資料總管叢集健康情況和效能。 您也可以使用計量作為營運[Azure 儀表板](/azure/azure-portal/azure-portal-dashboards)和[Azure 警示](/azure/azure-monitor/platform/alerts-metric-overview)的基礎。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有帳戶，您可以建立一個[免費的 Azure 帳戶](https://azure.microsoft.com/free/)。
* 叢集[和資料庫](create-cluster-database-portal.md)。

## <a name="using-metrics"></a>使用計量

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. 在您的 Azure 資料總管叢集中，選取 [**計量**] 以開啟 [計量] 窗格，並在您的叢集上開始分析。
    ![選取 [計量] ](media/using-metrics/select-metrics.png) 。
1. 在 [計量] 窗格中： [ ![ 計量] 窗格](media/using-metrics/metrics-pane.png)
    1. 若要**建立度量圖表，請選取 [** 計量名稱] 和 [每個度量的相關**匯總**]。 系統會為您的 Azure 資料總管叢集預先選取**資源**和計量**命名空間**選取器。 如需不同計量的詳細資訊，請參閱[支援的 Azure 資料總管計量](#supported-azure-data-explorer-metrics)。
    1. 選取 [**新增度量**] 以查看在相同圖表中繪製的多個計量。
    1. 選取 [ **+ 新增圖表**] 以在單一視圖中查看多個圖表。
    1. 使用時間選擇器來變更時間範圍（預設：過去24小時）。
    1. 針對具有維度的計量，使用 [ [**加入篩選**] 和 [套用**分割**](/azure/azure-monitor/platform/metrics-getting-started#apply-dimension-filters-and-splitting) ]。
    1. 選取 [**釘選到儀表板**]，將您的圖表設定新增至儀表板，讓您可以再次進行查看。
    1. 設定**新的警示規則**，使用設定的準則將您的計量視覺化。 新的警示規則將包含來自您圖表的目標資源、計量、分割及篩選維度。 在 [[警示規則建立] 窗格](/azure/azure-monitor/platform/metrics-charts#create-alert-rules)中修改這些設定。

有關使用[計量瀏覽器](/azure/azure-monitor/platform/metrics-getting-started)的其他資訊。

## <a name="supported-azure-data-explorer-metrics"></a>支援的 Azure 資料總管計量

根據使用方式，支援的 Azure 資料總管計量會分成各種類別。 

### <a name="cluster-health-metrics"></a>叢集健康情況計量

叢集健康情況計量會追蹤叢集的一般健全狀況。 這包括資源和內嵌使用率和回應性。

**計量** | **單位** | **累積** | **度量說明** | **維度** |
|---|---|---|---|---|
| 快取使用率 | 百分比 | Avg、Max、Min | 叢集目前正在使用的已配置快取資源百分比。 Cache 是根據定義的快取原則，配置給使用者活動的 SSD 大小。 平均快取使用率80% （或更少）是叢集的持續性狀態。 如果平均快取使用率高於80%，則叢集應相應[增加](manage-cluster-vertical-scaling.md)為儲存體優化的定價層，或向[外擴充](manage-cluster-horizontal-scaling.md)至更多實例。 或者，調整快取原則（快取中的天數較少）。 如果快取使用率超過100%，則根據快取原則快取的資料大小會大於叢集上快取的總大小。 | 無 |
| CPU | 百分比 | Avg、Max、Min | 叢集中電腦目前正在使用的已配置計算資源百分比。 叢集的平均 CPU 80% （或更少）是可持續的。 CPU 的最大值是100%，這表示沒有任何額外的計算資源可以處理資料。 當叢集無法正常執行時，請檢查 CPU 的最大值，以判斷是否有特定的 Cpu 遭到封鎖。 | 無 |
| 內嵌使用率 | 百分比 | Avg、Max、Min | 實際資源的百分比，用來從配置的總資源（在容量原則中）內嵌資料，以執行內嵌。 預設容量原則不超過512的並行內嵌作業或75% 的叢集資源投資。 80% 或更少的平均內嵌使用率是叢集的持續性狀態。 內嵌使用率的最大值是100%，這表示會使用所有的叢集內嵌功能，而且可能會產生內嵌佇列。 | 無 |
| 保持運作 | 計數 | Avg | 追蹤叢集的回應性。 完全回應的叢集會傳回值1，而已封鎖或已中斷連線的叢集會傳回0。 |
| 已節流的命令總數 | 計數 | Avg、Max、Min、Sum | 因為已達到允許的並行（平行）命令數目上限，所以叢集中的節流（已拒絕）命令數目。 | 無 |
| 範圍總數 | 計數 | Avg、Max、Min、Sum | 叢集中的資料範圍總數。 此計量中的變更可表示大規模的資料結構變更和叢集上的高負載，因為合併資料範圍是一項 CPU 繁重的活動。 | 無 |
| | | | |

### <a name="export-health-and-performance-metrics"></a>匯出健全狀況和效能計量

匯出健全狀況和效能計量會追蹤匯出作業的一般健全狀況和效能，例如延遲、結果、記錄數目和使用率。

**計量** | **單位** | **累積** | **度量說明** | **維度** |
|---|---|---|---|---|
匯出記錄的連續匯出數目    | 計數 | Sum | 所有連續匯出作業中匯出的記錄數目。 | 無 |
連續匯出最大延遲 |    計數   | 最大值   | 叢集中的連續匯出作業所報告的延遲（以分鐘為單位）。 | 無 |
連續匯出暫止計數 | 計數 | 最大值   | 暫止連續匯出作業的數目。 這些作業已準備好執行，但在佇列中等候，可能是因為容量不足）。 
連續匯出結果    | 計數 |   計數   | 每個連續匯出執行的失敗/成功結果。 | ContinuousExportName |
匯出使用率 |    百分比 | 最大值   | 已使用的匯出容量，超出叢集中的總匯出容量（介於0到100之間）。 | 無 |
| | | | |

### <a name="ingestion-health-and-performance-metrics"></a>內嵌健全狀況和效能計量

內嵌健全狀況和效能計量會追蹤像是延遲、結果和磁片區等內嵌作業的一般健全狀況和效能。

**計量** | **單位** | **累積** | **度量說明** | **維度** |
|---|---|---|---|---|
| 已處理的事件（適用于事件/IoT 中樞） | 計數 | Max、Min、Sum | 讀取自事件中樞並由叢集處理的事件總數。 事件會分割成已拒絕的事件和叢集引擎所接受的事件。 | EventStatus |
| 內嵌延遲 | 秒 | Avg、Max、Min | 資料內嵌的延遲，從叢集中收到資料直到準備好進行查詢為止。 內嵌延遲期間取決於內嵌案例。 | 無 |
| 內嵌結果 | 計數 | 計數 | 失敗和成功的內嵌作業總數。 使用 [套用**分割**] 來建立成功和失敗結果的 bucket，並分析維度（**值**  >  **狀態**）。| IngestionResultDetails |
| 內嵌磁片區（以 MB 為單位） | 計數 | Max、Sum | 壓縮之前內嵌至叢集的資料大小總計（以 MB 為單位）。 | 資料庫 |
| | | | |  

### <a name="query-performance"></a>查詢效能

查詢效能計量會追蹤查詢持續時間，以及並行或節流查詢的總次數。

**計量** | **單位** | **累積** | **度量說明** | **維度** |
|---|---|---|---|---|
| 查詢持續時間 | 毫秒 | Avg、Min、Max、Sum | 在收到查詢結果之前的總時間（不包括網路延遲）。 | QueryStatus |
| 並行查詢的總數 | 計數 | Avg、Max、Min、Sum | 在叢集中平行執行的查詢數目。 此計量是預估叢集負載的好方法。 | 無 |
| 已節流的查詢總數 | 計數 | Avg、Max、Min、Sum | 叢集中已節流（已拒絕）的查詢數目。 並行查詢原則中會定義允許的並行（平行）查詢數目上限。 | 無 |
| | | | |

### <a name="streaming-ingest-metrics"></a>串流內嵌計量

串流內嵌計量會追蹤串流內嵌資料和要求率、持續時間和結果。

**計量** | **單位** | **累積** | **度量說明** | **維度** |
|---|---|---|---|---|
串流內嵌資料速率 |    計數   | RateRequestsPerSecond | 內嵌至叢集的總數據量。 | 無 |
串流內嵌持續時間   | 毫秒  | Avg、Max、Min | 所有串流內嵌要求的總持續時間。 | 無 |
串流內嵌要求速率   | 計數 | Count、Avg、Max、Min、Sum | 串流內嵌要求總數。 | 無 |
串流內嵌結果 | 計數 | Avg   | 依結果類型的串流內嵌要求總數。 | 結果 |
| | | | |

[支援的 Azure 資料總管叢集計量](/azure/azure-monitor/platform/metrics-supported#microsoftkustoclusters)的其他相關資訊。


## <a name="next-steps"></a>後續步驟

* [教學課程：在 Azure 中內嵌和查詢監視資料資料總管](ingest-data-no-code.md)
* [使用診斷記錄來監視 Azure 資料總管擷取作業](using-diagnostic-logs.md)
* [快速入門：在 Azure 資料總管中查詢資料](web-query-data.md)
