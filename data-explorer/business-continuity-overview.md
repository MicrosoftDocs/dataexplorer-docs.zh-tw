---
title: Azure 資料總管和商務持續性嚴重損壞修復
description: 本文說明從干擾性事件中復原的 Azure 資料總管功能。
author: orspod
ms.author: orspodek
ms.reviewer: ankhanol
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/05/2020
ms.openlocfilehash: 39bddff724d30e19f240a25fe3a277fd2151d81e
ms.sourcegitcommit: f7f3ecef858c1e8d132fc10d1e240dcd209163bd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2020
ms.locfileid: "88201348"
---
# <a name="business-continuity-and-disaster-recovery-overview"></a>商務持續性和嚴重損壞修復總覽

Azure 資料總管中的商務持續性和嚴重損壞修復，可讓您的企業在面臨中斷時繼續操作。 本文討論 (內部區域) 和嚴重損壞修復的可用性。 它會詳細說明彈性 Azure 資料總管部署的原生功能和架構考慮。 它會詳細說明從人為錯誤、高可用性，然後再進行多個嚴重損壞修復設定的修復。 這些設定取決於復原點目標 (RPO) 和復原時間目標 (RTO) 、所需的花費和成本。

## <a name="mitigate-disruptive-events"></a>減少干擾性事件

* [人為錯誤](#human-error)
* [Azure 資料總管的高可用性](#high-availability-of-azure-data-explorer)
* [Azure 可用性區域中斷](#outage-of-an-azure-availability-zone)
* [Azure 資料中心中斷](#outage-of-an-azure-datacenter)
* [Azure 區域中斷](#outage-of-an-azure-region)

### <a name="human-error"></a>人為錯誤 

人為錯誤是不可避免的。 使用者可能會不小心卸載叢集、資料庫或資料表。  

#### <a name="accidental-cluster-or-database-deletion"></a>意外的叢集或資料庫刪除

意外的叢集或資料庫刪除是無法復原的動作。 身為 Azure 資料總管資源擁有者，您可以啟用 Azure 資源層級所提供的刪除 [鎖定](/azure/azure-resource-manager/management/lock-resources) 功能，以防止資料遺失。

#### <a name="accidental-table-deletion"></a>意外刪除資料表

具有資料表管理員許可權或更新版本的使用者可以卸載 [資料表](kusto/management/drop-table-command.md)。 如果其中一位使用者不小心卸載資料表，您可以使用命令來復原它 [`.undo drop table`](kusto/management/undo-drop-table-command.md) 。 若要讓此命令成功，您必須先啟用[保留原則](kusto/management/retentionpolicy.md)中的 [復原*能力*] 屬性。

#### <a name="accidental-external-table-deletion"></a>意外刪除外部資料表

[外部資料表](kusto/query/schema-entities/externaltables.md) 是參考儲存在資料庫外部之資料的 Kusto 查詢架構實體。 刪除外部資料表只會刪除資料表中繼資料。 您可以重新執行資料表建立命令來復原它。 使用「虛 [刪除](/azure/storage/blobs/storage-blob-soft-delete) 」功能，可防止意外刪除或覆寫檔案/blob，以達使用者設定的時間量。

### <a name="high-availability-of-azure-data-explorer"></a>Azure 資料總管的高可用性

「高可用性」指的是 azure 資料總管、其元件和 Azure 區域內的基礎相依性的容錯能力。 此容錯功能可避免在執行中 (SPOF) 的單一失敗點。 在 Azure 資料總管中，高可用性包含持續性層、計算層和領導者進行的設定。

#### <a name="persistence-layer"></a>持續性層

Azure 資料總管會利用 Azure 儲存體作為其持久的持續性層。 Azure 儲存體會自動提供容錯功能，預設設定會在資料中心內提供本機的冗余儲存體 (LRS) 。 會保存三個複本。 如果複本在使用中時遺失，則會在不中斷的情況下部署另一個複本。 您可以使用區域冗余儲存體，以智慧方式將複本放在 Azure 區域可用性區域，以取得更高的容錯能力，以進行進一步的復原。

#### <a name="compute-layer"></a>計算層

Azure 資料總管是分散式運算平臺，而且可以有兩個到多個節點，視規模和節點角色類型而定。 在 [布建時間] 中，選取 [可用性區域] 以散發節點部署，跨區域進列區域內復原的最大值。 可用性區域失敗不會導致完全中斷，而是會降低效能，直到區域復原為止。 

#### <a name="leader-follower-cluster-configuration"></a>領導人的叢集設定

Azure 資料總管提供一個選擇性的執行者 [功能](follower.md) ，讓領導者叢集後面接著其他可供存取領導者的資料和中繼資料的其他資料。 領導者中的變更（例如 `create` 、和）會 `append` `drop` 自動同步處理到進行中。 雖然領導者可以跨越 Azure 區域，但您應該在與領導者相同的區域中， (的) 。 如果領導者叢集已關閉，或資料庫或資料表不小心遭到卸載，則在領導者中復原存取之前，會失去存取權的叢集。 

### <a name="outage-of-an-azure-availability-zone"></a>Azure 可用性區域中斷

Azure 可用性區域是位於相同 Azure 區域內的唯一實體位置。 他們可以保護 Azure 資料總管叢集的計算，以及部分區域失敗的資料。 區域失敗是可用性案例，因為它是在區域內部。 

將 Azure 資料總管叢集釘選到與其他已連線 Azure 資源相同的區域。 如需有關啟用可用性區域的詳細資訊，請參閱 [建立](create-cluster-database-portal.md#create-a-cluster)叢集。

> [!NOTE] 
> 只有在建立叢集時才支援可用性區域選取，且稍後無法修改。

### <a name="outage-of-an-azure-datacenter"></a>Azure 資料中心中斷

Azure 可用性區域具有成本，而某些客戶選擇部署而不需要區域冗余。 有了這類 Azure 資料總管部署，Azure 資料中心中斷會導致叢集中斷。 因此，處理 Azure 資料中心中斷的情況與 Azure 區域中斷的情況完全相同。

### <a name="outage-of-an-azure-region"></a>Azure 區域中斷

Azure 資料總管不會提供整個 Azure 區域中斷的自動保護。 若要在發生這類中斷時將業務影響降至最低，請在 [azure 配對區域](/azure/best-practices-availability-paired-regions)中多個 azure 資料總管叢集。 根據您的復原時間目標 (RTO) 、復原點目標 (RPO) ，以及工作和成本考慮，有 [多個](#disaster-recovery-configurations)嚴重損壞修復設定。 您可以使用 Azure Advisor 建議和 [自動](manage-cluster-horizontal-scaling.md) 調整設定來進行成本和效能優化。

## <a name="disaster-recovery-configurations"></a>嚴重損壞修復設定

 本節詳細說明多個嚴重損壞修復設定，視復原需求而定 (RPO 和 RTO) 、所需的人力和成本。

復原時間目標 (RTO) 指的是從中斷情況中復原的時間。 例如，2小時的 RTO 表示應用程式必須在中斷的兩個小時內啟動並執行。 復原點目標 (RPO) 指的是在該期間遺失的資料量超過允許的閾值之前，中斷期間可能經歷的時間間隔。 例如，如果 RPO 是24小時，而應用程式的資料是從15年前開始，則它們仍然在已同意 RPO 的參數內。

在規劃嚴重損壞修復時，內嵌、處理和鑒藏程式需要預先用心的設計。 [內嵌] 是指從各種來源整合到 Azure 資料總管的資料;處理是指轉換和類似的活動;鑒藏是指具體化的視圖、匯出至 data lake 等等。

以下是熱門的嚴重損壞修復設定，其詳細說明如下。
* [Always on 設定](#always-on-configuration)
* [主動-主動設定](#active-active-configuration)
* [主動-熱待命設定](#active-hot-standby-configuration)
* [隨選資料恢復叢集設定](#on-demand-data-recovery-configuration)

### <a name="always-on-configuration"></a>Always on 設定

針對不容許中斷的重大應用程式部署，您應該在 Azure 配對區域中使用多個 Azure 資料總管叢集。 將內嵌、處理和鑒藏平行設定為所有叢集。 跨區域的叢集 SKU 必須相同。 Azure 會確保更新會在 Azure 配對的區域中推出和錯開。 Azure 區域中斷不會導致應用程式中斷。 您可能會遇到一些延遲或效能降低的情況。

:::image type="content" source="media/business-continuity-overview/active-active-active-n.png" alt-text="主動-主動-主動-n 設定":::

| **Configuration** | **復原點目標 (RPO)** | **復原時間目標 (RTO)** | **氣** | **成本** |
| --- | --- | --- | --- | --- |
| **主動-主動-主動-n** | 0小時 | 0小時 | 較低 | 最高 |

### <a name="active-active-configuration"></a>主動-主動設定

此設定與 [Always on](#always-on-configuration)設定相同，但只牽涉到兩個 Azure 配對的區域。 設定雙重內嵌、處理和鑒藏。 使用者會路由至最接近的區域。 跨區域的叢集 SKU 必須相同。

:::image type="content" source="media/business-continuity-overview/active-active.png" alt-text="主動-主動設定":::

| **Configuration** | **復原點目標 (RPO)** | **復原時間目標 (RTO)** | **氣** | **成本** |
| --- | --- | --- | --- | --- |
| **主動-主動** | 無 | 無 | 較低 | 高 |

### <a name="active-hot-standby-configuration"></a>主動-熱待命設定

作用中的設定類似于雙重內嵌、處理和鑒藏中的 [主動-主動](#active-active-configuration) 設定。 不過，待命叢集對使用者而言是離線的，而且不需要位於與主資料庫相同的 SKU 中。 熱待命叢集也可以是較小的 SKU 和規模，因此效能較低。 在嚴重損壞的情況下，待命叢集會上線並相應增加。

:::image type="content" source="media/business-continuity-overview/active-hot-standby.png" alt-text="主動-熱待命設定":::

| **Configuration** | **復原點目標 (RPO)** | **復原時間目標 (RTO)** | **氣** | **成本** |
| --- | --- | --- | --- | --- |
| **主動-熱待命** | 低度 | 低度 | 適中 | 適中 |

### <a name="on-demand-data-recovery-configuration"></a>隨選資料恢復設定

此解決方案提供最小的復原 (最高的 RPO 和 RTO) ，這是成本最低和最高的工作。 在此設定中，沒有任何資料恢復叢集。 設定連續匯出策劃資料 (，除非) 的儲存體帳戶也需要原始和中繼資料，才能) 設定 (GRS 的異地多餘儲存體。 如果發生嚴重損壞修復案例，資料復原叢集就會啟動。 此時，會套用 Ddl、設定、原則和處理常式。 資料會從儲存體內嵌，並使用 [內嵌屬性 [kustoCreationTime](ingest-data-event-grid-overview.md) ]，將預設為 [系統時間] 的內嵌時間設為 [高]。 

:::image type="content" source="media/business-continuity-overview/on-demand-data-recovery-cluster.png" alt-text="隨選資料恢復叢集設定":::

| **Configuration** | **復原點目標 (RPO)** | **復原時間目標 (RTO)** | **氣** | **成本** |
| --- | --- | --- | --- | --- |
| **隨選資料恢復叢集** | 最高 | 最高 | 最高 | 最低 |

### <a name="summary-of-disaster-recovery-configuration-options"></a>嚴重損壞修復設定選項的摘要

| **Configuration** | **復原** | **復原點目標 (RPO)** | **復原時間目標 (RTO)** | **氣** | **成本** |
| --- | --- | --- | --- | --- | --- |
| **主動-主動-主動-n** | 最高 | 0小時 | 0小時 | 較低 | 最高 |
| **主動-主動** | 高 | 無 | 無 | 較低 | 高 |
| **主動-熱待命** | 中型 | 低度 | 低度 | 適中 | 適中 |
| **隨選資料恢復叢集** | 最低 | 最高 | 最高 | 最高 | 最低 |

## <a name="best-practices"></a>最佳作法

不論選擇哪一種嚴重損壞修復設定，請遵循下列最佳作法：

* 所有資料庫物件、原則和設定都應該保存在原始檔控制中，以便從您的發行自動化工具將它們發行至叢集。 如需詳細資訊，請參閱 [Azure 資料總管的 Azure DevOps 支援](devops.md)。 
* 設計、開發及執行驗證常式，以確保所有叢集都是從資料觀點來同步。 Azure 資料總管支援 [跨](kusto/query/cross-cluster-or-database-queries.md?pivots=azuredataexplorer)叢集聯結。 資料表間的簡單計數或資料列可以協助驗證。
* 使用 [連續匯出](kusto/management/data-export/continuous-data-export.md) 功能，並將 Azure 資料總管資料表內的資料匯出至 Azure Data Lake 存放區。 請確定選取 GRS 以取得最高的復原能力。
* 發行程式應涉及治理檢查和平衡，以確保叢集的鏡像。
* 從頭開始建立叢集所需的完整 cognizant。
* 建立部署單位的檢查清單。 您的清單對您的需求而言是唯一的，但應包括：部署腳本、內嵌連線、BI 工具和其他重要設定。 

## <a name="next-steps"></a>後續步驟

深入瞭解使用 [Azure 資料總管建立商務持續性和嚴重損壞修復解決方案](business-continuity-create-solution.md)。
