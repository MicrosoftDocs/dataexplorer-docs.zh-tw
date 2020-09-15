---
title: 使用 Grafana 將 Azure 資料總管中的資料視覺化
description: 在本文中，您將瞭解如何將 Azure 資料總管設定為 Grafana 的資料來源，然後將範例叢集中的資料視覺化。
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: how-to
ms.date: 09/09/2020
ms.openlocfilehash: 08093fd06fed1facc1d8e55d98785abb952632c8
ms.sourcegitcommit: 95527c793eb873f0135c4f0e9a2f661ca55305e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2020
ms.locfileid: "90534039"
---
# <a name="visualize-data-from-azure-data-explorer-in-grafana"></a>在 Grafana 中從 Azure 資料總管將資料視覺化

Grafana 是分析平台，可讓您查詢和視覺化資料，然後根據您的視覺效果建立並共用儀表板。 Grafana 提供 Azure 資料總管「外掛程式」**，可讓您從 Azure 資料總管連線到資料並加以視覺化。 在本文中，您將瞭解如何將 Azure 資料總管設定為 Grafana 的資料來源，然後將範例叢集中的資料視覺化。

使用下列影片，瞭解如何使用 Grafana 的 Azure 資料總管外掛程式、將 Azure 資料總管設定為 Grafana 的資料來源，然後將資料視覺化。 

> [!VIDEO https://www.youtube.com/embed/fSR_qCIFZSA]

相反地，您可以 [設定資料來源](#configure-the-data-source) 並以 [視覺化方式呈現資料](#visualize-data) ，如下列文章中所述。

## <a name="prerequisites"></a>Prerequisites

您需要下列專案才能完成這篇文章：

* 適用於您作業系統的 [Grafana 5.3.0 版或更新版本](https://docs.grafana.org/installation/)

* 適用于 Grafana 的 [Azure 資料總管外掛程式](https://grafana.com/plugins/grafana-azure-data-explorer-datasource/installation) 。 需要外掛程式版本3.0.5 或更新版本，才能使用 Grafana query builder。

* 包含 StormEvents 範例資料的叢集。 如需詳細資訊。請參閱[快速入門：建立 Azure 資料總管叢集與資料庫](create-cluster-database-portal.md)及[將範例資料內嵌至 Azure 資料總管](ingest-sample-data.md)。

    [!INCLUDE [data-explorer-storm-events](includes/data-explorer-storm-events.md)]

[!INCLUDE [data-explorer-configure-data-source](includes/data-explorer-configure-data-source.md)]

### <a name="specify-properties-and-test-the-connection"></a>指定屬性並測試連線

將服務主體指派給「檢視者」** 角色後，您現在要在 Grafana 執行個體中指定屬性，並測試與 Azure 資料總管的連線。

1. 在 Grafana 的左側功能表中，選取齒輪圖示，然後選取 [資料來源]****。

    ![資料來源](media/grafana/data-sources.png)

1. 選取 [新增資料來源] ****。

1. 在 [資料來源] / [新增]**** 頁面上，輸入資料來源名稱，然後選取 [Azure 資料總管資料來源]**** 類別。

    ![連線名稱和類型](media/grafana/connection-name-type.png)

1. 以下列格式輸入您的叢集名稱：https://{ClusterName}.{Region}.kusto.windows.net。 從 Azure 入口網站或 CLI 輸入其他值。 請參閱下圖底下的對應表。

    ![連線內容](media/grafana/connection-properties.png)

    | Grafana UI | Azure 入口網站 | Azure CLI |
    | --- | --- | --- |
    | 訂用帳戶識別碼 | 訂用帳戶識別碼 | SubscriptionId |
    | 租用戶識別碼 | 目錄識別碼 | tenant |
    | 用戶端識別碼 | 應用程式識別碼 | appId |
    | 用戶端密碼 | 密碼 | 密碼 |
    | | | |

1. 選取 [儲存並測試]****。

    如果測試成功，請移至下一節。 如果您遇到任何問題，請檢查您在 Grafana 中指定的值，並檢查先前的步驟。

## <a name="visualize-data"></a>顯現資料

現在您已將 Azure 資料總管設為 Grafana 的資料來源，是時候將資料視覺化了。 我們將示範一個使用查詢產生器模式和 [查詢編輯器] 的 raw 模式的基本範例。 我們建議您查看[撰寫 Azure 資料總管的查詢](write-queries.md)，以取得其他針對範例資料集執行的查詢範例。

1. 在 Grafana 的左側功能表中，選取加號圖示，然後選取 [儀表板]****。

    ![建立儀表板](media/grafana/create-dashboard.png)

1. 在 [ **新增** ] 索引標籤下，選取 [ **加入新面板**]。

    ![新增圖表](media/grafana/add-graph.png)

1. 在 [圖表] 面板中，依序選取 [面板標題]**** 和 [編輯]****。

    ![編輯面板](media/grafana/edit-panel.png)

1. 在面板底部，選取 [資料來源]****，然後選取您所設定的資料來源。

    ![選取資料來源](media/grafana/select-data-source.png)

### <a name="query-builder-mode"></a>查詢產生器模式

查詢編輯器有兩種模式。 查詢產生器模式和 raw 模式。 使用查詢產生器模式來定義您的查詢。

1. 在 [資料來源] 底下，選取 [ **資料庫** ]，然後從下拉式清單中選擇您的資料庫。 
1. 從下拉式 **清單中選取並選擇** 您的資料表。

    :::image type="content" source="media/grafana/query-builder-from-table.png" alt-text="在查詢產生器中選取資料表":::    

1. 定義資料表之後，請篩選資料、選取要顯示的值，然後定義這些值的群組。

    **Filter**
    1. 按一下 **+** 以 ** (篩選) ** 從資料表的下拉式清單中選取一個或多個資料行。 
    1. 針對每個篩選準則，使用適用的運算子來定義)  (s 值。 
    此選項類似于在 Kusto 查詢語言中使用 [where 運算子](kusto/query/whereoperator.md) 。

    **值選取**
    1. 按一下 [ **+** 值資料 **行** ]，即可從下拉式清單中選取要顯示在面板中的值資料行。
    1. 針對每個 [值] 資料行，設定匯總類型。 
    可以設定一或多個值資料行。 此選項相當於使用 [摘要運算子](kusto/query/summarizeoperator.md)。

    **值群組** <br> 
    按一下 **+** [ **群組依據] ([摘要]) ** ，從下拉式清單中選取要用來將值排列成群組的一或多個資料行。 這相當於摘要運算子中的群組運算式。

1. 若要執行查詢，請選取 [ **執行查詢**]。

    :::image type="content" source="media/grafana/query-builder-all-values.png" alt-text="所有值都已完成的查詢產生器":::

    > [!TIP]
    > 在查詢產生器中完成設定時，會建立 Kusto 查詢語言查詢。 此查詢會顯示您使用圖形化查詢編輯器所建造的邏輯。 

1. 選取 [ **編輯 KQL** ] 以移至 raw 模式，並使用 Kusto 查詢語言的彈性和功能來編輯查詢。

:::image type="content" source="media/grafana/query-builder-with-raw-query.png" alt-text="具有原始查詢的查詢產生器":::

### <a name="raw-mode"></a>Raw 模式

使用 raw 模式來編輯查詢。 

1. 在 [查詢] 窗格中，複製下列查詢，然後選取 [ **執行查詢**]。 查詢會包含範例資料集的每日事件計數。

    ```kusto
    StormEvents
    | summarize event_count=count() by bin(StartTime, 1d)
    ```

    ![執行查詢](media/grafana/run-query.png)

1. 圖表未顯示任何結果，因為其預設的資料範圍是過去六小時。 在頂端功能表上，選取 [過去 6 小時]****。

    ![過去六小時](media/grafana/last-six-hours.png)

1. 指定自訂範圍以涵蓋 2007 年，也就是 StormEvents 範例資料集包含的年份。 選取 [套用]。

    ![自訂日期範圍](media/grafana/custom-date-range.png)

    現在圖表會顯示 2007 中的資料，並依據日期分佈。

    ![已完成的圖表](media/grafana/finished-graph.png)

1. 在最上層功能表中，選取儲存圖示： ![[儲存] 圖示](media/grafana/save-icon.png).

> [!IMPORTANT]
> 若要切換到 [查詢產生器] 模式，請選取 [ **切換至**產生器]。 Grafana 會將查詢轉換成查詢產生器中的可用邏輯。 查詢產生器邏輯會受到限制，因此您可能會遺失對查詢所做的手動變更。

:::image type="content" source="media/grafana/raw-mode.png" alt-text="從原始模式移至 builder":::

## <a name="create-alerts"></a>建立警示

1. 在 [首頁] 儀表板中，選取**警示**  >  **通知通道**以建立新的通知通道

    ![建立通知通道](media/grafana/create-notification-channel.png)

1. 建立新的 **通知通道**，然後 **儲存**。

    ![建立新的通知通道](media/grafana/new-notification-channel-adx.png)

1. 在 [ **儀表板**] 上，從下拉式清單中選取 [ **編輯** ]。

    ![選取儀表板中的 [編輯]](media/grafana/edit-panel-4-alert.png)

1. 選取警示鐘圖示以開啟 [ **警示** ] 窗格。 選取 [ **建立警示**]。 在 [ **警示** ] 窗格中完成下列屬性。

    ![警示屬性](media/grafana/alert-properties.png)

1. 選取 [ **儲存儀表板** ] 圖示以儲存您的變更。

## <a name="next-steps"></a>後續步驟

* [撰寫 Azure 資料總管的查詢](write-queries.md)
