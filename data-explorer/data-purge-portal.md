---
title: 使用資料清除，從 Azure 中的裝置或服務刪除個人資料資料總管
description: 瞭解如何使用資料清除來清除（刪除） Azure 資料總管中的資料。
author: orspod
ms.author: orspodek
ms.reviewer: kedamari
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/12/2020
ms.openlocfilehash: 3f170a48f31f9d842e39fcf42fcfce0c383c71ed
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83232382"
---
# <a name="enable-data-purge-on-your-azure-data-explorer-cluster"></a>在您的 Azure 資料總管叢集上啟用資料清除

[!INCLUDE [gdpr-intro-sentence](includes/gdpr-intro-sentence.md)]

Azure 資料總管支援刪除個別記錄的能力。 透過命令刪除資料 `.purge` 可保護個人資料，且不應在其他案例中使用。 它的設計不是為了支援頻繁的刪除要求，或刪除大量的資料，而且可能會對服務產生顯著的效能影響。

執行 `.purge` 命令會觸發可能需要幾天才能完成的進程。 如果套用的記錄「密度」 `predicate` 很大，此程式將會 reingest 資料表中的所有資料。 此程式對效能和 COGS 有重大影響。 如需詳細資訊，請參閱[Azure 中的資料清除資料總管](kusto/concepts/data-purge.md)。

## <a name="methods-of-invoking-purge-operations"></a>叫用清除作業的方法 

Azure 資料總管（Kusto）支援個別記錄刪除和清除整份資料表。 您 `.purge` 可以透過[兩種方式](kusto/concepts/data-purge.md#purge-table-tablename-records-command)來叫用命令，以用於不同的使用案例：

* 以程式設計方式叫用：要由應用程式叫用的單一步驟。 呼叫此命令會直接觸發清除執行順序。

* 人為調用：需要明確確認做為個別步驟的兩步驟程式。 命令的調用會傳回驗證 token，應提供此權杖來執行實際的清除。 此程式可降低不小心刪除不正確資料的風險。 使用此選項可能需要花很長的時間才能在具有大量冷快取資料的大型資料表上完成。 

## <a name="prerequisites"></a>Prerequisites

* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。
* 登入[WEB UI](https://dataexplorer.azure.com/)。
* 建立[Azure 資料總管叢集和資料庫](create-cluster-database-portal.md)

## <a name="enable-data-purge-on-your-cluster"></a>在您的叢集上啟用資料清除

> [!WARNING]
> * 啟用資料清除需要重新開機服務，這可能會導致查詢卸載。
> * 請先檢查[限制](#limitations)，再啟用資料清除。

1. 在 Azure 入口網站中，移至您的 Azure 資料總管叢集。 
1. 在 [設定] 中 **，選取 [****設定**]。 
1. **在 [設定**] 窗格中，選取 [**開啟**] 以啟用 [**啟用清除**]。
1. 選取 [儲存]  。
 
    ![啟用清除](media/data-purge-portal/enable-purge-on.png)

## <a name="disable-data-purge-on-your-cluster"></a>停用叢集上的資料清除

1. 在 Azure 入口網站中，移至您的 Azure 資料總管叢集。 
1. 在 [設定] 中 **，選取 [****設定**]。 
1. **在 [設定**] 窗格中，選取 [**關閉**] 以停用 [**啟用清除**]。
1. 選取 [儲存]  。

    ![啟用清除功能](media/data-purge-portal/enable-purge-off.png)

## <a name="limitations"></a>限制

* 清除程式為最終且無法復原。 無法「復原」此進程或復原已清除的資料。 因此，[復原資料表](kusto/management/undo-drop-table-command.md)卸載之類的命令無法復原已清除的資料，而將資料復原到先前的版本，則無法移至最新的清除。
* 此 `.purge` 命令會針對資料管理端點執行： * https://ingest- [YourClusterName]. [Region]. kusto. net*。 此命令需要相關資料庫的[資料庫管理員](kusto/management/access-control/role-based-authorization.md)許可權。 
* 由於清除程式效能的影響，呼叫端應該會修改資料結構描述，讓最少的資料表包含相關資料，以及每個資料表的批次命令，以減少清除程式的重大 COGS 影響。
* `predicate`清除命令的參數是用來指定要清除的記錄。 `Predicate`大小限制為 63 KB。 

## <a name="next-steps"></a>後續步驟

* [Azure 資料總管中的資料清除](kusto/concepts/data-purge.md)
