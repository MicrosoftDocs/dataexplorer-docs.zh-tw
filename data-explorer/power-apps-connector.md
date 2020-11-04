---
title: 建立 Power Apps 應用程式來查詢 Azure 資料總管中的資料
description: 瞭解如何根據 Azure 中的資料，在 Power Apps 中建立應用程式資料總管
author: orspod
ms.author: orspodek
ms.reviewer: olgolden
ms.service: data-explorer
ms.topic: how-to
ms.date: 10/20/2020
ms.openlocfilehash: 17ee68efa1f7c43814c4c86f9e5881cd0ba9af61
ms.sourcegitcommit: 455d902bad0aae3e3d72269798c754f51442270e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93349540"
---
# <a name="create-power-apps-application-to-query-data-in-azure-data-explorer-preview"></a>建立 Power Apps 應用程式以在 Azure 資料總管中查詢資料 (預覽) 

Azure 資料總管是快速、完全受控的資料分析服務，可即時分析來自應用程式、網站、IoT 裝置等的大量資料。

Power Apps 是一套應用程式、服務、連接器和資料平臺，可提供快速的應用程式開發環境，以建立連接至您商務資料的自訂應用程式。 如果您在 Azure 資料總管中有大量且不斷成長的串流資料集合，而且想要建立低程式碼、高功能的應用程式來使用此資料，Power Apps 連接器特別有用。 在本文中，您將建立 Power Apps 應用程式來查詢 Azure 資料總管資料。 在這個過程中，您將會看到資料參數化、抓取和呈現的步驟。

## <a name="prerequisites"></a>先決條件

* Power platform 授權。 開始著手 [https://powerapps.microsoft.com](https://powerapps.microsoft.com) 。
* 熟悉 [Power Apps 套件](/powerapps/powerapps-overview)。

## <a name="connect-to-azure-data-explorer-connector"></a>連接到 Azure 資料總管連接器

1. 流覽 [https://make.preview.powerapps.com/](https://make.preview.powerapps.com/) 並登入。

1. 在左側功能表中選取 [ **連接** ]。
1. 選取 [ **+ 新增連接** ]。

    :::image type="content" source="media/power-apps-connector/new-connection.png" alt-text="在 Power Apps 中建立新的連接":::

1. 搜尋搜尋列中的 **Azure 資料總管** 。 從產生的選項中選取 [ **Azure 資料總管** ]。

    :::image type="content" source="media/power-apps-connector/search-adx.png" alt-text="在 Power Apps 中搜尋並選取 Azure 資料總管連接":::

1. 選取 [ **Azure 資料總管** ] 快顯視窗上的 [ **建立** ]。 視需要提供認證。

    :::image type="content" source="media/power-apps-connector/create-connector.png" alt-text="建立 Azure 資料總管的連接器-快顯視窗":::

## <a name="create-app"></a>建立應用程式

1. 流覽至 Power Apps，然後選取左側功能表中的 [ **應用程式** ]。
1. 在功能表列中選取 [ **+ 新增應用程式** ]。
1. 從產生的下拉式清單中選取 [ **Canvas** ]。

    :::image type="content" source="media/power-apps-connector/create-new-app.png" alt-text="建立新的應用程式和 canvas Power Apps 連接器至 Azure 資料總管":::

1. 在 [ **空白應用程式** ] 區段中，選取 [ **Tablet 版面** 配置]。

    :::image type="content" source="media/power-apps-connector/blank-canvas.png" alt-text="從平板電腦版面配置中的空白畫布開始（Power Apps 連接器至 Azure 資料總管":::

### <a name="add-connector"></a>新增連接器

1. 按一下左側導覽上的 **資料** 圖示。 
1. 展開 [ **連接器** ]。
1. 在產生的選項中選取 [ **Azure 資料總管** ]。

    :::image type="content" source="media/power-apps-connector/data-connectors-adx.png" alt-text="將連接器新增至 Azure 中的 Azure 資料總管 Power Apps":::

您將會 **在應用程式中** 看到新的區域，其中包含 **Azure 資料總管** 現在已包含在內。

   :::image type="content" source="media/power-apps-connector/adx-appears.png" alt-text="Azure 資料總管現在會出現在應用程式區域的 Power Apps":::

### <a name="save-your-app"></a>儲存您的應用程式

1. 在功能表列 **中選取 [** 檔案]。 
1. 選取左側導覽中的 [ **儲存** ]。

    :::image type="content" source="media/power-apps-connector/save-app.png" alt-text="將您的應用程式儲存至 Power Apps":::

1. 為您的應用程式輸入有意義的名稱。 按一下右下方的 [ **儲存** ] 按鈕。

### <a name="advanced-settings"></a>進階設定

1. 選取左側功能表中的 [ **設定** ]。
1. 選取 [進階設定]  。
1. 從產生的選項中選取 **動態架構** 。 啟用此功能。

    :::image type="content" source="media/power-apps-connector/dynamic-schema.png" alt-text="在 Power Apps-連線至 Azure 資料總管中開啟動態架構設定":::

1. 搜尋 **非委派查詢設定的資料列限制** 。 設定傳回的記錄限制。

    :::image type="content" source="media/power-apps-connector/set-limit.png" alt-text="在 Power Apps 中設定傳回結果限制-Azure 資料總管":::

    > [!NOTE]
    > 預設限制為500，最大值為2000傳回的記錄。

> [!IMPORTANT]
> 重新儲存您的應用程式，並視需要重新開機。

### <a name="add-dropdown"></a>新增下拉式清單

1. 選取功能表列中的 [ **插入** ]。 
1. 在產生的子功能表欄中選取 [ **輸入** ]。 
1. 在產生的下拉式清單中選取 **下拉式** 清單。
1. 按一下右側彈出中的 [ **Advanced** ] 索引標籤。
1. 將 **專案** 輸入方塊填入： ["加州"，"密歇根"]

    :::image type="content" source="media/power-apps-connector/populate-dropdown.png" alt-text="填入下拉式功能表中的專案" lightbox="media/power-apps-connector/populate-dropdown.png":::

1. 在仍選取 **下拉式清單** 的情況下，從公式列的 **屬性** 下拉式清單中選取 [ **OnChange** ]。

1. 輸入下列公式：

    ```kusto
    ClearCollect(
    Results,
    AzureDataExplorer.listKustoResultsPost(
    "https://help.kusto.windows.net",
    "Samples",
    "StormEvents | where State == '" & Dropdown1.SelectedText.Value & "' | take 15"
    ).value
    )
    ```
    
1. 按一下 [ **捕捉架構** ] 按鈕。 允許時間進行處理。

    :::image type="content" source="media/power-apps-connector/capture-schema.png" alt-text="在下拉式功能表中選取 [捕捉架構] 按鈕":::

### <a name="add-data-table"></a>新增資料表

1. 選取功能表列中的 [ **插入** ]。 
1. 在產生的子功能表欄中選取 [ **資料表** ]。
1. 重新調整資料表的位置，並考慮加入框線以提供可見度。
1. 選取右側彈出中的 [ **屬性** ] 索引標籤。 從 [ **資料來源** ] 下拉式清單中選取結果。
1. 選取 [ **編輯欄位** ] 連結。 
1. 在產生的彈出中選取 [ **+ 新增欄位** ]。 
    
    :::image type="content" source="media/power-apps-connector/insert-data-table-small.png" alt-text="重新放置資料表並加入框線" lightbox="media/power-apps-connector/insert-data-table.png":::

1. 選取所需的欄位，然後按一下 [ **加入** 按鈕]。 所選資料表的預覽隨即出現。

    :::image type="content" source="media/power-apps-connector/preview-table.png" alt-text="填入資料的資料表預覽":::

### <a name="validate"></a>Validate

1. 按一下螢幕右上方的 [ **預覽應用程式** ] 按鈕。
1. 請嘗試下拉式清單，並在資料表中進行滾動，確認資料的抓取和呈現成功。

    :::image type="content" source="media/power-apps-connector/preview-app.png" alt-text="使用 Azure 資料總管的資料在 Power Apps 中預覽新的應用程式 ":::

### <a name="limitations"></a>限制

* Power Apps 有最多2000個傳回給用戶端之結果記錄的限制。 這些記錄的整體記憶體不能超過 64 MB，而且會有七分鐘的時間來執行。
* 連接器不支援 [分叉](./kusto/query/forkoperator.md) 和 [facet](./kusto/query/facetoperator.md) 運算子。
* **Timeout 例外** 狀況：連接器的超時限制為7分鐘。 為了避免可能的超時問題，請讓您的查詢更有效率，讓它執行得更快，或將它分成多個區塊。 每個區塊都可以在查詢的不同部分上執行。 如需詳細資訊，請參閱 [查詢最佳做法](./kusto/query/best-practices.md)。

## <a name="next-steps"></a>後續步驟