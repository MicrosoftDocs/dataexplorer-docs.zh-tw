---
title: 使用重劃線視覺化 Azure 資料資源管理員
description: 在本文中,您將瞭解如何使用 Redash 本機連接器在 Azure 資料資源管理器中可視化數據。
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 8237eb917f5123755898d281151ce34a08eb90db
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81495454"
---
# <a name="visualize-data-from-azure-data-explorer-in-redash"></a>在 Redash 中視覺化來自 Azure 資料資源管理員的資料

[Redash](https://redash.io/)連接和查詢數據源,構建儀錶板以可視化數據並與對等方共享數據。 在本文中,您將瞭解如何將 Azure 資料資源管理員設置為 Redash 的數據源,然後可視化數據。

## <a name="prerequisites"></a>Prerequisites

1. [建立叢集與資料庫](create-cluster-database-portal.md)。
1. 在[將範例資料引入 Azure 資料資源管理員中](ingest-sample-data.md)所述的引入數據。 有關更多引入選項,請參閱[攝入概述](ingest-data-overview.md)。

[!INCLUDE [data-explorer-configure-data-source](includes/data-explorer-configure-data-source.md)]

## <a name="create-azure-data-explorer-connector-in-redash"></a>在 Redash 中建立 Azure 資料資源管理員連接器 

1. 登錄到[Redash。](https://www.redash.io/) 選擇 **「開始」** 以建立帳戶。
1. 在 **"讓我們開始**"下,選擇"**連接數據源**"。

    ![連線資料來源](media/redash/connect-data-source.png)

1. 在 **"建立新資料來源'** 視窗中,選擇**Azure 資料資源管理員(庫托),** 然後選擇 **"創建**" 。 

    ![選擇 Azure 資料資源管理員資料來源](media/redash/select-adx-data-source.png)

1. 在**Azure 資料資源管理員(Kusto)** 視窗中,完成以下窗體並選擇 **"創建**" 。

    ![Azure 資料資源管理員 (函式庫托)設定視窗](media/redash/adx-settings-window.png)

1. 在 **「設定」** 視窗中,選擇 **「儲存**」和「**測試連接**」以測試**Azure 資料資源管理員 (Kusto)** 資料來源連接。

## <a name="create-queries-in-redash"></a>在 Redash 中建立查詢

1. 在 Redash 的左上角,選擇 **「創建** > **查詢**」。 按下 **「新建查詢**」 並重新命名查詢。

    ![建立查詢](media/redash/create-query.png)

1. 在頂部編輯窗格中鍵入查詢,然後選擇 **「保存****和執行**」。 選擇 **「發佈**」以發佈查詢以供將來使用。

    ![儲存及執行查詢](media/redash/save-and-execute-query.png)

    在左邊窗格中,您可以在下拉選單中看到資料源連接名稱(流中的**Github 連接器**)和所選資料庫中的表。 

1. 在底部中心窗格中查看查詢結果。 通過選擇 **「新建可視化」** 按鈕,創建與查詢配合使用的可視化效果。

    ![新增視覺化](media/redash/new-visualization.png)

1. 在視覺化螢幕中,選擇**視覺化類型**與相關欄位,例如 X**欄**位, 例如 X 欄位與**Y 欄**位 。 **保存**可視化效果。

    ![設定並儲存視覺化效果](media/redash/configure-visualization.png)

### <a name="create-a-query-using-a-parameter"></a>使用參數建立查詢

1. **建立** > **查詢**以建立新查詢。 使用 [{}] 捲曲括弧向其添加參數。 選擇**{}[ ]** 以開啟 **「新增參數」** 視窗。 您還可以選擇*設置圖示*來修改現有參數的屬性,並打開 **<parameter_name>** 視窗。 

    ![插入參數](media/redash/insert-parameter.png)

1. 命名參數。 選擇**型態**:**從下拉選單中選擇根據查詢的下拉清單**。 選取 [確定]****

    ![查詢查詢的下拉清單](media/redash/query-based-dropdown-list.png)

    > [!NOTE]
    > 查詢使用多個值,因此必須包含以下語法`| where Type in ((split('{{Type}}', ',')))`。 有關詳細資訊,請參閱[運算子](kusto/query/inoperator.md)。 這會導致[重劃線應用中有多個查詢參數選項](https://redash.io/help/user-guide/querying/query-parameters#Serialized-Multi-Select-Query-Parametersredash.io)

## <a name="create-a-dashboard-in-redash"></a>在 Redash 中建立儀表板

1. 要建立儀表板,**請建立** > **儀表板**。 或者,選擇現有儀錶板,**儀錶板**>从列表中选择仪表板。

    ![建立儀表板](media/redash/create-dashboard.png)

1. 在 **「新建儀錶板」** 視窗中,命名儀錶板並選擇「**儲存**」 。 在 **<Dashboard_name>** 視窗中,選擇 **「添加小部件**」以建立新的小部件。 

1. 在 **'新增小元件'** 視窗中, 選擇查詢名稱、**選擇視覺化**與**參數**。 選擇 **"添加到儀錶板"**

   ![選擇視覺化效果並加入到儀表板](media/redash/add-widget-window.png)

1. 選擇 **「完成編輯**」以完成儀錶板創建。

1.  在儀表板編輯模式下,選擇 **「使用儀表板級別篩選器**」以使用以前定義的 **「類型」** 參數。

    ![完成儀表板建立](media/redash/complete-dashboard.png)

## <a name="next-steps"></a>後續步驟

* [為 Azure 資料資源管理員編寫查詢](write-queries.md)


