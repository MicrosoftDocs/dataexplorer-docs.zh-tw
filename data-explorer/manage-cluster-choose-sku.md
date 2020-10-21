---
title: 為您的 Azure 資料總管叢集選取正確的計算 SKU
description: 本文說明如何為 Azure 資料總管叢集選取最佳的計算 SKU 大小。
author: orspod
ms.author: orspodek
ms.reviewer: avnera
ms.service: data-explorer
ms.topic: how-to
ms.date: 10/13/2020
ms.openlocfilehash: 44c115cd509b72d5f83b1c1109ae09dc050d1a74
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92337443"
---
# <a name="select-the-correct-compute-sku-for-your-azure-data-explorer-cluster"></a>為您的 Azure 資料總管叢集選取正確的計算 SKU 

當您建立新的叢集或針對變更的工作負載優化叢集時，Azure 資料總管可提供多個虛擬機器 (VM) Sku 來從中選擇。 您可以謹慎選擇這些計算 Sku，為您的任何工作負載提供最理想的成本。 

資料管理叢集的大小和 VM SKU 完全由 Azure 資料總管服務管理。 它們是由引擎的 VM 大小和內嵌工作負載等因素所決定。 

您可以藉由相應 [增加](manage-cluster-vertical-scaling.md)叢集來隨時變更引擎叢集的計算 SKU。 最好是從符合初始案例的最小 SKU 大小著手。 請記住，在使用新的 SKU 重新建立叢集時，擴大叢集會導致最多30分鐘的停機時間。 您也可以使用 [Azure Advisor 建議](azure-advisor.md) 將您的計算 SKU 優化。

> [!TIP]
> [ (RI) 的計算保留實例 ](/azure/virtual-machines/windows/prepay-reserved-vm-instances) 適用于 Azure 資料總管叢集。  

本文說明各種計算 SKU 選項，並提供可協助您做出最佳選擇的技術詳細資料。

## <a name="select-a-cluster-type"></a>選取叢集類型

Azure 資料總管提供兩種類型的叢集：

* **生產**環境：生產叢集包含兩個適用于引擎和資料管理叢集的節點，並在 Azure 資料總管 [SLA](https://azure.microsoft.com/support/legal/sla/data-explorer/v1_0/)下運作。

* **開發/測試 (沒有 SLA) **：開發/測試叢集有一個適用于引擎和資料管理叢集的節點。 此叢集類型是最低的成本設定，因為它的實例計數較低，而且沒有引擎標記費用。 此叢集設定沒有 SLA，因為缺少冗余。

## <a name="compute-sku-types"></a>計算 SKU 類型

Azure 資料總管叢集針對不同類型的工作負載，支援各種不同的 Sku。 每個 SKU 都提供不同的 SSD 和 CPU 比例，以協助客戶正確地調整其部署的大小，並為其企業分析工作負載建立符合成本的最佳解決方案。

### <a name="compute-optimized"></a>計算最佳化

* 提供高核心到快取的比率。
* 適用于對小型到中型資料大小的高比率查詢。
* 低延遲 i/o 的本機 SSD。

### <a name="heavy-compute"></a>大量計算

* AMD Sku，提供比核心更高的速率。
* 低延遲 i/o 的本機 SSD。

### <a name="storage-optimized"></a>儲存體最佳化

* 較大的儲存體選項，範圍從 1 TB 到 4 TB （每個引擎節點）。
* 適用于需要以較不密集的計算查詢需求來儲存大量資料的工作負載。
* 某些 Sku 會使用高階儲存體 (受控磁片) 連接到引擎節點，而不是本機 SSD 來儲存熱資料。

### <a name="isolated-compute"></a>隔離計算

適用于執行需要伺服器實例層級隔離之工作負載的理想 SKU。

## <a name="select-and-optimize-your-compute-sku"></a>選取並優化您的計算 SKU 

### <a name="select-your-compute-sku-during-cluster-creation"></a>在叢集建立期間選取您的計算 SKU

當您建立 Azure 資料總管叢集時，請針對規劃的工作負載選取 *最佳* VM SKU。

下列屬性也可協助您進行 SKU 選取：
 
| 屬性 | 詳細資料 |
|---|---
|**可用性**| 並非所有的 Sku 都可在所有區域中使用 |
|**每個核心的每 GB 快取成本**| 高成本的計算和大量計算優化。 儲存體優化 Sku 的低成本 |
|** (RI) 定價的保留實例**| RI 折扣依區域和 SKU 而異 |  

> [!NOTE]
> 針對 Azure 資料總管叢集，相較于儲存體和網路，計算成本是叢集成本最重要的部分。

### <a name="optimize-your-cluster-compute-sku"></a>優化您的叢集計算 SKU

若要將您的叢集計算 SKU 優化，請 [設定垂直調整](manage-cluster-vertical-scaling.md#configure-vertical-scaling) ，並檢查 [Azure Advisor 建議](azure-advisor.md)。 

您可以使用各種計算 SKU 選項來進行選擇，以將您案例的效能和快取需求的成本優化。 
* 如果您需要高查詢量的最佳效能，理想的 SKU 應為計算優化。 
* 如果您需要以相對較低的查詢負載來查詢大量資料，儲存體優化的 SKU 可協助降低成本，並仍提供絕佳的效能。

由於小型 Sku 的每個叢集實例數目有限，因此最好使用具有較大 RAM 的較大 Vm。 某些查詢類型需要更多 RAM，以對 RAM 資源提出更多需求，例如使用的查詢 `joins` 。 這就是為什麼當您考慮調整選項時，我們建議您相應增加為較大的 SKU，而不是藉由新增更多實例來向外擴充。

## <a name="compute-sku-options"></a>計算 SKU 選項

下表說明 Azure 資料總管叢集 Vm 的技術規格：

|**名稱**| **類別** | **SSD 大小** | **核心** | **RAM** | **Premium storage 磁片 (1 &nbsp; TB) **| **每個群集的最小實例計數** | **每個叢集的最大實例計數**
|---|---|---|---|---|---|---|---
|開發 () Standard_D11_v2 的 SLA| 計算優化 | 75 &nbsp; GB    | 1 | 14 &nbsp; GB | 0 | 1 | 1
|開發 () Standard_E2a_v4 的 SLA| 計算優化 | 18 &nbsp; GB    | 1 | 14 &nbsp; GB | 0 | 1 | 1
|Standard_D11_v2| 計算優化 | 75 &nbsp; GB    | 2 | 14 &nbsp; GB | 0 | 2 | 8 
|Standard_D12_v2| 計算優化 | 150 &nbsp; GB   | 4 | 28 &nbsp; GB | 0 | 2 | 16
|Standard_D13_v2| 計算優化 | 307 &nbsp; GB   | 8 | 56 &nbsp; GB | 0 | 2 | 1,000
|Standard_D14_v2| 計算優化 | 614 &nbsp; GB   | 16| 112 &nbsp; GB | 0 | 2 | 1,000
|Standard_E2a_v4| 大量計算 | 18 &nbsp; GB    | 2 | 14 &nbsp; GB | 0 | 2 | 8 
|Standard_E4a_v4| 大量計算 | 54 &nbsp; GB   | 4 | 28 &nbsp; GB | 0 | 2 | 16
|Standard_E8a_v4| 大量計算 | 127 &nbsp; GB   | 8 | 56 &nbsp; GB | 0 | 2 | 1,000
|Standard_E16a_v4| 大量計算 | 273 &nbsp; GB   | 16| 112 &nbsp; GB | 0 | 2 | 1,000
|Standard_DS13_v2 + 1 &nbsp; TB &nbsp; PS| 儲存體-優化 | 1 &nbsp; TB | 8 | 56 &nbsp; GB | 1 | 2 | 1,000
|Standard_DS13_v2 + 2 &nbsp; TB &nbsp; PS| 儲存體-優化 | 2 &nbsp; TB | 8 | 56 &nbsp; GB | 2 | 2 | 1,000
|Standard_DS14_v2 + 3 &nbsp; TB &nbsp; PS| 儲存體-優化 | 3 &nbsp; TB | 16 | 112 &nbsp; GB | 2 | 2 | 1,000
|Standard_DS14_v2 + 4 &nbsp; TB &nbsp; PS| 儲存體-優化 | 4 &nbsp; TB | 16 | 112 &nbsp; GB | 4 | 2 | 1,000
|Standard_E8as_v4 + 1 &nbsp; TB &nbsp; PS| 儲存體-優化 | 1 &nbsp; TB | 8 | 56 &nbsp; GB | 1 | 2 | 1,000
|Standard_E8as_v4 + 2 &nbsp; TB &nbsp; PS| 儲存體-優化 | 2 &nbsp; TB | 8 | 56 &nbsp; GB | 2 | 2 | 1,000
|Standard_E16as_v4 + 3 &nbsp; TB &nbsp; PS| 儲存體-優化 | 3 &nbsp; TB | 16 | 112 &nbsp; GB | 3 | 2 | 1,000
|Standard_E16as_v4 + 4 &nbsp; TB &nbsp; PS| 儲存體-優化 | 4 &nbsp; TB | 16 | 112 &nbsp; GB | 4 | 2 | 1,000
|Standard_L4s| 儲存體-優化 | 650 &nbsp; GB | 4 | 32 &nbsp; GB | 0 | 2 | 16
|Standard_L8s| 儲存體-優化 | 1.3 &nbsp; TB | 8 | 64 &nbsp; GB | 0 | 2 | 1,000
|Standard_L16s| 儲存體-優化 | 2.6 &nbsp; TB | 16| 128 &nbsp; GB | 0 | 2 | 1,000
|Standard_L8s_v2| 儲存體-優化 | 1.7 &nbsp; TB | 8 | 64 &nbsp; GB | 0 | 2 | 1,000
|Standard_L16s_v2| 儲存體-優化 | 3.5 &nbsp; TB | 16| 128 &nbsp; GB | 0 | 2 | 1,000
|Standard_E64i1_v3| 隔離計算 | 1.1 &nbsp; TB | 16| 128 &nbsp; GB | 0 | 2 | 1,000

* 您可以使用 Azure 資料總管 [LISTSKUS API](/dotnet/api/microsoft.azure.management.kusto.clustersoperationsextensions.listskus?view=azure-dotnet)來查看每個區域的更新計算 SKU 清單。 
* 深入瞭解 [各種 sku](/azure/virtual-machines/windows/sizes)。 

## <a name="next-steps"></a>後續步驟

* 您可以視不同需求變更 VM SKU，隨時 [擴大或](manage-cluster-vertical-scaling.md) 縮小引擎叢集。 

* 您可以根據不斷變化的需求，相應 [縮小或](manage-cluster-horizontal-scaling.md) 相應放大引擎叢集的大小，以改變容量。

* 使用 [Azure Advisor 建議](azure-advisor.md) 將您的計算 SKU 優化。