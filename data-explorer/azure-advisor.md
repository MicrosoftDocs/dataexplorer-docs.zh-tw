---
title: 使用 Azure Advisor 建議來優化您的 Azure 資料總管叢集
description: 本文說明用來優化 Azure 資料總管叢集的 Azure Advisor 建議
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: lizlotor
ms.service: data-explorer
ms.topic: how-to
ms.date: 09/14/2020
ms.openlocfilehash: 1d7fafcab3293a66bafb4b60f86413d00ee9c354
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241879"
---
# <a name="use-azure-advisor-recommendations-to-optimize-your-azure-data-explorer-cluster-preview"></a>使用 Azure Advisor 建議來優化您的 Azure 資料總管叢集 (預覽) 

Azure Advisor 會分析 Azure 資料總管叢集設定和使用量遙測，並提供個人化且可採取動作的建議，以協助您優化您的叢集。

## <a name="access-the-azure-advisor-recommendations"></a>存取 Azure Advisor 建議

有兩種方式可以存取 Azure Advisor 建議。

### <a name="view-azure-advisor-recommendations-for-your-azure-data-explorer-cluster"></a>查看 Azure 資料總管叢集的 Azure Advisor 建議

1. 在 Azure 入口網站中，移至您的 Azure 資料總管叢集頁面。 
1. 在左側功能表中，選取 [ **監視**] 底下的 [ **Advisor 建議**]。 針對該群集開啟的建議清單。

    :::image type="content" source="media/azure-advisor/resource-group-advisor-recommendations.png" alt-text="適用于 Azure 資料總管叢集的 Azure Advisor 建議"::: 

### <a name="view-azure-advisor-recommendations-for-all-clusters-in-your-subscription"></a>查看訂用帳戶中所有叢集的 Azure Advisor 建議

1. 在 Azure 入口網站中，移至 [Advisor 資源](https://ms.portal.azure.com/#blade/Microsoft_Azure_Expert/AdvisorMenuBlade/overview)。 
1. 在 **[總覽**] 中，選取您需要建議的訂用帳戶 () 。 
1. 在第二個下拉式清單中，選取 **azure 資料總管** 叢集和 **azure 資料總管資料庫** 。
 
    :::image type="content" source="media/azure-advisor/advisor-resource.png" alt-text="適用于 Azure 資料總管叢集的 Azure Advisor 建議":::

## <a name="use-the-azure-advisor-recommendations"></a>使用 Azure Advisor 建議

有各種 Azure Advisor 的建議類型。 使用相關的 [建議類型] 來協助您優化您的叢集。 

1. 在 [建議程式 **] 的 [****建議**] 下方，選取成本建議的**成本**。

    :::image type="content" source="media/azure-advisor/select-recommendation-type.png" alt-text="適用于 Azure 資料總管叢集的 Azure Advisor 建議":::

1. 從清單中選取建議。 

    :::image type="content" source="media/azure-advisor/select-recommendation.png" alt-text="適用于 Azure 資料總管叢集的 Azure Advisor 建議":::

1. 下列視窗包含與建議相關的群集清單。 每個叢集的建議詳細資料各不相同，且包含建議的動作。

    :::image type="content" source="media/azure-advisor/clusters-with-recommendations.png" alt-text="適用于 Azure 資料總管叢集的 Azure Advisor 建議":::

## <a name="recommendation-types"></a>建議類型

目前提供成本和效能建議。

> [!IMPORTANT]
> 您實際的每年節省金額可能會有所不同。 所顯示的每年費用都是以「隨用隨付」價格為基礎。 可能的儲存不會將 Azure 保留的虛擬機器實例納入考慮 (RIs) 帳單折扣。

### <a name="cost-recommendations"></a>成本建議

**成本**建議適用于可以變更的叢集，以降低成本，而不會影響效能。 成本建議包括： 

* [Azure 資料總管未使用的叢集](#azure-data-explorer-unused-cluster)
* [包含低活動資料的 Azure 資料總管叢集](#azure-data-explorer-clusters-containing-data-with-low-activity)
* [正確調整 Azure 資料總管叢集的大小以將成本優化](#correctly-size-azure-data-explorer-clusters-to-optimize-cost)
* [減少 Azure 資料總管資料表的快取](#reduce-cache-for-azure-data-explorer-tables)

#### <a name="azure-data-explorer-unused-cluster"></a>Azure 資料總管未使用的叢集

如果叢集在過去30天內有少量的資料、查詢和內嵌事件、過去兩天內的低 CPU 使用率，以及過去一天沒有任何關注者，則會將叢集視為未使用。 建議您 **考慮刪除空的/未使用** 的叢集有建議的動作來刪除未使用的叢集。

#### <a name="azure-data-explorer-clusters-containing-data-with-low-activity"></a>包含低活動資料的 Azure 資料總管叢集

建議您 **停止 Azure 資料總管叢集以降低成本，並** 針對包含資料但活動較低的叢集提供其資料。 低活動是過去30天內少量的查詢和內嵌、過去兩天內的低 CPU 使用量，以及過去一天沒有任何關注者。 建議您停止叢集以降低成本，但仍保留資料。 如果不需要資料，請考慮刪除叢集以增加您的節省量。

#### <a name="correctly-size-azure-data-explorer-clusters-to-optimize-cost"></a>正確調整 Azure 資料總管叢集的大小以將成本優化

針對大小或 VM SKU 未進行成本優化的叢集，建議使用 **適當大小的 Azure 資料總管叢集以獲得最佳成本** 。 這項建議是以過去一周的資料容量、CPU 和內嵌使用率等參數為基礎。 您可以使用向[下](manage-cluster-vertical-scaling.md)[調整和相應縮小調整](manage-cluster-horizontal-scaling.md)為建議的叢集設定，以降低成本。

建議使用 [優化的自動](manage-cluster-horizontal-scaling.md#optimized-autoscale)調整規模設定。 如果您使用優化的自動調整，且您在叢集上看到大小建議，您目前的 VM SKU 或優化的自動調整最小值和最大實例計數界限都未優化。 建議的實例計數應該包含在您定義的界限內。 如需詳細資訊，請參閱 [VM sku](manage-cluster-choose-sku.md) 和 [定價](https://azure.microsoft.com/pricing/details/data-explorer/)。

> [!TIP]
> 優化自動調整設定不會立即變更實例計數。 若為立即變更，請使用手動調整大小來重設建議的實例計數，然後啟用優化的自動 [調整](manage-cluster-horizontal-scaling.md#manual-scale) 功能以進行未來的優化。

#### <a name="reduce-cache-for-azure-data-explorer-tables"></a>減少 Azure 資料總管資料表的快取

針對可縮減其資料表快取[原則](kusto/management/cachepolicy.md)的叢集，提供叢集**成本優化建議的縮減 Azure 資料總管資料表**快取期間。 這項建議是以過去30天內的查詢回頭查看期間為基礎。 您會看到有可能節省快取的前10個數據表。 只有在叢集可以在快取原則變更之後相應縮小或相應減少時，才會提供這項建議。 Advisor 會檢查叢集是否「由資料系結」，這表示叢集具有低 CPU 和低內嵌使用率，但因為資料容量很高，所以叢集無法相應縮小或向下調整。

### <a name="performance-recommendations"></a>效能建議

**效能**建議可協助改善 Azure 資料總管叢集的效能。 效能建議包括： 
* [正確調整 Azure 資料總管叢集的大小以將效能優化](#correctly-size-azure-data-explorer-clusters-to-optimize-performance)
* [更新 Azure 資料總管資料表的快取原則](#update-cache-policy-for-azure-data-explorer-tables)

#### <a name="correctly-size-azure-data-explorer-clusters-to-optimize-performance"></a>正確調整 Azure 資料總管叢集的大小以將效能優化

**為了達到最佳效能，建議使用適當大小的 Azure 資料總管**叢集，其大小或 VM SKU 不會優化效能。 這項建議是根據參數，例如其資料容量，以及過去一周內的 CPU 和內嵌使用率。 您可以使用向上[延展和向外延展，將](manage-cluster-vertical-scaling.md)建議的叢集設定適當地重[scale-out](manage-cluster-horizontal-scaling.md)設大小，以改善效能。

建議使用 [優化的自動](manage-cluster-horizontal-scaling.md#optimized-autoscale)調整規模設定。 如果您使用優化的自動調整，且您在叢集上看到大小建議，您目前的 VM SKU 或優化的自動調整最小值和最大實例計數界限都未優化。 建議的實例計數應該包含在您定義的界限內。 如需詳細資訊，請參閱 [VM sku](manage-cluster-choose-sku.md)。

> [!TIP]
> 優化自動調整設定不會立即變更實例計數。 若為立即變更，請使用手動調整大小來重設建議的實例計數，然後啟用優化的自動 [調整](manage-cluster-horizontal-scaling.md#manual-scale) 功能以進行未來的優化。

#### <a name="update-cache-policy-for-azure-data-explorer-tables"></a>更新 Azure 資料總管資料表的快取原則

**審核 Azure 資料總管資料表快取期間 (原則) 以取得較佳的效能**建議的叢集，該叢集需要不同的反向查看期間 (時間篩選) 或更高的快取[原則](kusto/management/cachepolicy.md)。 這項建議是以過去30天內的查詢回頭查看期間為基礎。 過去30天內執行的大部分查詢都會存取不在快取中的資料，這可能會增加您的查詢執行時間。  您會看到存取非快取資料的前10個數據表（依查詢百分比排序）。

## <a name="next-steps"></a>後續步驟

* [在 Azure 資料總管中管理叢集水準規模調整 (scale out) ，以滿足不斷變化的需求](manage-cluster-horizontal-scaling.md)
* [管理叢集垂直調整 (在 Azure 資料總管中擴大) ，以滿足不斷變化的需求](manage-cluster-vertical-scaling.md)
