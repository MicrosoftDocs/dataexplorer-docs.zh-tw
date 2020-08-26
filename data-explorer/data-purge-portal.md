---
title: 使用資料清除從 Azure 中的裝置或服務刪除個人資料資料總管
description: 瞭解如何使用資料清除，從 Azure 資料總管清除 (刪除) 資料。
author: orspod
ms.author: orspodek
ms.reviewer: kedamari
ms.service: data-explorer
ms.topic: how-to
ms.date: 05/12/2020
ms.openlocfilehash: 6bf447a845954bde58a0308a03bee45f98f16111
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88873228"
---
# <a name="enable-data-purge-on-your-azure-data-explorer-cluster"></a>在您的 Azure 資料總管叢集上啟用資料清除

[!INCLUDE [gdpr-intro-sentence](includes/gdpr-intro-sentence.md)]

Azure 資料總管支援刪除個別記錄的功能。 透過命令刪除資料會 `.purge` 保護個人資料，而且不應該用於其他案例。 其設計目的不是為了支援頻繁的刪除要求或刪除大量的資料，而且可能會對服務造成顯著的效能影響。

執行 `.purge` 命令會觸發可能需要幾天才能完成的進程。 如果套用之記錄的「密度」 `predicate` 很大，此程式會 reingest 資料表中的所有資料。 此程式對效能和 COGS 有顯著的影響。 如需詳細資訊，請參閱 [Azure 資料總管中的資料清除](kusto/concepts/data-purge.md)。

## <a name="methods-of-invoking-purge-operations"></a>叫用清除作業的方法 

Azure 資料總管 (Kusto) 支援個別記錄刪除和清除整個資料表。 您 `.purge` 可以透過 [兩種方式](kusto/concepts/data-purge.md#purge-table-tablename-records-command) 來叫用不同使用案例的命令：

* 程式設計調用：單一步驟，可供應用程式叫用。 呼叫此命令會直接觸發清除執行順序。

* 人類調用：需要以個別步驟明確確認的兩步驟程式。 命令調用會傳回驗證權杖，此權杖應提供給執行實際的清除。 此程式可降低不慎刪除不正確資料的風險。 使用這個選項可能需要很長的時間才能完成具有大量冷快取資料的大型資料表。 

## <a name="prerequisites"></a>Prerequisites

* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。
* 登入 [WEB UI](https://dataexplorer.azure.com/)。
* 建立 [Azure 資料總管叢集和資料庫](create-cluster-database-portal.md)

## <a name="enable-data-purge-on-your-cluster"></a>在您的叢集上啟用資料清除

> [!WARNING]
> * 啟用資料清除需要重新開機服務，可能會導致查詢卸載。
> * 請先檢查 [限制](#limitations) ，再啟用資料清除。

1. 在 Azure 入口網站中，移至您的 Azure 資料總管叢集。 
1. 在 [設定] 中 **，選取 [****設定**]。 
1. **在 [設定**] 窗格中，選取 [**開啟**] 以啟用 [**啟用清除**]。
1. 選取 \[儲存\]。
 
    ![啟用清除](media/data-purge-portal/enable-purge-on.png)

## <a name="disable-data-purge-on-your-cluster"></a>停用叢集中的資料清除

1. 在 Azure 入口網站中，移至您的 Azure 資料總管叢集。 
1. 在 [設定] 中 **，選取 [****設定**]。 
1. **在 [設定**] 窗格中，選取 [**關閉**] 以停用 [**啟用清除**]。
1. 選取 \[儲存\]。

    ![啟用清除功能](media/data-purge-portal/enable-purge-off.png)

## <a name="limitations"></a>限制

* 清除程式是最後且無法復原的。 您無法「復原」此進程或復原已清除的資料。 因此， [復原資料表](kusto/management/undo-drop-table-command.md) 卸載之類的命令無法復原已清除的資料，而將資料回復為先前的版本，將無法在最新的清除之前移至「之前」。
* 此 `.purge` 命令會針對資料管理端點執行： * https://ingest- [>yourclustername]. [Region]. kusto*。 此命令需要相關資料庫的 [資料庫系統管理員](kusto/management/access-control/role-based-authorization.md) 許可權。 
* 由於清除程式的效能影響，呼叫端應該修改資料結構描述，讓最少量的資料表包含相關的資料，以及每個資料表的批次命令，以減少清除程式的大量 COGS 影響。
* `predicate`清除命令的參數是用來指定要清除的記錄。 `Predicate` 大小限制為 63 KB。 

## <a name="next-steps"></a>後續步驟

* [Azure 資料總管中的資料清除](kusto/concepts/data-purge.md)
