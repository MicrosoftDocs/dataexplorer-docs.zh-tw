---
title: 管理叢集垂直調整（相應增加）以符合 Azure 資料總管中的需求
description: 本文說明根據變更需求相應增加和縮小 Azure 資料總管叢集的步驟。
author: orspod
ms.author: orspodek
ms.reviewer: radennis
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/14/2019
ms.openlocfilehash: fc3b4af91cd24b60f8ace110551daa47ff8cf934
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351602"
---
# <a name="manage-cluster-vertical-scaling-scale-up-in-azure-data-explorer-to-accommodate-changing-demand"></a>在 Azure 資料總管中管理叢集垂直調整（相應增加）以配合變更需求

適當調整叢集的大小對 Azure 資料總管的效能來說非常重要。 靜態叢集大小可能導致使用量過低或使用量過高，兩者皆不理想。

由於叢集上的需求無法以絕對精確度來預測，因此更好的方法是*調整*叢集、新增和移除具有變更需求的容量和 CPU 資源。 

有兩個工作流程可調整 Azure 資料總管叢集：

* [水準調整](manage-cluster-horizontal-scaling.md)，也稱為相應縮小和放大。
* 垂直調整，也稱為相應增加和減少。

本文說明垂直調整工作流程：

## <a name="configure-vertical-scaling"></a>設定垂直調整

1. 在 Azure 入口網站中，移至您的 Azure 資料總管叢集資源。 在 [**設定**] 下，選取 [相應**增加**]。

1. 在 [相應**增加**] 視窗中，您會看到叢集可用的 sku 清單。 例如，在下圖中，只有四個 Sku 可供使用。

    ![相應增加](media/manage-cluster-vertical-scaling/scale-up.png)

    Sku 已停用，因為它們是目前的 SKU，或在叢集所在的區域中無法使用。

1. 若要變更您的 SKU，請選取新的 SKU，然後按一下 [**選取**]。

> [!NOTE]
> * 垂直調整程式可能需要幾分鐘的時間，而在這段期間，您的叢集將會暫停。 
> * 相應減少可能會危害您的叢集效能。
> * 價格是叢集的虛擬機器和 Azure 資料總管服務成本的預估。 不包含其他成本。 如需完整的定價資訊，請參閱 Azure 資料總管[成本估計工具](https://dataexplorer.azure.com/AzureDataExplorerCostEstimator.html)頁面，以取得預估和 azure 資料總管[定價頁面](https://azure.microsoft.com/pricing/details/data-explorer/)。

您現在已設定 Azure 資料總管叢集的垂直調整。 新增另一個規則來進行水準調整。 如果您需要叢集調整問題的協助，請在 Azure 入口網站中[開啟支援要求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。

## <a name="next-steps"></a>後續步驟

* [管理叢集水準調整](manage-cluster-horizontal-scaling.md)，以根據您指定的計量動態地相應放大實例計數。

* 遵循這篇文章來監視您的資源使用量：[使用計量監視 Azure 資料總管效能、健康情況和使用量](using-metrics.md)。

