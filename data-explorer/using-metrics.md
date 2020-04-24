---
title: 監視 Azure 資料資源管理員效能、執行狀況&使用指標
description: 瞭解如何使用 Azure 資料資源管理器指標來監視群集的性能、運行狀況和使用方式。
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/19/2020
ms.openlocfilehash: 1319b8cd6ac8a0eb83381c24bcde9996458e47a7
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81500914"
---
# <a name="monitor-azure-data-explorer-performance-health-and-usage-with-metrics"></a>使用指標監控 Azure 資料資源管理員的效能、執行狀況和使用

Azure 資料總管是快速、完全受控的資料分析服務，可即時分析來自應用程式、網站、IoT 裝置等的大量資料流。 若要使用 Azure 資料總管，請先建立叢集，然後在該叢集中建立一或多個資料庫。 然後將資料內嵌 (載入) 至資料庫，讓您可以對資料執行查詢。 Azure 資料資源管理員指標提供有關群集資源運行狀況和性能的關鍵指標。 使用本文中詳述的指標作為獨立指標監視特定方案中的 Azure 數據資源管理器群集運行狀況和性能。 您還可以使用指標作為操作[Azure 儀表板](/azure/azure-portal/azure-portal-dashboards)和[Azure 警報的基礎](/azure/azure-monitor/platform/alerts-metric-overview)。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果沒有,可以創建[免費的 Azure 帳戶](https://azure.microsoft.com/free/)。
* [叢集與資料庫](create-cluster-database-portal.md)。

## <a name="using-metrics"></a>使用指標

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
1. 在 Azure 資料資源管理器群集中,選擇**指標**以打開指標窗格並開始對群集進行分析。
    ![選擇指標](media/using-metrics/select-metrics.png)。
1. 在「指標」窗格中![:指標窗格](media/using-metrics/metrics-pane.png)
    1. 要建立指標圖表,請選擇**指標**名稱和每個指標的相關**聚合**。 為 Azure 資料資源管理器群集預先選擇**資源和****指標命名空間**選取器。 有關不同指標的詳細資訊,請參閱[受支援的 Azure 資料資源管理器指標](#supported-azure-data-explorer-metrics)。
    1. 選擇 **「新增指標**」可查看在同一圖表中繪製的多個指標。
    1. 選擇 **= 新圖表**可檢視一個檢視中的多個圖表。
    1. 使用時間選取器更改時間範圍(預設值:過去 24 小時)。
    1. 使用立體式指標使用[**「篩選器**與應用程式**分割 」**](/azure/azure-monitor/platform/metrics-getting-started#apply-dimension-filters-and-splitting)。
    1. 選擇 **「固定到儀錶板**」可將圖表配置添加到儀錶板,以便可以再次查看。
    1. 設置 **「新警報規則**」,以便使用設置條件可視化指標。 新的警示規則將包含來自您圖表的目標資源、計量、分割及篩選維度。 在[警報規則創建窗格](/azure/azure-monitor/platform/metrics-charts#create-alert-rules)中修改這些設置。

有關使用[指標資源管理員](/azure/azure-monitor/platform/metrics-getting-started)的其他資訊。

## <a name="supported-azure-data-explorer-metrics"></a>支援的 Azure 資料資源管理員指標

受支援的 Azure 資料資源管理器指標根據使用方式分為不同的類別。 

### <a name="cluster-health-metrics"></a>叢集執行狀況指標

群集運行狀況指標跟蹤群集的一般運行狀況。 這包括資源和攝入利用率和回應能力。

**計量** | **單位** | **彙總** | **度量說明** | **維度** |
|---|---|---|---|---|
| 快取利用率 | 百分比 | 平均,最大,最小 | 群集目前正在使用的已分配緩存資源的百分比。 快取是根據定義的緩存策略為使用者活動分配的 SSD 的大小。 群集的平均緩存利用率為 80% 或更少是一種可持續狀態。 如果平均緩存利用率超過 80%,則應[將群集擴展到](manage-cluster-vertical-scaling.md)存儲優化定價層或[擴展到](manage-cluster-horizontal-scaling.md)更多實例。 或者,調整緩存策略(緩存中的天數更少)。 如果快取利用率超過 100%,則根據快取策略,要緩存的資料大小大於群集上緩存的總大小。 | None |
| CPU | 百分比 | 平均,最大,最小 | 群集中計算機當前正在使用的已分配計算資源的百分比。 群集的平均 CPU 為 80% 或更少是可持續的。 CPU 的最大值為 100%,這意味著沒有額外的計算資源來處理數據。 當群集性能不佳時,請檢查 CPU 的最大值,以確定是否存在被阻止的特定 CPU。 | None |
| 攝入利用率 | 百分比 | 平均,最大,最小 | 用於從容量策略中分配用於執行引入的總資源中引入數據的實際資源的百分比。 默認容量策略不超過 512 個併發引入操作或投入引入的群集資源的 75%。 平均攝入利用率為 80% 或更少是群集的可持續狀態。 攝入利用率的最大值為 100%,這意味著使用所有群集引入能力,並可能導致引入佇列。 | None |
| 保持活力 | Count | Avg | 跟蹤群集的回應能力。 完全回應的群集返回值 1,被阻止或斷開連接的群集返回 0。 |
| 限制指令的總數 | Count | 平均,最大值,最小,總和 | 群集中受限制(拒絕)命令的數量,因為已達到允許的最大併發(並行)命令數。 | None |
| 擴充磁碟區總數 | Count | 平均,最大值,最小,總和 | 群集中的數據範圍總數。 此指標中的更改可能意味著群集上的數據結構發生大規模更改和高負載,因為合併數據範圍是 CPU 繁重的活動。 | None |
| | | | |

### <a name="export-health-and-performance-metrics"></a>匯出執行狀況和效能指標

匯出運行狀況和性能指標跟蹤匯出操作的一般運行狀況和性能,如晚點、結果、記錄數和利用率。

**計量** | **單位** | **彙總** | **度量說明** | **維度** |
|---|---|---|---|---|
連續匯出的紀錄數    | Count | Sum | 所有連續匯出作業中的匯出記錄數。 | None |
連續匯出最大延遲 |    Count   | 最大值   | 群集中連續匯出作業報告的延遲(以分鐘表示)。 | None |
連續匯出待處理計數 | Count | 最大值   | 掛起的連續匯出作業數。 這些作業已準備好運行,但在佇列中等待,這可能是由於容量不足)。 
連續匯出結果    | Count |   Count   | 每次連續匯出運行的失敗/成功結果。 | 連續匯出名稱 |
出口利用率 |    百分比 | 最大值   | 在群集的總匯出容量(0 到100之間)中所使用的出口容量。 | None |
| | | | |

### <a name="ingestion-health-and-performance-metrics"></a>攝入運行狀況和性能指標

攝入運行狀況和性能指標跟蹤攝入操作(如延遲、結果和卷)的一般運行狀況和性能。

**計量** | **單位** | **彙總** | **度量說明** | **維度** |
|---|---|---|---|---|
| 處理的事件(對於事件/IoT 中心) | Count | 最大值,最小,總和 | 從事件中心讀取並由群集處理的事件總數。 事件分為拒絕的事件和群集引擎接受的事件。 | EventStatus |
| 攝入延遲 | 秒 | 平均,最大,最小 | 從群集中接收數據到準備好查詢的數據,數據引入的延遲。 攝入延遲期取決於攝入方案。 | None |
| 攝取結果 | Count | Count | 失敗並成功的引入操作的總數。 使用**應用分割**創建成功桶和失敗結果並分析維度(**value** > **狀態**)。| IngestionResultDetails |
| 攝入體積(以 MB) | Count | 最大,總和 | 壓縮前引入到群集(以 MB 表示)的數據的總大小。 | 資料庫 |
| | | | |  

### <a name="query-performance"></a>查詢效能

查詢性能指標跟蹤查詢持續時間和併發或限制查詢的總數。

**計量** | **單位** | **彙總** | **度量說明** | **維度** |
|---|---|---|---|---|
| 查詢持續時間 | 毫秒 | 平均,最小,最大,總和 | 收到查詢結果之前的總時間(不包括網路延遲)。 | QueryStatus |
| 併發查詢總數 | Count | 平均,最大值,最小,總和 | 群集中並行運行的查詢數。 此指標是估計群集上負載的好方法。 | None |
| 限制查詢的總數 | Count | 平均,最大值,最小,總和 | 群集中已限制(拒絕)查詢的數量。 允許在併發查詢策略中定義允許的最大併發(並行)查詢數。 | None |
| | | | |

### <a name="streaming-ingest-metrics"></a>串流式傳輸引入指標

流式處理指標跟蹤流式引入數據和請求速率、持續時間和結果。

**計量** | **單位** | **彙總** | **度量說明** | **維度** |
|---|---|---|---|---|
串流式傳輸引入資料速率 |    Count   | 速率要求每秒 | 引入到群集的數據總量。 | None |
串流式傳輸引入持續時間   | 毫秒  | 平均,最大,最小 | 所有流式引入請求的總持續時間。 | None |
串流式傳輸引入要求速率   | Count | 計數, 平均, 最大, 最小, 總和 | 流式引入請求的總數。 | None |
串流式傳送結果 | Count | Avg   | 按結果類型表示的流式處理請求總數。 | 結果 |
| | | | |

有關[受支援的 Azure 資料資源管理器群集指標](/azure/azure-monitor/platform/metrics-supported#microsoftkustoclusters)的其他資訊。


## <a name="next-steps"></a>後續步驟

* [教學:在 Azure 資料資源管理員中引入與查詢監視資料](/azure/data-explorer/ingest-data-no-code)
* [使用診斷記錄來監視 Azure 資料總管擷取作業](/azure/data-explorer/using-diagnostic-logs)
* [快速入門：在 Azure 資料總管中查詢資料](web-query-data.md)
