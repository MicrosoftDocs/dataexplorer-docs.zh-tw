---
title: 將 Azure Blob 擷取至 Azure 資料總管
description: 在本文中，您將瞭解如何使用 Event Grid 訂用帳戶，將儲存體帳戶資料傳送至 Azure 資料總管。
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 08/13/2020
ms.openlocfilehash: 2c5c5cbb15e55b585bae632a960909070c724eb8
ms.sourcegitcommit: 4d5628b52b84f7564ea893f621bdf1a45113c137
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96444221"
---
# <a name="ingest-blobs-into-azure-data-explorer-by-subscribing-to-event-grid-notifications"></a>訂閱 Event Grid 通知，以便將 Blob 擷取至 Azure 資料總管

> [!div class="op_single_selector"]
> * [入口網站](ingest-data-event-grid.md)
> * [C#](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Azure Resource Manager 範本](data-connection-event-grid-resource-manager.md)

[!INCLUDE [data-connector-intro](includes/data-connector-intro.md)]

在本文中，您將瞭解如何使用 Event Grid 資料連線，將 blob 從儲存體帳戶內嵌到 Azure 資料總管。 您將建立可設定 [Azure 事件方格](/azure/event-grid/overview) 訂用帳戶的事件方格資料連線。 事件方格訂用帳戶會透過 Azure 事件中樞，將事件從您的儲存體帳戶路由傳送到 Azure 資料總管。 然後，您會看到整個系統中的資料流程範例。 

如需從事件方格擷取至 Azure 資料總管的一般資訊，請參閱 [連接到事件方格](ingest-data-event-grid-overview.md)。 若要在 Azure 入口網站中手動建立資源，請參閱 [手動建立事件方格內嵌的資源](ingest-data-event-grid-manual.md)。

## <a name="prerequisites"></a>先決條件

* Azure 訂用帳戶。 建立 [免費的 Azure 帳戶](https://azure.microsoft.com/free/)。
* 叢集[和資料庫](create-cluster-database-portal.md)。
* [儲存體帳戶](/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)。
    * 您可以針對 `BlobStorage` 、 `StorageV2` 或 [Data Lake Storage Gen2](/azure/storage/blobs/data-lake-storage-introduction)Azure 儲存體帳戶設定事件方格通知訂閱。


## <a name="create-a-target-table-in-azure-data-explorer"></a>在 Azure 資料總管中建立目標資料表

在 Azure 資料總管中建立一個資料表，供事件中樞將資料傳送至此。 在必要條件中準備的叢集與資料庫內建立該資料表。

1. 在 Azure 入口網站中，您的叢集下方，選取 [查詢]。

    :::image type="content" source="media/ingest-data-event-grid/query-explorer-link.png" alt-text="連結至查詢瀏覽器"::: 

1. 將下列命令複製到視窗，然後選取 [執行] 以建立資料表 (TestTable)，該資料表會接收內嵌的資料。

    ```kusto
    .create table TestTable (TimeStamp: datetime, Value: string, Source:string)
    ```

    :::image type="content" source="media/ingest-data-event-grid/run-create-table.png" alt-text="執行命令 create table":::

1. 將下列命令複製到視窗中，然後選取 [執行] 以將傳入的 JSON 資料對應至資料表 (TestTable) 的資料行名稱與資料類型。

    ```kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp","path":"$.TimeStamp"},{"column":"Value","path":"$.Value"},{"column":"Source","path":"$.Source"}]'
    ```

## <a name="create-an-event-grid-data-connection-in-azure-data-explorer"></a>在 Azure 資料總管中建立 Event Grid 資料連線

現在，將儲存體帳戶連線至 Azure 資料總管，以便將流入儲存體的資料串流處理到測試資料表。 

1. 在您建立的叢集下方，選取 [**資料庫**  >  **>testdatabase**]。

    :::image type="content" source="media/ingest-data-event-grid/select-test-database.png" alt-text="選取測試資料庫":::

1. 選取 [**資料** 內嵌  >  **新增資料連線**]。

    :::image type="content" source="media/ingest-data-event-grid/data-ingestion-create.png" alt-text="新增資料內嵌的資料連線":::

### <a name="data-connection---basics-tab"></a>資料連線-基本資料索引標籤

1. 選取連線類型： **Blob 儲存體**。

1. 在表單中填寫以下資訊：

    :::image type="content" source="media/ingest-data-event-grid/data-connection-basics.png" alt-text="在事件方格表單中填寫連接基本概念":::

    |**設定** | **建議的值** | **欄位描述**|
    |---|---|---|
    | 資料連線名稱 | *test-grid-connection* | 您想要在 Azure 資料總管中建立的連線名稱。|
    | 儲存體帳戶訂用帳戶 | 您的訂用帳戶識別碼 | 儲存體帳戶所在的訂用帳戶識別碼。|
    | 儲存體帳戶 | *gridteststorage1* | 您先前建立之儲存體帳戶的名稱。|
    | 事件類型 | *Blob 已建立* 或 *blob 重新命名* | 觸發內嵌的事件種類。 |
    | 資源建立 | *自動* | 定義您想要 Azure 資料總管為您建立事件方格訂用帳戶、事件中樞命名空間和事件中樞。 若要手動建立資源，請參閱 [手動建立事件方格內嵌的資源](ingest-data-event-grid-manual.md)|

1. 如果您想要追蹤特定的主題，請選取 [ **篩選設定** ]。 設定通知的篩選條件，如下所示：
    * [**前置** 詞] 欄位是主旨的 *常* 值前置詞。 當套用的模式為 *startswith* 時，它可以跨越多個容器、資料夾或 blob。 不允許使用萬用字元。
        * 若要在 blob 容器上定義篩選，欄位 *必須* 設定如下： *`/blobServices/default/containers/[container prefix]`* 。
        * 若要在 blob 首碼 (或 Azure Data Lake Gen2) 中的資料夾上定義篩選，欄位 *必須* 設定如下： *`/blobServices/default/containers/[container name]/blobs/[folder/blob prefix]`* 。
    * **尾碼** 欄位是 blob 的 *常* 值尾碼。 不允許使用萬用字元。
    * 區分 **大小寫** 的欄位指出前置詞和後置詞篩選準則是否區分大小寫
    * 如需有關篩選事件的詳細資訊，請參閱 [Blob 儲存體事件](/azure/storage/blobs/storage-blob-event-overview#filtering-events)。
    
    :::image type="content" source="media/ingest-data-event-grid/filter-settings.png" alt-text="篩選設定事件方格":::    

1. 選取 **[下一步：內嵌屬性]**。

### <a name="data-connection---ingest-properties-tab"></a>資料連線-內嵌屬性索引標籤

1. 在表單中填寫以下資訊。 資料表和對應名稱會區分大小寫：

   :::image type="content" source="media/ingest-data-event-grid/data-connection-ingest-properties.png" alt-text="檢查和建立資料表和對應內嵌屬性":::

    內嵌屬性：

     **設定** | **建議的值** | **欄位描述**
    |---|---|---|
    | 資料表名稱 | *TestTable* | 您在 **TestDatabase** 中建立的資料表。 |
    | 資料格式 | *JSON* | 支援的格式為 Avro、CSV、JSON、多行 JSON、ORC、PARQUET、PSV、SCSV、SOHSV、TSV、TXT、TSVE、APACHEAVRO、RAW 和 W3CLOG。 支援的壓縮選項為 Zip 和 GZip。 |
    | 對應 | *TestMapping* | 您在 **TestDatabase** 中建立的對應，會將傳入的 JSON 資料對應至 **TestTable** 的資料行名稱與資料類型。|
    | 進階設定 | *我的資料有標頭* | 忽略標頭。 支援 * SV 類型的檔案。|

   > [!NOTE]
   > 您不必指定所有 **預設路由設定**。 也會接受部分設定。
1. 選取 **下一個：檢查 + 建立**

### <a name="data-connection---review--create-tab"></a>資料連線-[檢查 + 建立] 索引標籤

1. 檢查為您自動建立的資源，然後選取 [ **建立**]。

    :::image type="content" source="media/ingest-data-event-grid/create-event-grid-data-connection-review-create.png" alt-text="檢查和建立事件方格的資料連線":::

### <a name="deployment"></a>部署

請等候部署完成。 如果您的部署失敗，請選取失敗階段旁邊的作業 **詳細資料** ，以取得失敗原因的詳細資訊。 選取 [重新 **部署** ] 以嘗試再次部署資源。 您可以在部署之前變更參數。

:::image type="content" source="media/ingest-data-event-grid/deploy-event-grid-resources.png" alt-text="部署事件方格資源":::

## <a name="generate-sample-data"></a>產生範例資料

既然 Azure 資料總管和儲存體帳戶已連接，您可以建立範例資料，並將它上傳至儲存體容器。

我們將使用可發出一些基本 Azure CLI 命令的小型殼層指令碼，來與 Azure 儲存體資源互動。 此腳本會執行下列動作： 
1. 在儲存體帳戶中建立新的容器。
1. 將現有的檔案 (作為 blob) 上傳至該容器。
1. 列出容器中的 blob。 

您可以使用 [Azure Cloud Shell](/azure/cloud-shell/overview)，直接在入口網站中執行指令碼。

使用這個指令碼將資料儲存到檔案並上傳：

```json
{"TimeStamp": "1987-11-16 12:00","Value": "Hello World","Source": "TestSource"}
```

```azurecli
#!/bin/bash
### A simple Azure Storage example script

    export AZURE_STORAGE_ACCOUNT=<storage_account_name>
    export AZURE_STORAGE_KEY=<storage_account_key>

    export container_name=<container_name>
    export blob_name=<blob_name>
    export file_to_upload=<file_to_upload>
    export destination_file=<destination_file>

    echo "Creating the container..."
    az storage container create --name $container_name

    echo "Uploading the file..."
    az storage blob upload --container-name $container_name --file $file_to_upload --name $blob_name --metadata "rawSizeBytes=1024"

    echo "Listing the blobs..."
    az storage blob list --container-name $container_name --output table

    echo "Done"
```

> [!NOTE]
> 為了達到最佳的內嵌效能，必須傳達針對內嵌提交的壓縮 blob 的 *未壓縮* 大小。 因為事件方格通知只包含基本的詳細資料，所以必須明確地傳達大小資訊。 您可以藉由在 `rawSizeBytes` blob 中繼資料上設定屬性（以位元組為單位），以提供 *未壓縮的* 大小資訊。

### <a name="ingestion-properties"></a>內嵌屬性

您可以透過 blob 中繼資料指定 blob 內嵌的內嵌 [屬性](ingest-data-event-grid-overview.md#ingestion-properties) 。 

> [!NOTE]
> Azure 資料總管不會在內嵌後刪除 blob。
> 將 blob 保留三至五天。
> 使用 [Azure blob 儲存體生命週期](/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal) 來管理 Blob 刪除。 

## <a name="review-the-data-flow"></a>檢閱資料流程

> [!NOTE]
> Azure 資料總管具有資料擷取的彙總 (批次處理) 原則，可將擷取程序最佳化。
> 根據預設，此原則設定為 5 分鐘。
> 您稍後可以視需要修改原則。 在本文中，您可以預期會有幾分鐘的延遲。

1. 當應用程式正在執行時，在 Azure 入口網站內事件格線的下方，您會看見活動爆增。

    :::image type="content" source="media/ingest-data-event-grid/event-grid-graph.png" alt-text="事件方格的活動圖形":::

1. 若要檢查目前為止已有多少則訊息成功進入資料庫，請在測試資料庫中執行下列查詢。

    ```kusto
    TestTable
    | count
    ```

1. 若要查看訊息的內容，請在測試資料庫中執行下列查詢。

    ```kusto
    TestTable
    ```

    結果集看起來應該如下圖所示：

    :::image type="content" source="media/ingest-data-event-grid/table-result.png" alt-text="事件方格的訊息結果集":::

## <a name="clean-up-resources"></a>清除資源

如果您不打算再次使用您的事件方格，請清除為您自動建立的事件方格訂用帳戶、事件中樞命名空間和事件中樞，以避免產生成本。

1. 在 Azure 入口網站中，移至左側功能表，然後選取 [ **所有資源**]。

    :::image type="content" source="media/ingest-data-event-grid/clean-up-resources-select-all-resource.png" alt-text="選取事件方格清除的所有資源":::    

1. 搜尋您的事件中樞命名空間，然後選取 [ **刪除** ] 以刪除它：

    :::image type="content" source="media/ingest-data-event-grid/clean-up-resources-find-eventhub-namespace-delete.png" alt-text="清除事件中樞命名空間":::

1. 在 [刪除資源] 表單中，確認刪除以刪除事件中樞命名空間和事件中樞資源。

1. 移至您的儲存體帳戶。 在左側功能表中，選取 [ **事件**]：

    :::image type="content" source="media/ingest-data-event-grid/clean-up-resources-select-events.png" alt-text="選取事件方格要清除的事件":::

1. 在圖形下方，選取您的事件方格訂用帳戶，然後選取 [ **刪除** ] 以刪除它：

    :::image type="content" source="media/ingest-data-event-grid/delete-event-grid-subscription.png" alt-text="刪除事件方格訂用帳戶":::

1. 若要刪除您的 Event Grid 資料連線，請移至您的 Azure 資料總管叢集中。 在左側功能表中，選取 [ **資料庫**]。

1. 選取您的資料庫 **>testdatabase**：

    :::image type="content" source="media/ingest-data-event-grid/clean-up-resources-select-database.png" alt-text="選取要清除資源的資料庫":::

1. 在左側功能表中，選取 [ **資料** 內嵌：

    :::image type="content" source="media/ingest-data-event-grid/clean-up-resources-select-data-ingestion.png" alt-text="選取資料內嵌以清除資源":::

1. 選取您的資料連線 *測試格線連接* ，然後選取 [ **刪除** ] 以刪除它。

## <a name="next-steps"></a>後續步驟

* [在 Azure 資料總管中查詢資料](web-query-data.md)
