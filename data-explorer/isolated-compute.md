---
title: 在 Azure 資料總管叢集上啟用隔離計算
description: 在本文中，您將瞭解如何藉由選取正確的 SKU，在 Azure 資料總管叢集上啟用獨立計算。
author: orspod
ms.author: orspodek
ms.reviewer: dagrawal
ms.service: data-explorer
ms.topic: how-to
ms.date: 09/16/2020
ms.custom: references_regions
ms.openlocfilehash: 8139701037a8d4d25490a355cc822051851c85cd
ms.sourcegitcommit: 313a91d2a34383b5a6e39add6c8b7fabb4f8d39a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/16/2020
ms.locfileid: "90682305"
---
# <a name="enable-isolated-compute-on-your-azure-data-explorer-cluster"></a>在 Azure 資料總管叢集上啟用隔離計算

隔離計算虛擬機器 (Vm) 可讓客戶在單一客戶專用的硬體隔離環境中執行其工作負載。 使用隔離計算 Vm 部署的叢集最適合需要高度隔離以符合合規性和法規需求的工作負載。 計算 SKU () 提供隔離來保護資料安全，而不會犧牲設定的彈性。 如需詳細資訊，請參閱[獨立計算的](/azure/azure-government/azure-secure-isolation-guidance#compute-isolation)[計算隔離](/azure/security/fundamentals/isolation-choices#compute-isolation)和 Azure 指引。 

Azure 資料總管支援使用 SKU **Standard_E64i_v3**來提供隔離的計算。 此 SKU 可自動擴大和縮小，以符合您應用程式或企業的需求。 隔離計算支援可在以下區域取得：
* 美國西部 2
* 美國東部 
* 美國中南部

獨立計算 Vm （雖然高度定價）是執行需要伺服器實例層級隔離之工作負載的理想 SKU。 如需 Azure 資料總管支援的 Sku 的詳細資訊，請參閱 [為您的 azure 資料總管叢集選取正確的 VM sku](manage-cluster-choose-sku.md)。

> [!NOTE]
> Azure 資料總管目前不支援[azure 專用主機](https://azure.microsoft.com/services/virtual-machines/dedicated-host/)。 

## <a name="enable-isolated-compute-on-azure-data-explorer-cluster"></a>在 Azure 資料總管叢集上啟用隔離計算 

若要在 Azure 資料總管中啟用隔離計算，請遵循下列其中一個程式：
* [建立具有隔離計算 SKU 的叢集](#create-a-cluster-with-isolated-compute-sku)
* [選取現有叢集上的隔離計算 SKU](#select-the-isolated-compute-sku-on-an-existing-cluster)

## <a name="create-a-cluster-with-isolated-compute-sku"></a>建立具有隔離計算 SKU 的叢集

1. 遵循指示， [在 Azure 入口網站中建立 Azure 資料總管叢集和資料庫](create-cluster-database-portal.md)
1. 在 [**基本**] 索引標籤內的 [[建立](create-cluster-database-portal.md#create-a-cluster)叢集] 中，選取 [**計算規格**] 下拉式清單中**Standard_E64i_v3** 。

## <a name="select-the-isolated-compute-sku-on-an-existing-cluster"></a>選取現有叢集上的隔離計算 SKU

1. 在您的 Azure 資料總管叢集總覽畫面中，選取 **[** **擴大**]
1. 在搜尋方塊中，搜尋 *Standard_E64i_v3* 然後按一下 sku 名稱，或從 sku 清單中選取 **Standard_E64i_v3** sku。
1. 選取 [套用] 以更新 **您的 SKU** 。 

:::image type="content" source="media/isolated-compute/select-isolated-compute-sku.png" alt-text="選取隔離的計算 SKU":::

> [!TIP]
> 擴大進程可能需要幾分鐘的時間。

## <a name="next-steps"></a>後續步驟

* [管理叢集垂直調整 (在 Azure 資料總管中擴大) ，以滿足不斷變化的需求](manage-cluster-vertical-scaling.md)
* [為您的 Azure 資料總管叢集選取正確的 VM SKU](manage-cluster-choose-sku.md)
