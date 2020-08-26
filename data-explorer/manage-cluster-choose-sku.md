---
title: 選取正確的 Azure 資料總管叢集 VM SKU
description: 本文說明如何為 Azure 資料總管叢集選取最佳的 SKU 大小。
author: orspod
ms.author: orspodek
ms.reviewer: avnera
ms.service: data-explorer
ms.topic: how-to
ms.date: 07/14/2019
ms.openlocfilehash: 196bfdd69b5fc73676dc39c0d8fae92682cc2f93
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88875118"
---
# <a name="select-the-correct-vm-sku-for-your-azure-data-explorer-cluster"></a>為您的 Azure 資料總管叢集選取正確的 VM SKU 

當您建立新的叢集或針對變更的工作負載優化叢集時，Azure 資料總管可提供多個虛擬機器 (VM) Sku 來從中選擇。 您可以謹慎選擇 Vm，為您的任何工作負載提供最理想的成本。 

資料管理叢集的大小和 VM SKU 完全由 Azure 資料總管服務管理。 它們是由引擎的 VM 大小和內嵌工作負載等因素所決定。 

您可以藉由相應 [增加](manage-cluster-vertical-scaling.md)叢集來隨時變更引擎叢集的 VM SKU。 最好是從符合初始案例的最小 SKU 大小著手。 請記住，在使用新的 VM SKU 重新建立叢集時，擴大叢集會導致最多30分鐘的停機時間。

> [!TIP]
> [ (RI) 的計算保留實例](https://docs.microsoft.com/azure/virtual-machines/windows/prepay-reserved-vm-instances)適用于 Azure 資料總管叢集。  

本文說明各種 VM SKU 選項，並提供可協助您做出最佳選擇的技術詳細資料。

## <a name="select-a-cluster-type"></a>選取叢集類型

Azure 資料總管提供兩種類型的叢集：

* **生產**環境：生產叢集包含兩個適用于引擎和資料管理叢集的節點，並在 Azure 資料總管 [SLA](https://azure.microsoft.com/support/legal/sla/data-explorer/v1_0/)下運作。

* **開發/測試 (沒有 SLA) **：開發/測試叢集具有引擎叢集的單一 D11 v2 節點，以及資料管理叢集的單一 D1 節點。 此叢集類型是最低的成本設定，因為它的實例計數較低，而且沒有引擎標記費用。 此叢集設定沒有 SLA，因為缺少冗余。

## <a name="sku-types"></a>SKU 類型

當您建立 Azure 資料總管叢集時，請針對規劃的工作負載選取 *最佳* VM SKU。 您可以選擇下列兩個 Azure 資料總管 SKU 系列：

* **D v2**： d SKU 是計算優化的，有兩種類別：
    * VM 本身
    * 與 premium 儲存體磁片配套的 VM

* **LS**： L SKU 已儲存優化。 它的 SSD 大小遠高於類似定價的 D SKU。

下表說明可用的 SKU 類型之間的主要差異：
 
| 屬性 | D SKU | L SKU |
|---|---|---
|**小型 Sku**|大小下限是具有兩個核心的 D11|大小下限是具有四個核心的 L4 |
|**可用性**|適用于所有區域 (DS + PS 版本有更高的可用性) |適用于幾個區域 |
|**每個核心的每 GB 快取成本 &nbsp;**|使用 D SKU 高，DS + PS 版本低|使用隨用隨付選項的最低選項 |
|** (RI) 定價的保留實例**|三年承諾用量的高折扣 (超過 55 &nbsp; %) |&nbsp;三年承諾用量 (20% 的折扣)  |  

## <a name="select-your-cluster-vm"></a>選取您的叢集 VM 

若要選取您的叢集 VM，請 [設定垂直調整](manage-cluster-vertical-scaling.md#configure-vertical-scaling)。 

您可以使用各種可供選擇的 VM SKU 選項，將您案例的效能和快取需求的成本優化。 
* 如果您需要高查詢量的最佳效能，理想的 SKU 應為計算優化。 
* 如果您需要以相對較低的查詢負載來查詢大量資料，儲存體優化的 SKU 可協助降低成本，並仍提供絕佳的效能。

由於小型 Sku 的每個叢集實例數目有限，因此最好使用具有較大 RAM 的較大 Vm。 某些查詢類型需要更多 RAM，以對 RAM 資源提出更多需求，例如使用的查詢 `joins` 。 因此，當您考慮調整選項時，建議您擴大為較大的 SKU，而不是藉由新增更多實例來相應放大。

## <a name="vm-options"></a>VM 選項

下表說明 Azure 資料總管叢集 Vm 的技術規格：

|**名稱**| **類別** | **SSD 大小** | **核心** | **RAM** | **Premium storage 磁片 (1 &nbsp; TB) **| **每個群集的最小實例計數** | **每個叢集的最大實例計數**
|---|---|---|---|---|---|---|---
|Dev (沒有 SLA) _Standard_D11_v2| 計算優化 | 75 &nbsp; GB    | 1 | 14 &nbsp; GB | 0 | 1 | 1
|Standard_D11_v2| 計算優化 | 75 &nbsp; GB    | 2 | 14 &nbsp; GB | 0 | 2 | 8 
|Standard_D12_v2| 計算優化 | 150 &nbsp; GB   | 4 | 28 &nbsp; GB | 0 | 2 | 16
|Standard_D13_v2| 計算優化 | 307 &nbsp; GB   | 8 | 56 &nbsp; GB | 0 | 2 | 1,000
|Standard_D14_v2| 計算優化 | 614 &nbsp; GB   | 16| 112 &nbsp; GB | 0 | 2 | 1,000
|Standard_DS13_v2 + 1 &nbsp; TB &nbsp; PS| 儲存體-優化 | 1 &nbsp; TB | 8 | 56 &nbsp; GB | 1 | 2 | 1,000
|Standard_DS13_v2 + 2 &nbsp; TB &nbsp; PS| 儲存體-優化 | 2 &nbsp; TB | 8 | 56 &nbsp; GB | 2 | 2 | 1,000
|Standard_DS14_v2 + 3 &nbsp; TB &nbsp; PS| 儲存體-優化 | 3 &nbsp; TB | 16 | 112 &nbsp; GB | 2 | 2 | 1,000
|Standard_DS14_v2 + 4 &nbsp; TB &nbsp; PS| 儲存體-優化 | 4 &nbsp; TB | 16 | 112 &nbsp; GB | 4 | 2 | 1,000
|Standard_L4s| 儲存體-優化 | 650 &nbsp; GB | 4 | 32 &nbsp; GB | 0 | 2 | 16
|Standard_L8s| 儲存體-優化 | 1.3 &nbsp; TB | 8 | 64 &nbsp; GB | 0 | 2 | 1,000
|Standard_L16s| 儲存體-優化 | 2.6 &nbsp; TB | 16| 128 &nbsp; GB | 0 | 2 | 1,000

* 您可以使用 Azure 資料總管 [LISTSKUS API](/dotnet/api/microsoft.azure.management.kusto.clustersoperationsextensions.listskus?view=azure-dotnet)來查看每個區域的已更新 VM SKU 清單。 
* 深入瞭解 [各種 sku](/azure/virtual-machines/windows/sizes)。 

## <a name="next-steps"></a>後續步驟

* 您可以視不同需求變更 VM SKU，隨時 [擴大或](manage-cluster-vertical-scaling.md) 縮小引擎叢集。 

* 您可以根據不斷變化的需求，相應 [縮小或](manage-cluster-horizontal-scaling.md) 相應放大引擎叢集的大小，以改變容量。

