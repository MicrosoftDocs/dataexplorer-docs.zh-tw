---
title: Azure 資料總管和商務持續性嚴重損壞修復
description: 本文說明從干擾性事件中復原的 Azure 資料總管功能。
author: orspod
ms.author: orspodek
ms.reviewer: ankhanol
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/05/2020
ms.openlocfilehash: 2dcb1fb83d592ff7fd81fcfc5f51cf6fc95254b7
ms.sourcegitcommit: a36981785765b85a961f275be24d297d38e498fd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96310029"
---
# <a name="business-continuity-and-disaster-recovery-overview"></a>商務持續性和嚴重損壞修復的總覽

Azure 中的商務持續性和嚴重損壞修復資料總管可讓您的企業在發生中斷時繼續運作。 本文討論可用性 (內部區域) 和嚴重損壞修復。 它會詳述復原 Azure 資料總管部署的原生功能和架構考慮。 它會詳細說明從人為錯誤、高可用性，然後再進行多個嚴重損壞修復設定的修復。 這些設定取決於復原需求，例如復原點目標 (RPO) 和復原時間目標 (RTO) 、所需的投入時間和成本。

## <a name="mitigate-disruptive-events"></a>減緩干擾性事件

* [人為錯誤](#human-error)
* [Azure 資料總管的高可用性](#high-availability-of-azure-data-explorer)
* [Azure 可用性區域中斷](#outage-of-an-azure-availability-zone)
* [Azure 資料中心中斷](#outage-of-an-azure-datacenter)
* [Azure 區域中斷](#outage-of-an-azure-region)

### <a name="human-error"></a>人為錯誤 

人類的錯誤是不可避免的。 使用者可能會不小心卸載叢集、資料庫或資料表。  

#### <a name="accidental-cluster-or-database-deletion"></a>意外刪除叢集或資料庫

意外的叢集或資料庫刪除是無法復原的動作。 如同 Azure 資料總管資源擁有者，您可以啟用 Azure 資源層級所提供的刪除 [鎖定](/azure/azure-resource-manager/management/lock-resources) 功能，以避免資料遺失。

#### <a name="accidental-table-deletion"></a>意外刪除資料表

具有資料表管理員許可權或更高許可權的使用者可以卸載 [資料表](kusto/management/drop-table-command.md)。 如果其中一個使用者不小心卸載資料表，您可以使用命令來復原 [`.undo drop table`](kusto/management/undo-drop-table-command.md) 。 若要讓此命令成功，您必須先啟用 [保留原則](kusto/management/retentionpolicy.md)中的可復原 *性* 屬性。

#### <a name="accidental-external-table-deletion"></a>意外刪除外部資料表

[外部資料表](kusto/query/schema-entities/externaltables.md) 是參考儲存在資料庫外部之資料的 Kusto 查詢架構實體。 刪除外部資料表只會刪除資料表中繼資料。 您可以重新執行資料表建立命令來加以復原。 使用虛 [刪除](/azure/storage/blobs/storage-blob-soft-delete) 功能，防止在使用者設定的時間內意外刪除或覆寫檔案/blob。

### <a name="high-availability-of-azure-data-explorer"></a>Azure 資料總管的高可用性

高可用性是指 azure 資料總管、其元件，以及 Azure 區域內的基礎相依性的容錯能力。 這項容錯可避免在執行 (SPOF) 的單一失敗點。 在 Azure 資料總管中，高可用性包括持續性層、計算層級，以及領導者的設定。

#### <a name="persistence-layer"></a>持續性層

Azure 資料總管利用 Azure 儲存體作為其永久性持續性層。 Azure 儲存體會自動提供容錯功能，預設設定會在資料中心內提供本機冗余儲存體 (LRS) 。 系統會保存三個複本。 如果複本在使用時遺失，則會在不中斷的情況下部署另一個複本。 區域多餘的儲存體會以智慧方式將複本放在 Azure 區域的可用性區域，以提供更高的容錯能力，以提供更高的復原能力。

#### <a name="compute-layer"></a>計算層

Azure 資料總管是一種分散式運算平臺，而且可以有兩個到多個節點，視規模和節點角色類型而定。 在布建階段，選取 [可用性區域]，以在區域內復原的最大區域間散發節點部署。 可用性區域失敗不會導致完全中斷，而是在區域復原之前降低效能。 

#### <a name="leader-follower-cluster-configuration"></a>領導人對等叢集設定

針對領導者的資料和中繼資料進行唯讀存取，Azure 資料總管為領導者叢集提供了選用的執行程式 [功能](follower.md) ，以供其他的存取叢集使用。 領導人中的變更（例如 `create` 、和）會 `append` `drop` 自動同步處理到合作。 雖然領導人可以跨越 Azure 區域，但您應該將合作的叢集裝載在與領導者相同的 () 區域中。 如果領導者叢集已關閉，或資料庫或資料表不小心卸載，則在領導者中復原存取之前，將會失去存取權叢集的存取權。 

### <a name="outage-of-an-azure-availability-zone"></a>Azure 可用性區域中斷

Azure 可用性區域是相同 Azure 區域內的唯一實體位置。 它們可以保護 Azure 資料總管叢集的計算和資料免于部分區域失敗。 區域失敗是一種可用性案例，因為它在區域內。 

將 Azure 資料總管叢集釘選到與其他已連線 Azure 資源相同的區域。 如需有關啟用可用性區域的詳細資訊，請參閱 [建立](create-cluster-database-portal.md#create-a-cluster)叢集。

> [!NOTE] 
> 只有在建立叢集時才支援「可用性區域」選取，之後無法再修改。

### <a name="outage-of-an-azure-datacenter"></a>Azure 資料中心中斷

Azure 可用性區域具有成本，而某些客戶會選擇部署而不需要區域冗余。 透過這類 Azure 資料總管部署，Azure 資料中心中斷會導致叢集中斷。 因此，Azure 資料中心中斷的處理會與 Azure 區域中斷的情況完全相同。

### <a name="outage-of-an-azure-region"></a>Azure 區域中斷

Azure 資料總管不會針對整個 Azure 區域中斷提供自動保護。 若要在發生這類中斷時將業務影響降到最低，請在 [azure 配對區域](/azure/best-practices-availability-paired-regions)中多個 azure 資料總管叢集。 根據您的復原時間目標 (RTO) 、復原點目標 (RPO) 以及投入時間和成本考慮，有 [多個](#disaster-recovery-configurations)嚴重損壞修復設定。 您可以使用 Azure Advisor 建議和 [自動](manage-cluster-horizontal-scaling.md) 調整設定，來進行成本和效能優化。

## <a name="disaster-recovery-configurations"></a>嚴重損壞修復設定

 本節將詳細說明多個嚴重損壞修復設定，視復原需求而定 (RPO 和 RTO) 、所需的投入時間和成本。

復原時間目標 (RTO) 指的是從中斷復原的時間。 例如，2小時的 RTO 表示應用程式必須在中斷的兩小時內啟動並執行。 復原點目標 (RPO) 指的是在該期間內遺失的資料量大於允許的閾值之前，可能會在中斷期間通過的時間間隔。 例如，如果 RPO 為24小時，而應用程式的資料從15年前開始，則它們仍在已同意的 RPO 的參數內。

內嵌、處理和鑒藏程式在規劃嚴重損壞修復時，需要預先用心的設計。 內嵌是指從各種來源整合到 Azure 資料總管的資料;處理是指轉換和類似的活動;鑒藏指的是具體化的視圖、匯出至 data lake 等等。

以下是常見的嚴重損壞修復設定，各有詳細說明。
* [主動-主動-主動 (always on) 設定](#active-active-active-configuration)
* [主動-主動設定](#active-active-configuration)
* [主動-熱待命設定](#active-hot-standby-configuration)
* [隨選資料恢復叢集設定](#on-demand-data-recovery-configuration)

### <a name="active-active-active-configuration"></a>主動-主動-主動設定

這項設定也稱為「永遠開啟」。 針對不容許中斷的重大應用程式部署，您應該在 Azure 配對區域中使用多個 Azure 資料總管叢集。 設定與所有叢集平行的內嵌、處理及鑒藏。 跨區域的叢集 SKU 必須相同。 Azure 可確保在 Azure 配對的區域中推出更新並錯開。 Azure 區域中斷不會導致應用程式中斷。 您可能會遇到一些延遲或效能降低的情況。

:::image type="content" source="media/business-continuity-overview/active-active-active-n.png" alt-text="主動-主動-主動-n 設定":::

| **設定** | **復原點目標 (RPO)** | **復原時間目標 (RTO)** | **努力** | **成本** |
| --- | --- | --- | --- | --- |
| **主動-主動-主動-n** | 0小時 | 0小時 | 較低 | 最高 |

### <a name="active-active-configuration"></a>Active-Active 設定

這項設定等同于 [主動-主動-主動](#active-active-active-configuration)設定，但只牽涉到兩個 Azure 配對的區域。 設定雙重內嵌、處理及鑒藏。 使用者會路由至最接近的區域。 跨區域的叢集 SKU 必須相同。

:::image type="content" source="media/business-continuity-overview/active-active.png" alt-text="主動-主動設定":::

| **設定** | **復原點目標 (RPO)** | **復原時間目標 (RTO)** | **努力** | **成本** |
| --- | --- | --- | --- | --- |
| **主動-主動** | 無 | 無 | 較低 | 高 |

### <a name="active-hot-standby-configuration"></a>Active-Hot 待命設定

Active-Hot 設定類似于 [主動-主動](#active-active-configuration) 設定的雙重內嵌、處理及鑒藏。 不過，待命叢集會離線給終端使用者，且不需要與主資料庫位於相同的 SKU 中。 熱待命叢集也可以是較小的 SKU 和規模，因此效能較低。 在嚴重損壞的情況下，待命叢集會上線，並向上擴充。

:::image type="content" source="media/business-continuity-overview/active-hot-standby.png" alt-text="主動-熱待命設定":::

| **設定** | **復原點目標 (RPO)** | **復原時間目標 (RTO)** | **努力** | **成本** |
| --- | --- | --- | --- | --- |
| **主動-熱待命** | 低度 | 低度 | 適中 | 適中 |

### <a name="on-demand-data-recovery-configuration"></a>隨選資料恢復設定

此解決方案提供最少的復原 (最高的 RPO 和 RTO) ，是最低成本和最高的工作量。 在此設定中，不會有資料復原叢集。 設定策劃資料的連續匯出 (除非也需要原始和中繼資料，) 到設定 GRS (異地冗余儲存體) 的儲存體帳戶。 如果發生嚴重損壞修復案例，就會啟動資料修復叢集。 屆時會套用 Ddl、設定、原則和處理常式。 從儲存體內嵌資料時，會使用 [內嵌屬性 [kustoCreationTime](ingest-data-event-grid-overview.md) ]，將預設為系統時間的內嵌時間過度放置。 

:::image type="content" source="media/business-continuity-overview/on-demand-data-recovery-cluster.png" alt-text="隨選資料恢復叢集設定":::

| **設定** | **復原點目標 (RPO)** | **復原時間目標 (RTO)** | **努力** | **成本** |
| --- | --- | --- | --- | --- |
| **隨選資料修復叢集** | 最高 | 最高 | 最高 | 最低 |

### <a name="summary-of-disaster-recovery-configuration-options"></a>嚴重損壞修復設定選項的摘要

| **設定** | **復原** | **復原點目標 (RPO)** | **復原時間目標 (RTO)** | **努力** | **成本** |
| --- | --- | --- | --- | --- | --- |
| **主動-主動-主動-n** | 最高 | 0小時 | 0小時 | 較低 | 最高 |
| **主動-主動** | 高 | 無 | 無 | 較低 | 高 |
| **主動-熱待命** | 中 | 低 | 低度 | 適中 | 適中 |
| **隨選資料修復叢集** | 最低 | 最高 | 最高 | 最高 | 最低 |

## <a name="best-practices"></a>最佳作法

不論選擇哪一種嚴重損壞修復設定，請遵循下列最佳作法：

* 所有資料庫物件、原則和設定都應保存在原始檔控制中，以便從您的發行自動化工具將它們發行到叢集。 如需詳細資訊，請參閱 [Azure 的 Azure DevOps 支援資料總管](devops.md)。 
* 設計、開發和實行驗證常式，以確保所有叢集都能從資料的觀點進行同步處理。 Azure 資料總管支援 [跨](kusto/query/cross-cluster-or-database-queries.md?pivots=azuredataexplorer)叢集聯結。 資料表之間的簡單計數或資料列有助於進行驗證。
* 使用 [連續匯出](kusto/management/data-export/continuous-data-export.md) 功能，並將 Azure 資料總管資料表中的資料匯出至 Azure Data Lake 存放區。 請確定選取 GRS 以獲得最高的復原能力。
* 發行程式應涉及治理檢查和平衡，以確保叢集的鏡像。
* 完全 cognizant 從頭開始建立叢集所需的內容。
* 建立部署單位的檢查清單。 您的清單對您的需求是唯一的，但應包括：部署腳本、內嵌連接、BI 工具和其他重要設定。 

## <a name="next-steps"></a>後續步驟

深入瞭解如何 [使用 Azure 資料總管建立商務持續性和嚴重損壞修復解決方案](business-continuity-create-solution.md)。
