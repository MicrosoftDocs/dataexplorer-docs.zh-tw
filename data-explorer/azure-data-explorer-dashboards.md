---
title: 使用 Azure 資料總管儀表板將資料視覺化
description: 瞭解如何使用 Azure 資料總管儀表板將資料視覺化
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: how-to
ms.date: 05/26/2020
ms.openlocfilehash: 114e3b00b35ff6dd30241cd6afc4666fdedf6f62
ms.sourcegitcommit: 455d902bad0aae3e3d72269798c754f51442270e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93349489"
---
# <a name="visualize-data-with-azure-data-explorer-dashboardspreview"></a>使用 Azure 資料總管儀表板將資料視覺化 (預覽) 

Azure 資料總管是一項快速又可高度調整的資料探索服務，可用於處理記錄和遙測資料。 Azure 資料總管提供 web 應用程式，可讓您執行查詢和建立儀表板。 儀表板可在獨立 web 應用程式（ [WEB UI](web-query-data.md)）中使用。 Azure 資料總管也與其他儀表板服務（例如 [Power BI](power-bi-connector.md) 和 [Grafana](grafana.md)）整合。

Azure 資料總管儀表板提供三個主要優點：

* 以原生方式將查詢從 Web UI 匯出至 Azure 資料總管儀表板。 
* 探索 Web UI 中的資料。
* 優化的儀表板轉譯效能。

下圖描述 Azure 資料總管儀表板。

:::image type="content" source="media/adx-dashboards/dash.png" alt-text="最終的儀表板":::

> [!IMPORTANT]
> 您的資料是安全的。 與使用者相關的儀表板和儀表板相關中繼資料會在待用時加密。

## <a name="create-a-dashboard"></a>建立儀表板

1. 在巡覽列中，選取 [ **儀表板** ]，然後選取 [ **新增儀表板** ]。

    :::image type="content" source="media/adx-dashboards/new-dashboard.png" alt-text="新增儀表板":::

1. 選取儀表板名稱並 **建立** 。

    :::image type="content" source="media/adx-dashboards/new-dashboard-popup.png" alt-text="建立儀表板":::

## <a name="add-data-source"></a>新增資料來源

新增儀表板所需的資料來源。

1. 選取頂端列上的 [ **資料來源** ] 功能表項目。 在 [ **資料來源** ] 窗格中，選取 [ **+ 新增資料來源** ] 按鈕。

    :::image type="content" source="media/adx-dashboards/data-source.png" alt-text="資料來源":::

1. 在 [ **建立新的資料來源** ] 窗格中：
    1. 輸入叢集 **URI** 或部分名稱（包括區域），然後選取 **[連接]** 。 
    1. 從下拉式清單中選取 **資料庫** 。
    1. 如有需要，請使用預設值或修改 **資料來源名稱** 。 
    1. 選取 [套用]  。

    :::image type="content" source="media/adx-dashboards/data-source-pane.png" alt-text="資料來源窗格":::

## <a name="use-parameters"></a>使用參數

使用儀表板篩選器來啟用參數。 參數可大幅改善儀表板轉譯效能，並可讓您在查詢中儘早使用篩選值。 如需使用參數的詳細資訊，請參閱 [在 Azure 中使用參數資料總管儀表板](dashboard-parameters.md)。

1. 選取頂端列上的 **參數** 。 在 [ **參數** ] 窗格中，選取 [ **+ 新增參數** ] 按鈕。

    :::image type="content" source="media/adx-dashboards/parameters.png" alt-text="選取新參數":::

1. 輸入所有必要欄位的值，然後選取 [ **完成** ]。

    :::image type="content" source="media/adx-dashboards/parameter-pane.png" alt-text="參數窗格":::

|欄位  |描述 |
|---------|---------|
|**參數顯示名稱**    |   儀表板或編輯卡片上顯示的參數名稱。      |
|**參數類型**    |下列其中之一：<ul><li>**單一選取** ：可在篩選中選取一個值，作為參數的輸入。</li><li>**多重選取** ：可以在篩選中選取一個或多個值，作為參數的輸入 (s) 。</li><li>**時間範圍** ：允許建立額外的參數，以根據時間篩選查詢和儀表板。依預設，每個儀表板都有時間範圍選擇器。</li></ul>    |
|**變數名稱**     |   要在查詢中使用的參數名稱。      |
|**Data type**    |    參數值的資料類型。     |
|**釘選為儀表板篩選**   |   將參數型篩選釘選到儀表板或從儀表板取消釘選。       |
|**Source**     |    參數值的來源： <ul><li>**固定值** ：手動引進靜態篩選值。 </li><li>**查詢** ：使用 KQL 查詢動態引進值。  </li></ul>    |
|**新增「全選」值**    |   僅適用于單一選取專案和多重選取參數類型。 用來取得所有參數值的資料。      |

## <a name="add-query"></a>新增查詢

[ **加入查詢** ] 會使用 Kusto 查詢語言程式碼片段來取出資料和轉譯視覺效果。 每個查詢都可以支援單一視覺效果。

1. 從儀表板畫布或頂端功能表列選取 [ **加入查詢** ]。

    :::image type="content" source="media/adx-dashboards/empty-dashboard-new-query.png" alt-text="新查詢":::

1. 在 [ **查詢** ] 窗格中， 
    1. 從下拉式清單中選取資料來源
    1. 輸入查詢，然後選取 [ **執行** ] 
    1. 選取 [ **+ 新增視覺效果** ]

    :::image type="content" source="media/adx-dashboards/initial-query.png" alt-text="執行查詢":::

1. 在 [ **視覺效果格式化** ] 窗格中，選取 [ **圖表類型** ] 以選擇視覺效果的類型。 
1. 命名視覺效果，然後選取 [套用 **變更** ]，將視覺效果釘選到儀表板。

    :::image type="content" source="media/adx-dashboards/add-visual.png" alt-text="將視覺效果新增至查詢":::

1. 您可以調整視覺效果的大小並 **儲存變更** ，以儲存儀表板。

    :::image type="content" source="media/adx-dashboards/save-dashboard.png" alt-text="儲存儀表板":::

## <a name="share-dashboards"></a>共用儀表板

您可以使用 [共用] 功能表將 [許可權授](#grant-permissions) 與儀表板、 [變更使用者的許可權等級](#change-a-user-permission-level)，以及 [共用儀表板連結](#share-the-dashboard-link)。

> [!IMPORTANT]
> 若要存取儀表板，儀表板檢視器需要下列各項：
> * 存取的儀表板連結
> * 儀表板許可權
> * 存取 Azure 資料總管叢集中的基礎資料庫  

1. 在儀表板的頂端列中，選取 [ **共用** ] 功能表項目。
1. 從下拉式清單中選取 [ **管理許可權** ]。 

    :::image type="content" source="media/adx-dashboards/share-dashboard.png" alt-text="共用儀表板下拉式清單":::

### <a name="grant-permissions"></a>授與權限

若要在 [ **儀表板許可權** ] 窗格中將許可權授與使用者：
1. 在 [ **加入新成員** ] 方塊中，寫入使用者的名稱或電子郵件。
1. 選取 [ **可查看** ] 或 [ **可以編輯** ] 的 **許可權** 等級，然後按一下 [ **新增** ]。

:::image type="content" source="media/adx-dashboards/dashboard-permissions.png" alt-text="管理儀表板許可權":::

### <a name="change-a-user-permission-level"></a>變更使用者權限等級

若要在 [ **儀表板許可權** ] 窗格中變更使用者權限層級：
1. 使用搜尋方塊或滾動使用者清單來尋找使用者。
1. 視需要變更 **許可權** 層級。

### <a name="share-the-dashboard-link"></a>共用儀表板連結

共用儀表板連結：
* 選取 [ **共用** ] 下拉式清單，然後選取 [ **複製連結** ] 或
* 在 [ **儀表板許可權** ] 視窗中，選取 [ **複製連結** ]。 

## <a name="enable-auto-refresh"></a>啟用自動重新整理 

1. 選取 [儀表板] 功能表中的 [ **編輯** ]，以切換至編輯模式。
1. 選取 [ **自動** 重新整理]。 
 
    :::image type="content" source="media/adx-dashboards/auto-refresh.png" alt-text="選取 [自動重新整理]":::

1. 切換選項，以 **啟用** 自動重新整理。 
1. 選取 [ **最小時間間隔** ] 和 [預設重新整理 **頻率** ] 的值。 

    :::image type="content" source="media/adx-dashboards/auto-refresh-toggle.png" alt-text="啟用自動重新整理":::

1. 選取 **[** 套用並 **儲存** 儀表板]。

> [!NOTE]
> * 選取最小的最小時間間隔，以減少叢集中不必要的負載。 
> * 儀表板檢視器： 
>    * 只能變更個人使用的最小時間間隔。 
>    * 無法選取小於編輯器所指定之 **最小時間間隔** 的值。

## <a name="next-steps"></a>後續步驟

* [在 Azure 資料總管儀表板中使用參數](dashboard-parameters.md)
* [自訂儀表板視覺效果](dashboard-customize-visuals.md)
* [在 Azure 資料總管中查詢資料](web-query-data.md)
