---
title: 快速入門：在 Azure 資料總管 Web UI 中查詢資料
description: 在本快速入門中，您將了解如何在 Azure 資料總管 Web UI 中查詢和共用資料。
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 06/15/2020
ms.localizationpriority: high
ms.openlocfilehash: 479bd512f759a20123e5eb94fcc9ec54e2a13455
ms.sourcegitcommit: 4e811d2f50d41c6e220b4ab1009bb81be08e7d84
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95513296"
---
# <a name="quickstart-query-data-in-azure-data-explorer-web-ui"></a>快速入門：在 Azure 資料總管 Web UI 中查詢資料

Azure 資料總管是一項快速又可高度調整的資料探索服務，可用於處理記錄和遙測資料。 Azure 資料總管提供一個讓您執行和共用查詢的 Web 應用程式。 它可在 Azure 入口網站中取得，也以獨立的 Web 應用程式的形式提供。 在本文中，您將在獨立的版本中進行作業，它能讓您連接到多個叢集以及共用深層連結到查詢。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

除了 Azure 訂用帳戶之外，您還需要[一個測試叢集和資料庫](create-cluster-database-portal.md)，才能完成此快速入門。

## <a name="sign-in-to-the-application"></a>登入應用程式

登入[應用程式](https://dataexplorer.azure.com/)。

## <a name="add-clusters"></a>新增叢集

當您第一次開啟應用程式時，沒有任何連線。

![新增叢集](media/web-query-data/add-cluster.png)

您必須將連線新增到至少一個叢集，才能開始執行查詢。 在此節中，您將連線新增至 Azure 資料總管中我們已設置要輔助學習的 *說明叢集*，也要將連線新增到在先前快速入門中建立的測試叢集。

1. 在應用程式的左上方中，選取 [新增叢集]。

1. 在 [新增叢集] 對話方塊中，輸入 URI，然後選取 [新增]。

   您可以使用說明叢集 URI，`https://help.kusto.windows.net`。 如果您有自己的叢集，請提供您叢集的 URI。 例如，如下圖所示的 `https://mydataexplorercluster.westus.kusto.windows.net`：

    ![入口網站中的伺服器 URI](media/web-query-data/server-uri.png)

1. 在左窗格中，您現在應該會看到 [說明] 叢集。 展開 [範例] 資料庫，以便看到您可以存取的範例資料表。

    ![範例資料庫](media/web-query-data/sample-databases.png)

    我們在此快速入門與 Azure 資料總管文章中稍後會使用 **StormEvents** 資料表。

現在加入之前建立的測試叢集。

1. 選取 [新增叢集]。

1. 在 [新增叢集] 對話方塊中，以 `https://<ClusterName>.<Region>.kusto.windows.net/` 格式輸入您的測試叢集 URL，然後選取 [新增]。

    在下面的範例中，您會看到 [說明] 叢集和一個新的叢集 **docscluster.westus** (完整的 URL 是 `https://docscluster.westus.kusto.windows.net/`)。

    ![測試叢集](media/web-query-data/test-cluster.png)

## <a name="run-queries"></a>執行查詢

您現在可以對連線的叢集執行查詢 (假設測試叢集中已經有資料)。 我們將著重 [i說明] 叢集。

1. 在左窗格中，在 [說明]叢集下面選取 [範例] 資料庫。

1. 複製下列查詢並貼到查詢視窗中。 在視窗頂端，選取 [執行] 。

    ```Kusto
    StormEvents
    | sort by StartTime desc
    | take 10
    ```
    此查詢會傳回 **StormEvents** 資料表中最新的十筆記錄。 結果的左邊結果應該會類似於下面的表格。

    :::image type="content" source="media/web-query-data/result-set-01.png" alt-text="資料表的螢幕擷取畫面，其中列出十個衝擊事件的開始時間、結束時間、劇集、事件識別碼、狀態和事件種類。" border="false":::

    下圖顯示應用程式現在的狀態，包含已新增的叢集以及包含結果的查詢。

    ![完整應用程式](media/web-query-data/full-application.png)

1. 在第一個查詢下面，複製下列查詢並貼到查詢視窗中。 請注意，它有好幾行且尚未格式化，不像第一個查詢。

    ```Kusto
    StormEvents | sort by StartTime desc | project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative | take 10
    ```

1. 按一下視窗中的新查詢以選取該查詢。 按 Shift + Alt + F 來格式化查詢，讓它看起來如下所示。

    ![經過格式化的查詢](media/web-query-data/formatted-query.png)

1. 按 Shift + Enter，也就是執行查詢的快速鍵。

   此查詢會傳回與第一個查詢相同的記錄，但是只包含 `project` 陳述式中指定的資料行。 結果應該會類似於下列表格。

    :::image type="content" source="media/web-query-data/result-set-02.png" alt-text="資料表的螢幕擷取畫面，其中列出十個衝擊事件的開始時間、結束時間、事件類型、損毀屬性和劇集敘述。" border="false":::

1. 在查詢視窗頂端，選取 [召回] 。

    查詢視窗現在會顯示第一個查詢的結果，無需重新執行查詢。 通常在分析期間，您可以執行多個查詢，而 [召回] 可讓您重新瀏覽上一個查詢的結果。

1. 讓我們再執行一個查詢來查看不同的輸出類型。

    ```Kusto
    StormEvents
    | summarize event_count=count(), mid = avg(BeginLat) by State
    | sort by mid
    | where event_count > 1800
    | project State, event_count
    | render columnchart
    ```
    結果應會類似於下列圖表。

    ![直條圖](media/web-query-data/column-chart.png)

> [!NOTE]
> 查詢運算式中的空白行可能會影響執行查詢的哪個部分。
>
> 如果未選取任何文字，則會假設查詢或命令以空白行分隔。
> 如果選取文字，則會執行選取的文字。

## <a name="work-with-the-table-grid"></a>使用資料表格線

現在您已了解基本查詢的運作方式，讓我們看看如何使用資料表格線來自訂結果和執行進一步分析。

1. 重新執行第一個查詢。 讓滑鼠停留在 **State** 資料行上，選取功能表，然後選取 **Group by State**。

    ![依狀態分組](media/web-query-data/group-by.png)

1. 在方格中，展開 **California** 以查看該狀態的記錄。

    :::image type="content" source="media/web-query-data/result-set-03.png" alt-text="查詢結果格線的螢幕擷取畫面。加州群組已展開，並顯示三個資料列，其中包含來自加州事件的資料。" border="false":::

    執行探勘分析時，這種分組類型很有幫助。

1. 將滑鼠停留在 **Group** 資料行上，然後選取 **Reset columns**。

    ![重設資料行](media/web-query-data/reset-columns.png)

    這會讓格線回到其原始狀態。

1. 執行下列查詢。

    ```Kusto
    StormEvents
    | sort by StartTime desc
    | where DamageProperty > 5000
    | project StartTime, State, EventType, DamageProperty, Source
    | take 10
    ```

1. 在方格的右側，選取 **Columns** 以查看工具面板。

    ![工具面板](media/web-query-data/tool-panel.png)

    此面板的功能類似於 Excel 中的樞紐資料表的欄位清單，能讓您在格線本身執行更多分析。

1. 選取 [樞紐分析表模式]，然後拖曳資料行，如下所示：[狀態] 到 [資料列群組]；[DamageProperty] 到 [值]；[EventType] 到 [資料行標籤]。  

    ![樞紐分析表模式](media/web-query-data/pivot-mode.png)

    結果應該會類似於下列樞紐分析表。

    ![樞紐分析表](media/web-query-data/pivot-table.png)

    請注意 Vermont 和 Alabama 在相同的類別中各有兩個事件，而 Texas 在不同的類別下則有兩個事件。 樞紐分析表可讓您快速找出這類事情，它們是進行快速分析的絕佳工具。

## <a name="share-queries"></a>共用查詢

許多時候，您會想要共用所建立的查詢。 

1. 在查詢視窗中，選取您所複製的第一個查詢。

1. 在查詢視窗頂端，選取 [共用] 。 

:::image type="content" source="media/web-query-data/share-menu.png" alt-text="共用功能表":::

下拉式清單中有下列選項可用：
* 連結至剪貼簿
* [連結至剪貼簿或對其進行查詢](#provide-a-deep-link)
* 連結至剪貼簿或對其進行查詢，以及將結果送至剪貼簿
* [釘選到儀表板](#pin-to-dashboard)
* [查詢 Power BI](power-bi-imported-query.md)

### <a name="provide-a-deep-link"></a>提供深層連結

您可以提供深層連結，讓其他能夠存取叢集的使用者也能執行查詢。

1. 在 **共用** 中，選取 [連結至剪貼簿或對其進行查詢]。

1. 複製連結和查詢到文字檔。

1. 將連結貼到新的瀏覽器視窗。 查詢執行之後，結果應該會看起來如下所示。

    :::image type="content" source="media/web-query-data/shared-query.png" alt-text="共用查詢深層連結":::

### <a name="pin-to-dashboard"></a>釘選到儀表板

當您使用 Web UI 中的查詢完成資料探索並尋找所需的資料時，您可以將其釘選到儀表板以進行持續監視。 

若要釘選查詢：

1. 在 **共用** 中，選取 [釘選到儀表板]。

1. 在 [釘選到儀表板] 窗格中：
    1. 提供 **查詢名稱**。
    1. 選取 [使用現有項目] 或 [新建]。
    1. 提供 **儀表板名稱**
    1. 選取 [建立後檢視儀表板] 核取方塊 (如果是新的儀表板)。
    1. 選取 [釘選]

    :::image type="content" source="media/web-query-data/pin-to-dashboard.png" alt-text="釘選到儀表板窗格":::
    
> [!NOTE]
> **釘選到儀表板** 只會釘選選取的查詢。 若要建立儀表板資料來源，並將轉譯命令翻譯成儀表板中的視覺效果，您必須在資料庫清單中選取相關的資料庫。

## <a name="export-query-results"></a>匯出查詢結果

若要將查詢結果匯出至 CSV 檔案，請選取 [檔案] > [匯出至 CSV]。

:::image type="content" source="media/web-query-data/export-results.png" alt-text="將結果匯出至 CSV 檔案":::

## <a name="provide-feedback"></a>提供意見反應

1. 在應用程式右上角，選取意見反應圖示 ![意見反應圖示](media/web-query-data/icon-feedback.png).

1. 輸入您的意見反應，然後選取 [提交]。

## <a name="clean-up-resources"></a>清除資源

您並未在此快速入門中建立任何資源，但若您要從應用程式移除一或兩個叢集，請以滑鼠右鍵按一下叢集，然後選取 [移除連線]。

## <a name="next-steps"></a>後續步驟

[撰寫 Azure 資料總管的查詢](write-queries.md)
