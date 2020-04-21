---
title: 為 Azure 資料資源管理器叢集選擇正確的 VM SKU
description: 本文介紹如何為 Azure 資料資源管理器群集選擇最佳 SKU 大小。
author: avneraa
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/14/2019
ms.openlocfilehash: 2d078f9715a0cfa171f0c88776a4ab78c15215a8
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81502331"
---
# <a name="select-the-correct-vm-sku-for-your-azure-data-explorer-cluster"></a>為 Azure 資料資源管理器叢集選擇正確的 VM SKU 

當您為不斷變化的工作負荷創建新群集或優化群集時,Azure 資料資源管理器提供了多個虛擬機 (VM) SKU 供您選擇。 VM 經過精心挑選,可為您提供任何工作負載的最佳成本。 

數據管理群集的大小和 VM SKU 完全由 Azure 資料資源管理器服務管理。 它們由引擎的 VM 大小和引入工作負載等因素決定。 

您可以隨時透過[延伸叢集](manage-cluster-vertical-scaling.md)來更改引擎群集的 VM SKU。 最好從適合初始方案的最小 SKU 大小開始。 請記住,在使用新的 VM SKU 重新創建群集時,擴展群集會導致長達 30 分鐘的停機時間。

> [!TIP]
> 計算[保留實體 (RI)](https://docs.microsoft.com/azure/virtual-machines/windows/prepay-reserved-vm-instances)適用於 Azure 資料資源管理器群集。  

本文介紹了各種 VM SKU 選項,並提供了可説明您做出最佳選擇的技術詳細資訊。

## <a name="select-a-cluster-type"></a>選擇叢集型態

Azure 資料資源管理員提供兩種類型的群集:

* **生產**: 生產群集包含兩個節點的引擎和數據管理群集,並在 Azure 數據資源管理器[SLA](https://azure.microsoft.com/support/legal/sla/data-explorer/v1_0/)下運行。

* **開發/測試(無 SLA):** 開發/測試群集具有引擎群集的單個 D11 v2 節點和數據管理群集的單個 D1 節點。 此群集類型是成本最低的配置,因為它的實例計數較低,並且沒有發動機標記費用。 此群集配置沒有 SLA,因為它缺乏冗餘。

## <a name="sku-types"></a>SKU 類型

創建 Azure 資料資源管理器群集時,為計畫的工作負荷選擇*最佳*VM SKU。 您可以從以下兩個 Azure 資料資源管理員 SKU 系列中進行選擇:

* **D v2**: D SKU 經過計算優化,有兩種類型:
    * VM 本身
    * 與進階儲存磁碟捆綁的 VM

* **LS**: L SKU 是存儲優化的。 它的 SSD 大小比價格相似的 D SKU 大得多。

下表描述了可用 SKU 類型之間的主要區別:
 
| 屬性 | D SKU | L SKU |
|---|---|---
|**小型 SKU**|最小尺寸為 D11,帶有兩個內核|最小尺寸為 L4,帶四個核心 |
|**可用性**|在所有區域都提供(DS+PS 版本的可用性較為有限)|可在少數區域提供 |
|**每個核心&nbsp;每個 GB 快取的成本**|D SKU 高,DS_PS 版本低|使用即用即付選項的最低值 |
|**預留實體 (RI) 定價**|高折扣(三年承諾&nbsp;超過 55%)|折扣更低(三年&nbsp;承諾為 20%) |  

## <a name="select-your-cluster-vm"></a>選擇叢集 VM 

選擇群組的 VM,[請設定垂直縮放](manage-cluster-vertical-scaling.md#configure-vertical-scaling)。 

使用多種 VM SKU 選項可供選擇,您可以針對方案的性能和熱緩存要求優化成本。 
* 如果需要高查詢卷的最佳性能,則應對理想的 SKU 進行計算優化。 
* 如果需要以相對較低的查詢負載查詢大量數據,存儲優化的 SKU 可説明降低成本,但仍可提供出色的性能。

由於小 SKU 每個群集的實例數有限,因此最好使用 RAM 更大的 VM。 對於對 RAM 資源施加更多需求的某些查詢類型(`joins`如使用的查詢)需要更多的 RAM。 因此,在考慮縮放選項時,我們建議您向上擴展到更大的 SKU,而不是通過添加更多實例進行橫向擴展。

## <a name="vm-options"></a>VM 選項

下表描述了 Azure 資料資源管理器群集 VM 的技術規範:

|**名稱**| **類別目錄** | **SSD 大小** | **核心** | **Ram** | **進階儲存磁碟(1&nbsp;TB)**| **每個叢集的最小實體計數** | **每個叢集的最大實體計數**
|---|---|---|---|---|---|---|---
|D11 v2| 計算最佳化 | 75&nbsp;GB    | 2 | 14&nbsp;GB | 0 | 1 | 8 (開發/測試 SKU 除外,這是 1)
|D12 v2| 計算最佳化 | 150&nbsp;GB   | 4 | 28&nbsp;GB | 0 | 2 | 16
|D13 v2| 計算最佳化 | 307&nbsp;GB   | 8 | 56&nbsp;GB | 0 | 2 | 1,000
|D14 v2| 計算最佳化 | 614&nbsp;GB   | 16| 112&nbsp;GB | 0 | 2 | 1,000
|DS13 v2&nbsp;=&nbsp;1 TB PS| 儲存優化 | 1&nbsp;TB | 8 | 56&nbsp;GB | 1 | 2 | 1,000
|DS13 v2&nbsp;=&nbsp;2 TB PS| 儲存優化 | 2&nbsp;TB | 8 | 56&nbsp;GB | 2 | 2 | 1,000
|DS14 v2&nbsp;=&nbsp;3 TB PS| 儲存優化 | 3&nbsp;TB | 16 | 112&nbsp;GB | 2 | 2 | 1,000
|DS14 v2&nbsp;=&nbsp;4 TB PS| 儲存優化 | 4&nbsp;TB | 16 | 112&nbsp;GB | 4 | 2 | 1,000
|L4s v1| 儲存優化 | 650&nbsp;GB | 4 | 32&nbsp;GB | 0 | 2 | 16
|L8s v1| 儲存優化 | 1.3&nbsp;TB | 8 | 64&nbsp;GB | 0 | 2 | 1,000
|L16s_1| 儲存優化 | 2.6&nbsp;TB | 16| 128&nbsp;GB | 0 | 2 | 1,000

* 您可以使用 Azure 資料資源管理員[ListSkus API](/dotnet/api/microsoft.azure.management.kusto.clustersoperationsextensions.listskus?view=azure-dotnet)查看每個區域的更新 VM SKU 清單。 
* 瞭解有關[各種 SKU](/azure/virtual-machines/windows/sizes)的更多。 

## <a name="next-steps"></a>後續步驟

* 您可以隨時透過更改 VM SKU[來擴展或縮減](manage-cluster-vertical-scaling.md)引擎群集,具體取決於不同的需求。 

* 您可以[放大或縮小](manage-cluster-horizontal-scaling.md)發動機群集的大小,以根據不斷變化的需求來更改容量。

