---
title: 管理叢集垂直調整 (擴大) 以符合 Azure 資料總管中的需求
description: 本文描述根據變更需求擴大和縮小 Azure 資料總管叢集的步驟。
author: orspod
ms.author: orspodek
ms.reviewer: radennis
ms.service: data-explorer
ms.topic: how-to
ms.date: 07/14/2019
ms.openlocfilehash: c3177cb513e11a5fed976dafd113636addb5da37
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88875101"
---
# <a name="manage-cluster-vertical-scaling-scale-up-in-azure-data-explorer-to-accommodate-changing-demand"></a>管理叢集垂直調整 (在 Azure 資料總管中擴大) ，以滿足不斷變化的需求

適當調整叢集的大小對 Azure 資料總管的效能來說非常重要。 靜態叢集大小可能導致使用量過低或使用量過高，兩者皆不理想。

因為無法以絕對精確度預測叢集的需求，所以更好的方法是 *調整叢集規模* 、新增和移除具有變更需求的容量和 CPU 資源。 

有兩個用於調整 Azure 資料總管叢集的工作流程：

* [水準調整](manage-cluster-horizontal-scaling.md)，也稱為相應縮小和放大。
* 垂直調整，也稱為向上和向下調整。

本文說明垂直調整工作流程：

## <a name="configure-vertical-scaling"></a>設定垂直調整

1. 在 Azure 入口網站中，移至您的 Azure 資料總管叢集資源。 在 [ **設定**] 底下，選取 [ **擴大**]。

1. 在 [ **擴大** ] 視窗中，您會看到叢集可用的 sku 清單。 例如，在下圖中，只有四個 Sku 可用。

    ![相應增加](media/manage-cluster-vertical-scaling/scale-up.png)

    Sku 已停用，因為它們是目前的 SKU，或在叢集所在的區域中無法使用。

1. 若要變更您的 SKU，請選取新的 SKU，然後按一下 [ **選取**]。

> [!NOTE]
> * 垂直調整程式可能需要幾分鐘的時間，在這段期間，您的叢集將會暫止。 
> * 相應減少可能會危害您的叢集效能。
> * 價格是叢集虛擬機器和 Azure 資料總管服務成本的預估。 不包含其他成本。 如需完整的定價資訊，請參閱 Azure 資料總管 [成本估算器](https://dataexplorer.azure.com/AzureDataExplorerCostEstimator.html) 頁面中的估價和 azure 資料總管 [定價頁面](https://azure.microsoft.com/pricing/details/data-explorer/) 。

您現在已設定 Azure 資料總管叢集的垂直調整。 為水準縮放新增另一個規則。 如果您需要叢集調整問題的協助，請在 Azure 入口網站中 [開啟支援要求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) 。

## <a name="next-steps"></a>後續步驟

* [管理叢集水準調整](manage-cluster-horizontal-scaling.md) ，以根據您指定的計量動態地擴充實例計數。

* 遵循這篇文章來監視您的資源使用量： [使用計量監視 Azure 資料總管效能、健康情況和使用量](using-metrics.md)。

