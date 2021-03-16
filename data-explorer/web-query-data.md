---
title: 快速入門：在 Azure 資料總管 Web UI 中查詢資料
description: 在本快速入門中，您將了解如何在 Azure 資料總管 Web UI 中查詢和共用資料。
author: orspod
ms.author: orspodek
ms.reviewer: olgolden
ms.service: data-explorer
ms.topic: quickstart
ms.date: 02/09/2021
ms.localizationpriority: high
ms.openlocfilehash: d581ade4a9cbba083e8cebf39a30f01586d2635c
ms.sourcegitcommit: db99b9d0b5f34341ad3be38cc855c9b80b3c0b0e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2021
ms.locfileid: "103565697"
---
# <a name="quickstart-query-data-in-azure-data-explorer-web-ui"></a>快速入門：在 Azure 資料總管 Web UI 中查詢資料

Azure 資料總管是快速、完全受控的資料分析服務，可即時分析大量資料。 Azure 資料總管會提供 Web 體驗，讓您可以連線到 Azure 資料總管叢集，以及撰寫、執行和共用 Kusto 查詢語言命令和查詢。 在 Azure 入口網站和獨立 Web 應用程式形式的 [Azure 資料總管 Web UI](https://dataexplorer.azure.com) 中都可以獲得 Web 體驗。 Azure 資料總管 Web UI 也可以由 HTML iframe 中的其他 Web 入口網站來裝載。 如需如何裝載 Web UI 和所使用 Monaco 編輯器的詳細資訊，請參閱 [Monaco IDE 整合](kusto/api/monaco/monaco-kusto.md)。
在本快速入門中，您將會在獨立的 Azure 資料總管 Web UI 中工作。

:::image type="content" source="media/web-query-data/walkthrough.gif" alt-text="Kusto Web Explorer 體驗的逐步解說":::

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。
* 具有資料的叢集和資料庫。 [建立您自己的叢集](create-cluster-database-portal.md)或使用 Azure 資料總管說明叢集。

## <a name="sign-in-to-the-application"></a>登入應用程式

登入[應用程式](https://dataexplorer.azure.com/)。

## <a name="add-clusters"></a>新增叢集

當您第一次開啟應用程式時，其中並沒有任何叢集連線。

![新增叢集](media/web-query-data/add-cluster.png)

您必須先新增叢集連線，才能開始執行查詢。 在本節中，您會新增 Azure 資料總管 **說明** 叢集的連線，以及您在 [必要條件](#prerequisites)中所建立測試叢集的連線 (選擇性)。

### <a name="add-help-cluster"></a>新增說明叢集

1. 在應用程式的左上方中，選取 [新增叢集]。

1. 在 [新增叢集] 對話方塊中，輸入 URI https://help.kusto.windows.net ，然後選取 [新增]。
   
1. 在左窗格中，您現在應該會看到 [說明] 叢集。 展開 [範例] 資料庫，然後開啟 [資料表] 資料夾，以查看您可以存取的範例資料表。

    :::image type="content" source="media/web-query-data/help-cluster.png" alt-text="在說明叢集中尋找資料表":::

我們在此快速入門與 Azure 資料總管文章中稍後會使用 **StormEvents** 資料表。 

### <a name="add-your-cluster"></a>新增您的叢集

現在加入之前建立的測試叢集。

1. 選取 [新增叢集]。

1. 在 [新增叢集] 對話方塊中，以 `https://<ClusterName>.<Region>.kusto.windows.net/` 格式輸入您的測試叢集 URL，然後選取 [新增]。 例如，如下圖所示的 `https://mydataexplorercluster.westus.kusto.windows.net`：

    :::image type="content" source="media/web-query-data/server-uri.png" alt-text="輸入測試叢集 URL":::
    
1. 在下面的範例中，您會看到 [說明] 叢集和一個新的叢集 **docscluster.westus** (完整的 URL 是 `https://docscluster.westus.kusto.windows.net/`)。

    ![測試叢集](media/web-query-data/test-cluster.png)

## <a name="run-queries"></a>執行查詢

您現在可以在這兩個叢集上執行查詢 (假設您的測試叢集中有資料)。 **在本文** 中，我們會將焦點放在說明叢集上。

1. 在左窗格中，在 [說明]叢集下面選取 [範例] 資料庫。

1. 複製下列查詢並貼到查詢視窗中。 在視窗頂端，選取 [執行] 。

    ```kusto
    StormEvents
    | sort by StartTime desc
    | take 10
    ```
    
    此查詢會傳回 **StormEvents** 資料表中最新的 10 筆記錄。 結果應該會類似於下列表格。

    :::image type="content" source="media/web-query-data/result-set-take-10.png" alt-text="資料表的螢幕擷取畫面，其中列出 10 個風暴事件的資料。" border="false":::

    下圖顯示應用程式的狀態、新增的叢集，以及具有結果的查詢。

    :::image type="content" source="media/web-query-data/webui-take10.png" alt-text="全螢幕":::

1. 在第一個查詢下面，複製下列查詢並貼到查詢視窗中。 請注意，其有好幾行且尚未格式化，不像第一個查詢。

    ```kusto
    StormEvents | sort by StartTime desc 
    | project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative | take 10
    ```

1. 選取新的查詢。 按 *Shift+Alt+F* 來格式化查詢，讓其看起來像下列查詢。

    ![經過格式化的查詢](media/web-query-data/formatted-query.png)

1. 選取 [執行] 或按 *Shift+Enter* 以執行查詢。 此查詢會傳回與第一個查詢相同的記錄，但是只包含 `project` 陳述式中指定的資料行。 結果應該會類似於下列表格。

    :::image type="content" source="media/web-query-data/result-set-project.png" alt-text="資料表的螢幕擷取畫面，其中列出 10 個風暴事件的開始時間、結束時間、州別、事件類型、損毀的財產和事件敘述。" border="false":::

    > [!TIP]
    > 選取查詢視窗頂端的 [重新叫用] 以顯示第一個查詢的結果集，而不需要重新執行查詢。 通常在分析期間，您可以執行多個查詢，而 [重新叫用] 可讓您擷取上一個查詢的結果。

1. 讓我們再執行一個查詢來查看不同的輸出類型。

    ```kusto
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
    > * 如果未選取任何文字，則會假設查詢或命令以空白行分隔。
    > * 如果選取文字，則會執行選取的文字。

## <a name="work-with-the-table-grid"></a>使用資料表格線

現在您已瞭解基本查詢的運作方式，您可以使用資料表方格來自訂結果，並進行進一步的分析。 

### <a name="expand-a-cell"></a>展開資料格

展開資料格有助於查看長字串或動態欄位（例如 JSON）。 

1. 按兩下資料格以開啟展開的視圖。 此視圖可讓您讀取長字串，並提供動態資料的 JSON 格式。

    :::image type="content" source="media/web-query-data/expand-cell.png" alt-text="Azure 資料瀏覽器 WebUI 展開資料格以顯示長字串":::

1. 按一下結果方格右上方的圖示，以切換 [讀取窗格] 模式。 選擇下列展開視圖的讀取窗格模式：內嵌、下方窗格和右窗格。

    :::image type="content" source="media/web-query-data/expanded-view-icon.png" alt-text="變更展開視圖模式之讀取窗格的圖示-Azure 資料瀏覽器 WebUI 查詢結果":::

### <a name="expand-a-row"></a>展開資料列

使用具有數十個數據行的資料表時，請展開整個資料列，以便能夠輕鬆地查看不同資料行及其內容的總覽。 

1. 按一下 **>** 您要展開之資料列左邊的箭號。

    :::image type="content" source="media/web-query-data/expand-row.png" alt-text="在 Azure 資料瀏覽器 WebUI 中展開資料列":::

1. 在展開的資料列中，某些資料行會展開 (箭號) ，而某些資料行則會折迭 (箭號指向右邊) 。 按一下這些箭號可在這兩種模式之間切換。

### <a name="group-column-by-results"></a>依結果群組資料行

在結果中，您可以依任何資料行將結果分組。

1. 執行下列查詢：
     
    ```kusto
    StormEvents
    | sort by StartTime desc
    | take 10
    ```

1. 讓滑鼠停留在 **State** 資料行上，選取功能表，然後選取 **Group by State**。

    ![依狀態分組](media/web-query-data/group-by.png)

1. 在格線中，按兩下 [加州] 以展開並查看該州的記錄。 執行探勘分析時，這種分組類型很有幫助。

    :::image type="content" source="media/web-query-data/group-expanded.png" alt-text="螢幕擷取畫面：已展開加州群組的查詢結果格線" border="false":::

1. 將滑鼠停留在 **Group** 資料行上，然後選取 **Reset columns**。 此設定會讓格線回到其原始狀態。

    ![重設資料行](media/web-query-data/reset-columns.png)

#### <a name="use-value-aggregation"></a>使用值匯總

依資料行分組之後，您就可以使用 value 彙總函式來計算每個群組的簡單統計資料。

1. 選取您要評估之資料行的功能表。
1. 選取 [ **值匯總**]，然後選取您要在此資料行上進行的函數類型。

    :::image type="content" source="media/web-query-data/aggregate.png" alt-text="依結果群組資料行時匯總結果。 ":::

### <a name="filter-columns"></a>篩選資料行

您可以使用一或多個運算子來篩選資料行的結果。

1. 若要篩選特定資料行，請選取該資料行的功能表。
1. 選取篩選圖示。
1. 在 [篩選產生器] 中，選取所需的運算子。
1. 輸入您想要篩選資料行的運算式。 當您輸入時，會篩選結果。
    
    > [!NOTE] 
    > 篩選不區分大小寫。

1. 若要建立多重條件篩選準則，請選取布林運算子來新增另一個條件
1. 若要移除篩選準則，請從您的第一個篩選準則中刪除文字。

    :::image type="content" source="media/web-query-data/filter-column.gif" alt-text="GIF 顯示如何在 Azure 資料瀏覽器 WebUI 中篩選資料行":::

### <a name="run-cell-statistics"></a>執行資料格統計資料

1. 執行下列查詢。

    ```kusto
    StormEvents
    | sort by StartTime desc
    | where DamageProperty > 5000
    | project StartTime, State, EventType, DamageProperty, Source
    | take 10
    ```

1. 在結果格線中，選取幾個數值資料格。 資料表格線可讓您選取多個資料列、資料行和資料格，並計算其彙總。 Web UI 目前針對數值支援下列函式：**Average**、**Count**、**Min**、**Max** 和 **Sum**。

    :::image type="content" source="media/web-query-data/select-stats.png" alt-text="選取函式"::: 

### <a name="filter-to-query-from-grid"></a>從方格篩選至查詢

篩選方格的另一種簡單方式，就是直接從方格將篩選運算子加入至查詢。

1. 選取具有您想要為其建立查詢篩選之內容的資料格。
1. 以滑鼠右鍵按一下以開啟 [資料格動作] 功能表。 選取 [ **將選取專案加入為篩選**]。
    
    :::image type="content" source="media/web-query-data/add-selection-filter.png" alt-text="在 Azure 資料瀏覽器 WebUI 中將選取專案新增為篩選，以從方格結果查詢":::

1. 查詢子句將會新增至查詢編輯器中的查詢：

    :::image type="content" source="media/web-query-data/add-query-from-filter.png" alt-text="從 Azure 資料瀏覽器 WebUI 中的方格篩選新增查詢子句":::

### <a name="pivot"></a>樞紐

樞紐分析表模式功能與 Excel 的樞紐分析表類似，可讓您在方格本身進行先進的分析。

進行切換可讓您取得資料行值，並將其轉換成資料行。 例如，您可以根據 *狀態* 來將資料行設為佛羅里達、Missouri、>alabama 等等。

1. 在格線的右側，選取 [資料行] 以查看資料表工具面板。

    ![資料表工具面板](media/web-query-data/tool-panel.png)

1. 選取 [ **樞紐分析表模式]**，然後將資料行拖曳 **如下： [** 資料列 **群組**]。 **DamageProperty** 至 **值**;和 **狀態** 為數據 **行標籤**。  

    ![樞紐分析表模式](media/web-query-data/pivot-mode.png)

    結果看起來應該會像下列的樞紐分析表：

    ![樞紐分析表](media/web-query-data/pivot-table.png)

## <a name="search-in-the-results-table"></a>在結果資料表中搜尋

您可以在結果資料表中尋找特定運算式。

1. 執行下列查詢：

    ```Kusto
    StormEvents
    | where DamageProperty > 5000
    | take 1000
    ```

1. 按一下右側的 [搜尋] 按鈕，然後輸入 *"Wabash"*

    :::image type="content" source="media/web-query-data/search.png" alt-text="在資料表中搜尋":::

1. 所有提及的搜尋運算式現在都會在資料表中醒目提示。 若要在這些運算式之間進行瀏覽，您可以按一下 *Enter* 往下繼續，也可以按 *Shift+Enter* 來回頭，或者，您也可以使用搜尋方塊旁邊的 *向上鍵* 和 *向下鍵* 來進行。

    :::image type="content" source="media/web-query-data/search-results.png" alt-text="瀏覽搜尋結果":::

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

1. 將連結貼到新的瀏覽器視窗。 結果看起來應該如下所示

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

## <a name="settings"></a>設定

在 [設定] 索引標籤中，您可以：

* [匯出環境設定](#export-environment-settings)
* [匯入環境設定](#import-environment-settings)
* [反白顯示錯誤層級](#highlight-error-levels)
* [清除本機狀態](#clean-up-resources)

選取右上方的 [設定] 圖示 :::image type="icon" source="media/web-query-data/settings-icon.png" border="false":::，以開啟 [設定] 視窗。

:::image type="content" source="media/web-query-data/settings.png" alt-text="[設定] 視窗":::

### <a name="export-and-import-environment-settings"></a>匯出和匯入環境設定

匯出和匯入動作可協助您保護工作環境，並將其重新放置到其他瀏覽器和裝置。 匯出動作會將您的所有設定、叢集連線和查詢索引標籤匯出至 JSON 檔案，以供匯入到不同的瀏覽器或裝置。

#### <a name="export-environment-settings"></a>匯出環境設定

1. 在 [設定] > [一般] 視窗中，選取 [匯出]。
1. **adx-export.json** 檔案將會下載至您的本機儲存體。
1. 選取 [清除本機狀態] 可將您的環境還原為原始狀態。 此設定會刪除您所有的叢集連線，並關閉開啟的索引標籤。

> [!NOTE]
> [匯出] 只會匯出查詢的相關資料。 **adx-export.json** 檔案內不會匯出任何儀表板資料。

#### <a name="import-environment-settings"></a>匯入環境設定

1. 在 [設定] > [一般] 視窗中，選取 [匯入]。 然後在 [警告] 快顯視窗中，選取 [匯入]。

    :::image type="content" source="media/web-query-data/import.png" alt-text="匯入警告":::

1. 從您的本機儲存體找出 **adx-export.json** 檔案，然後將其開啟。
1. 您先前的叢集連線和已開啟的索引標籤現在便可供使用。

> [!NOTE]
> [匯入] 會覆寫任何現有的環境設定和資料。

### <a name="highlight-error-levels"></a>反白顯示錯誤層級

Kusto 會嘗試解譯結果面板中每個資料列的嚴重性和詳細程度層級，並且據以調整其色彩。 其方式是將每個資料行的相異值與一組已知模式 (「警告」、「錯誤」等等) 進行比對。 

#### <a name="enable-error-level-highlighting"></a>啟用錯誤層級反白顯示

若要啟用錯誤層級反白顯示：

1. 選取您的使用者名稱旁邊的「設定」圖示。
1. 選取 [外觀]索引標籤，並將 [啟用錯誤層級反白顯示] 選項切換至右邊。 

    :::image type="content" source="media/web-query-data/enable-error-highlighting.gif" alt-text="動畫 GIF 會示範如何在設定中，啟用錯誤層級的反白顯示":::

**淺色** 模式中的錯誤層級色彩配置 | **深色** 模式中的錯誤層級色彩配置
|---|---|
:::image type="content" source="media/web-query-data/light-mode.png" alt-text="淺色模式中色彩圖例的螢幕擷取畫面"::: | :::image type="content" source="media/web-query-data/dark-mode.png" alt-text="深色模式中色彩圖例的螢幕擷取畫面":::

#### <a name="column-requirements-for-highlighting"></a>反白顯示的資料行需求

若為反白顯示的錯誤層級，資料行的類型必須是 int、long 或 string。 

* 如果資料行的類型是 `long` 或 `int` ：
   * 資料行名稱必須是 *層級*
   * 值只可包含介於1到5之間的數位。
* 如果資料行的類型為 `string` ： 
   * 資料行名稱可以選擇性地為提升效能的 *層級* 。 
   * 此資料行只能包含下列值：
       * critical、臨界、critical、assert、high
       * 錯誤，e
       * 警告，w，監視器
       * 資訊
       * 詳細資訊，動詞，d
   
## <a name="provide-feedback"></a>提供意見反應

1. 在應用程式右上角，選取意見反應圖示 :::image type="icon" source="media/web-query-data/icon-feedback.png" border="false":::。

1. 輸入您的意見反應，然後選取 [提交]。

## <a name="clean-up-resources"></a>清除資源

您並未在此快速入門中建立任何資源，但若您要從應用程式移除一或兩個叢集，請以滑鼠右鍵按一下叢集，然後選取 [移除連線]。
另一個選項是從 [設定] > [一般] 索引標籤中選取 [清除本機狀態]。此動作將會移除所有叢集連線，並關閉所有已開啟的查詢索引標籤。

## <a name="next-steps"></a>後續步驟

[撰寫 Azure 資料總管的查詢](write-queries.md)
