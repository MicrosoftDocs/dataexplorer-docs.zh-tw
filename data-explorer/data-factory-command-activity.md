---
title: 在 Azure Data Factory 中使用 Azure 資料總管控制命令
description: 在本主題中，您會在 Azure Data Factory 中使用 Azure 資料總管控制命令
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 09/15/2019
ms.openlocfilehash: af351530b8622785f76c6165aede056073aff67f
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88873384"
---
# <a name="use-azure-data-factory-command-activity-to-run-azure-data-explorer-control-commands"></a>使用 Azure Data Factory 命令活動來執行 Azure 資料總管控制命令

[Azure Data Factory](/azure/data-factory/) (ADF) 是以雲端為基礎的資料整合服務，可讓您在資料上執行活動的組合。 使用 ADF 來建立資料驅動的工作流程，以協調和自動化資料移動和資料轉換。 Azure Data Factory 中的 **azure 資料總管命令** 活動可讓您在 ADF 工作流程內執行 [azure 資料總管控制命令](kusto/concepts/index.md#control-commands) 。 本文會教您如何使用查閱活動和 ForEach 活動（包含 Azure 資料總管命令活動）建立管線。

## <a name="prerequisites"></a>Prerequisites

* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。
* [Azure 資料總管叢集和資料庫](create-cluster-database-portal.md)
* 資料來源。
* [Data factory](data-factory-load-data.md#create-a-data-factory)

## <a name="create-a-new-pipeline"></a>建立新管線

1. 選取 [ **作者** 鉛筆] 工具。 
1. 選取 **+** []，然後從下拉式清單中選取 [ **管線** ]，以建立新的管線。

   ![建立新的管線](media/data-factory-command-activity/create-pipeline.png)

## <a name="create-a-lookup-activity"></a>建立查閱活動

[查閱活動](/azure/data-factory/control-flow-lookup-activity)可以從任何 Azure Data Factory 支援的資料來源取得資料集。 查閱活動的輸出可用於 ForEach 或其他活動中。

1. 在 [ **活動** ] 窗格中，選取 [ **一般**] 底下的 **查閱** 活動。 將它拖放到右邊的主要畫布中。
 
    ![選取查閱活動](media/data-factory-command-activity/select-activity.png)

1. 畫布現在包含您所建立的查閱活動。 使用畫布下方的索引標籤來變更任何相關的參數。 **一般情況**下，請重新命名活動。 

    ![編輯查閱活動](media/data-factory-command-activity/edit-lookup-activity.png)

    > [!TIP]
    > 按一下空白畫布區域以查看管線屬性。 使用 [ **一般** ] 索引標籤來重新命名管線。 我們的管線命名為 *pipeline-4*檔。

### <a name="create-an-azure-data-explorer-dataset-in-lookup-activity"></a>在查閱活動中建立 Azure 資料總管資料集

1. 在 [ **設定**] 中，選取預先建立的 Azure 資料總管 **源資料集**，或選取 [ **+ 新增** ] 來建立新的資料集。
 
    ![在查閱設定中加入資料集](media/data-factory-command-activity/lookup-settings.png)

1. 從 [**新增資料集**] 視窗中選取 [ **Azure 資料總管 (Kusto) **資料集]。 選取 [ **繼續** ] 以新增新的資料集。

   ![選取新的資料集](media/data-factory-command-activity/select-new-dataset.png) 

1. 新的 Azure 資料總管資料集參數會顯示在 [ **設定**] 中。 若要更新參數，請選取 [ **編輯**]。

    ![使用 Azure 資料總管資料集的查閱設定](media/data-factory-command-activity/lookup-settings-with-adx-dataset.png)

1. [ **>azuredataexplorertable** 新增] 索引標籤會在主要畫布中開啟。 
    * 選取 **[一般** ] 並編輯資料集名稱。 
    * 選取 [ **連接** ] 以編輯資料集屬性。 
    * 從下拉式清單中選取 **連結服務** ，或選取 [ **+ 新增** ] 來建立新的連結服務。

    ![編輯 Azure 資料總管資料集屬性](media/data-factory-command-activity/adx-dataset-properties-edit-connections.png)

1. 建立新的連結服務時，會開啟 **新的連結服務 (Azure 資料總管) ** 頁面：

    ![ADX 新的連結服務](media/data-factory-command-activity/adx-new-linked-service.png)

   * 選取 Azure 資料總管連結服務的 **名稱** 。 視需要新增 **描述** 。
   * 在 **[透過整合運行**時間連線] 中，視需要變更目前的設定。 
   * 在 [ **帳戶選取方法** ] 中，使用下列兩種方法之一來選取您的叢集： 
        * 選取 [ **從 azure 訂用** 帳戶] 選項按鈕，然後選取您的 **azure 訂** 用帳戶。 然後， **選取您的**叢集。 請注意，下拉式清單只會列出屬於該使用者的群集。
        * 請改為選取 [ **手動輸入** ] 選項按鈕，然後輸入您的 **端點** (叢集 URL) 。
    * 指定 **租**使用者。
    * 輸入 **服務主體識別碼**。 主體識別碼必須具有適當的許可權，根據所使用命令所需的許可權層級而定。
    * 選取 [ **服務主體金鑰** ] 按鈕，然後輸入 **服務主體金鑰**。
    * 從下拉式功能表中選取您的 **資料庫** 。 或者，選取 [ **編輯** ] 核取方塊，然後輸入您的資料庫名稱。
    * 選取 [ **測試連接** ] 以測試您所建立的連結服務連接。 如果您可以連接到您的設定，則會出現綠色的核取記號 **連接成功** 。
    * 選取 **[完成]** 以完成連結服務建立。

1. 設定連結服務之後，請在 [ **>azuredataexplorertable**  >  **連接**] 中加入**資料表**名稱。 選取 [ **預覽資料**]，確認資料已正確呈現。

   您的資料集現在已就緒，您可以繼續編輯管線。

### <a name="add-a-query-to-your-lookup-activity"></a>將查詢新增至查閱活動

1. 在 [**管線-4-** 檔設定] 的 [  >  **Settings** **查詢**] 文字方塊中加入查詢，例如：

    ```kusto
    ClusterQueries
    | where Database !in ("KustoMonitoringPersistentDatabase", "$systemdb")
    | summarize count() by Database
    ```

1. 視需要變更 [ **查詢超時** **] 或 [** **僅限第一個資料列** 的屬性]。 在此流程中，我們會保留預設的 **查詢超時** ，並取消選取核取方塊。 

    ![查閱活動的最後設定](media/data-factory-command-activity/lookup-activity-final-settings.png)

## <a name="create-a-for-each-activity"></a>建立 For Each 活動 

[For each](/azure/data-factory/control-flow-for-each-activity)活動可用來反復查看集合，並在迴圈中執行指定的活動。 

1. 現在您可以將 For Each 活動新增至管線。 此活動會處理從查閱活動傳回的資料。 
    * 在 [ **活動** ] 窗格中的 [ **反復專案 & 條件**] 底下，選取 [ **ForEach** ] 活動，然後將它拖放到畫布中。
    * 在查閱活動的輸出與畫布中 ForEach 活動的輸入之間繪製一條線來連接它們。

        ![ForEach 活動](media/data-factory-command-activity/for-each-activity.png)

1. 在畫布中選取 ForEach 活動。 在下方的 [ **設定** ] 索引標籤中：
    * 請檢查 **連續** 的核取方塊，以取得查閱結果的連續處理，或保持未核取以建立平行處理。
    * 設定 **批次計數**。
    * 在 [**專案**] 中，提供下列輸出值的參考： * @activity ( ' Lookup1 ' ) 。值*

       ![ForEach 活動設定](media/data-factory-command-activity/for-each-activity-settings.png)

## <a name="create-an-azure-data-explorer-command-activity-within-the-foreach-activity"></a>在 ForEach 活動內建立 Azure 資料總管命令活動

1. 按兩下畫布中的 ForEach 活動，在新的畫布中開啟它，以指定 ForEach 內的活動。
1. 在 [ **活動** ] 窗格中，選取 [ **azure 資料總管**] 下的 [ **azure 資料總管命令** ] 活動，然後將其拖放到畫布中。

    ![Azure 資料總管命令活動](media/data-factory-command-activity/adx-command-activity.png)

1. 在 [ **連接** ] 索引標籤中，選取先前建立的相同連結服務。

    ![azure 資料瀏覽器命令活動連線索引標籤](media/data-factory-command-activity/adx-command-activity-connection-tab.png)

1. 在 [ **命令** ] 索引標籤中，提供下列命令：

    ```kusto
    .export
    async compressed
    into csv h"http://<storageName>.blob.core.windows.net/data/ClusterQueries;<storageKey>" with (
    sizeLimit=100000,
    namePrefix=export
    )
    <| ClusterQueries | where Database == "@{item().Database}"
    ```

    此 **命令** 會指示 Azure 資料總管以壓縮格式將指定查詢的結果匯出至 blob 儲存體。 它會使用 async 修飾詞) 以非同步方式執行 (。
    查詢會定址查閱活動結果中每個資料列的資料庫資料行。 **命令逾時**可保持不變。

    ![命令活動](media/data-factory-command-activity/command.png)   

    > [!NOTE]
    > 命令活動有下列限制：
    > * 大小限制： 1 MB 回應大小
    > * 時間限制：20分鐘 (預設) ，1小時 (最大) 。
    > * 如有需要，您可以使用 [AdminThenQuery](kusto/management/index.md#combining-queries-and-control-commands)將查詢附加至結果，以減少產生的大小/時間。

1. 現在管線已就緒。 您可以按一下管線名稱，返回主要管線視圖。

    ![Azure 資料總管命令管線](media/data-factory-command-activity/adx-command-pipeline.png)

1. 在發佈管線之前，請先選取 [ **Debug** ]。 您可以在 [ **輸出** ] 索引標籤中監視管線進度。

    ![azure 資料瀏覽器命令活動輸出](media/data-factory-command-activity/command-activity-output.png)

1. 您可以 **全部發佈** ，然後 **新增觸發** 程式來執行管線。 

## <a name="control-command-outputs"></a>控制命令輸出

命令活動輸出的結構如下所述。 此輸出可供管線中的下一個活動使用。

### <a name="returned-value-of-a-non-async-control-command"></a>非 async control 命令的傳回值

在非非同步控制命令中，傳回值的結構與查閱活動結果的結構相似。 `count`欄位指出傳回的記錄數目。 固定陣列欄位 `value` 包含記錄清單。 

```json
{ 
    "count": "2", 
    "value": [ 
        { 
            "ExtentId": "1b9977fe-e6cf-4cda-84f3-4a7c61f28ecd", 
            "ExtentSize": 1214.0, 
            "CompressedSize": 520.0 
        }, 
        { 
            "ExtentId": "b897f5a3-62b0-441d-95ca-bf7a88952974", 
            "ExtentSize": 1114.0, 
            "CompressedSize": 504.0 
        } 
    ] 
} 
```
 
### <a name="returned-value-of-an-async-control-command"></a>非同步控制命令的傳回值

在 async control 命令中，活動會輪詢幕後的作業資料表，直到非同步作業完成或超時為止。因此，傳回的值將包含 `.show operations OperationId` 該指定 **OperationId** 屬性的結果。 檢查 [ **狀態** ] 和 [ **狀態** ] 屬性的值，以確認作業是否順利完成。

```json
{ 
    "count": "1", 
    "value": [ 
        { 
            "OperationId": "910deeae-dd79-44a4-a3a2-087a90d4bb42", 
            "Operation": "TableSetOrAppend", 
            "NodeId": "", 
            "StartedOn": "2019-06-23T10:12:44.0371419Z", 
            "LastUpdatedOn": "2019-06-23T10:12:46.7871468Z", 
            "Duration": "00:00:02.7500049", 
            "State": "Completed", 
            "Status": "", 
            "RootActivityId": "f7c5aaaf-197b-4593-8ba0-e864c94c3c6f", 
            "ShouldRetry": false, 
            "Database": "MyDatabase", 
            "Principal": "<some principal id>", 
            "User": "<some User id>" 
        } 
    ] 
}
``` 

## <a name="next-steps"></a>後續步驟

* 瞭解如何 [使用 Azure Data Factory 將資料複製到 Azure 資料總管](data-factory-load-data.md)。
* 瞭解如何使用 [Azure Data Factory 範本，從資料庫大量複製到 Azure 資料總管](data-factory-template.md)。
