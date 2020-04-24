---
title: 將 Azure Blob 擷取至 Azure 資料總管
description: 在本文中,您將瞭解如何使用事件網格訂閱向 Azure 資料資源管理器發送存儲帳戶數據。
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: ba7305eca81ed405f2a577c4a994133509429564
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81500511"
---
# <a name="ingest-blobs-into-azure-data-explorer-by-subscribing-to-event-grid-notifications"></a>訂閱 Event Grid 通知，以便將 Blob 擷取至 Azure 資料總管

> [!div class="op_single_selector"]
> * [入口網站](ingest-data-event-grid.md)
> * [C#](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Azure Resource Manager 範本](data-connection-event-grid-resource-manager.md)

Azure 資料總管是一項快速又可調整的資料探索服務，可用於處理記錄和遙測資料。 它會從寫入至 Blob 容器的 Blob 提供連續擷取 (資料載入)。 

在本文中,您將瞭解如何設置[Azure 事件網格](/azure/event-grid/overview)訂閱,以及通過事件中心將事件路由到 Azure 資料資源管理器。 首先，您應該具備含有事件格線訂用帳戶的儲存體帳戶，此帳戶會將通知傳送到 Azure 事件中樞。 然後，您可以建立 Event Grid 資料連線，並查看整個系統的資料流程。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 建立[免費的 Azure 帳號](https://azure.microsoft.com/free/)。
* [叢集與資料庫](create-cluster-database-portal.md)。
* [儲存帳號](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)。
* [事件中樞](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)。

## <a name="create-an-event-grid-subscription-in-your-storage-account"></a>在儲存體帳戶中建立 Event Grid 訂用帳戶

1. 在 Azure 入口網站中，尋找您的儲存體帳戶。
1. 選擇**事件** > **事件訂閱**。

    ![查詢應用程式連結](media/ingest-data-event-grid/create-event-grid-subscription.png)

1. 在 [建立事件訂用帳戶]**** 視窗的 [基本]**** 索引標籤內，提供下列值：

    **設定** | **建議的值** | **欄位描述**
    |---|---|---|
    | 名稱 | *test-grid-connection* | 您想要建立之事件格線的名稱。|
    | 事件結構描述 | *事件格線架構* | 應該用於事件格線的結構描述。 |
    | 主題類型 | *儲存體帳戶* | 事件格線主題的類型。 |
    | 主題資源 | *gridteststorage* | 儲存體帳戶的名稱。 |
    | 訂閱所有事件類型 | *清楚* | 不取得所有事件的通知。 |
    | 定義的事件類型 | *已建立 Blob* | 要取得通知的特定事件。 |
    | 端點類型 | *事件中心* | 要接收事件之端點的類型。 |
    | 端點 | *test-hub* | 您建立的事件中樞。 |
    | | |

1. 如果要追蹤特定容器中的檔案,請選擇 **「篩選**」選項卡。 設定通知的篩選條件，如下所示：
    * [主旨開頭為]**** 欄位是 Blob 容器的*常值*前置詞。 由於套用的模式是 *startswith*，因此它可以跨越多個容器。 不允許使用萬用字元。
     它*必須*設定如下：*`/blobServices/default/containers/`*[容器前置詞]
    * [主旨結尾為]**** 欄位是 Blob 的*常值*後置詞。 不允許使用萬用字元。

## <a name="create-a-target-table-in-azure-data-explorer"></a>在 Azure 資料總管中建立目標資料表

在 Azure 資料總管中建立一個資料表，供事件中樞將資料傳送至此。 在必要條件中準備的叢集與資料庫內建立該資料表。

1. 在 Azure 入口網站中，您的叢集下方，選取 [查詢]****。

    ![查詢應用程式連結](media/ingest-data-event-grid/query-explorer-link.png)

1. 將下列命令複製到視窗，然後選取 [執行]**** 以建立資料表 (TestTable)，該資料表會接收內嵌的資料。

    ```kusto
    .create table TestTable (TimeStamp: datetime, Value: string, Source:string)
    ```

    ![執行建立查詢](media/ingest-data-event-grid/run-create-table.png)

1. 將下列命令複製到視窗中，然後選取 [執行]**** 以將傳入的 JSON 資料對應至資料表 (TestTable) 的資料行名稱與資料類型。

    ```kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp","path":"$.TimeStamp"},{"column":"Value","path":"$.Value"},{"column":"Source","path":"$.Source"}]'
    ```

## <a name="create-an-event-grid-data-connection-in-azure-data-explorer"></a>在 Azure 資料總管中建立 Event Grid 資料連線

現在從 Azure 資料資源管理器連接到事件網格,以便流入 Blob 容器的數據流式傳輸到測試表。 

1. 選取工具列上的 [通知]****，以確認事件中樞部署已成功。

1. 在建立的叢集下,選擇**資料庫** > **測試資料庫**。

    ![選取測試資料庫](media/ingest-data-event-grid/select-test-database.png)

1. 選擇 **「資料引入** > **添加資料連接**」 。

    ![資料擷取](media/ingest-data-event-grid/data-ingestion-create.png)

1.  選擇連線型態 **:Blob 儲存**。

1. 在表單中填寫下列資訊，並選取 [建立]****。

    ![事件中樞連線](media/ingest-data-event-grid/create-event-grid-data-connection.png)

     資料來源：

    **設定** | **建議的值** | **欄位描述**
    |---|---|---|
    | 資料連線名稱 | *test-hub-connection* | 您想要在 Azure 資料總管中建立的連線名稱。|
    | 儲存體帳戶訂用帳戶 | 訂用帳戶識別碼 | 您儲存體帳戶所在的訂用帳戶識別碼。|
    | 儲存體帳戶 | *gridteststorage* | 您先前建立之儲存體帳戶的名稱。|
    | Event Grid | *test-grid-connection* | 您建立之事件格線的名稱。 |
    | 事件中樞名稱 | *test-hub* | 您建立的事件中樞。 此欄位會在您挑選事件格線時自動填入。 |
    | 取用者群組 | *test-group* | 在您所建立事件中樞中定義的取用者群組。 |
    | | |

    目標資料表：

     **設定** | **建議的值** | **欄位描述**
    |---|---|---|
    | Table | *TestTable* | 您在 **TestDatabase** 中建立的資料表。 |
    | 資料格式 | *JSON* | 支援的格式包括 Avro、CSV、JSON、多林傑森、PSV、SOH、SCSV、TSV、RAW 和 TXT。 支援的壓縮選項:Zip 和 GZip |
    | 資料行對應 | *TestMapping* | 您在 **TestDatabase** 中建立的對應，會將傳入的 JSON 資料對應至 **TestTable** 的資料行名稱與資料類型。|
    | | |
    
## <a name="generate-sample-data"></a>產生範例資料

既然 Azure 資料檔案總管及儲存體帳戶已經連線，您可以建立範例資料並上傳至 Blob 儲存體。

我們將使用可發出一些基本 Azure CLI 命令的小型殼層指令碼，來與 Azure 儲存體資源互動。 此指令碼會在儲存體帳戶中建立新的容器，將現有的檔案 (以 Blob 形式) 上傳至該容器，然後列出容器中的 Blob。 您可以使用 [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)，直接在入口網站中執行指令碼。

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
> 為了實現最佳的攝入性能,必須傳達提交用於攝入的壓縮 blob 的*未壓縮*大小。 由於事件網格通知僅包含基本詳細資訊,因此必須顯式傳達大小資訊。 可以通過在 blob 中`rawSizeBytes`繼資料上設定 屬性,以位元組為單位設定未壓縮的資料大小,從而提供*未壓縮*的大小資訊。

### <a name="ingestion-properties"></a>內嵌屬性

您可以透過 blob 中繼資料指定 blob 引入[的屬性](ingestion-properties.md)。

可以設定以下屬性:

|**屬性** | **屬性描述**|
|---|---|
| `rawSizeBytes` | 原始(未壓縮)數據的大小。 對於 Avro/ORC/Parquet,這是應用特定於格式的壓縮之前的大小。|
| `kustoTable` |  現有目標表的名稱。 覆蓋邊欄`Table`選項卡`Data Connection`上的 集。 |
| `kustoDataFormat` |  資料格式。 覆蓋邊欄`Data format`選項卡`Data Connection`上的 集。 |
| `kustoIngestionMappingReference` |  要使用的現有引入映射的名稱。 覆蓋邊欄`Column mapping`選項卡`Data Connection`上的 集。|
| `kustoIgnoreFirstRecord` | 如果設定為`true`,Kusto 將忽略 blob 的第一行。 使用表格格式資料(CSV、TSV 或類似資料)忽略標頭。 |
| `kustoExtentTags` | 表示將附加到結果範圍的[標記](kusto/management/extents-overview.md#extent-tagging)的字串。 |
| `kustoCreationTime` |  覆蓋 blob[的$IngestionTime,](kusto/query/ingestiontimefunction.md?pivots=azuredataexplorer)格式化為 ISO 8601 字串。 用於回填。 |

> [!NOTE]
> Azure 資料資源管理員不會刪除引入後的 Blob。
> 將 blob 保留五天。
> 使用[Azure Blob 儲存生命週期](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal)管理 Blob 刪除。 

## <a name="review-the-data-flow"></a>檢閱資料流程

> [!NOTE]
> Azure 資料總管具有資料擷取的彙總 (批次處理) 原則，可將擷取程序最佳化。
根據預設，此原則設定為 5 分鐘。
如果需要,您可以稍後更改策略。 在本文中,您可以預計延遲幾分鐘。

1. 當應用程式正在執行時，在 Azure 入口網站內事件格線的下方，您會看見活動爆增。

    ![事件格線圖表](media/ingest-data-event-grid/event-grid-graph.png)

1. 若要檢查目前為止已有多少則訊息成功進入資料庫，請在測試資料庫中執行下列查詢。

    ```kusto
    TestTable
    | count
    ```

1. 若要查看訊息的內容，請在測試資料庫中執行下列查詢。

    ```kusto
    TestTable
    ```

    結果集應會如下所示。

    ![訊息結果集](media/ingest-data-event-grid/table-result.png)

## <a name="clean-up-resources"></a>清除資源

如果您不打算再次使用您的事件格線，請清除 **test-hub-rg** 以避免產生成本。

1. 在 Azure 入口網站中選取靠左側的 [資源群組]****，然後選取您所建立的群組。  

    如果左側功能表已摺疊，請選取 ![[展開] 按鈕](media/ingest-data-event-grid/expand.png) 加以展開。

   ![選取要刪除的資源群組](media/ingest-data-event-grid/delete-resources-select.png)

1. 在 [test-resource-group]**** 下方，選取 [刪除資源群組]****。

1. 在新視窗中，輸入要刪除的資源群組名稱 (*test-hub-rg*)，然後選取 [刪除]****。

## <a name="next-steps"></a>後續步驟

* [Azure 資料資源管理員中的查詢資料](web-query-data.md)
