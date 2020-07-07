---
title: 使用事件方格訂用帳戶從儲存體內嵌-Azure 資料總管
description: 本文說明如何在 Azure 資料總管中使用事件方格訂用帳戶從儲存體內嵌。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/01/2020
ms.openlocfilehash: 3a69add7e395bbb5b18c390c4089a2e8ad80f674
ms.sourcegitcommit: 0d15903613ad6466d49888ea4dff7bab32dc5b23
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/06/2020
ms.locfileid: "86013782"
---
# <a name="ingest-from-storage-using-event-grid-subscription"></a>使用事件方格訂用帳戶從儲存體內嵌

Azure 資料總管使用[Azure Event Grid](/azure/event-grid/overview)訂用帳戶，從 Azure 儲存體（blob 儲存體和 ADLSv2）提供持續的內嵌，用於建立 Blob 的通知，並透過事件中樞將這些通知串流至 azure 資料總管。

## <a name="data-format"></a>資料格式

* Blob 可以是任何一種[支援的格式](../../../ingestion-supported-formats.md)。
* 可以壓縮 blob。 如需詳細資訊，請參閱[支援的壓縮](../../../ingestion-supported-formats.md#supported-data-compression-formats)。

> [!NOTE]
> 理想的情況是，原始未壓縮的資料大小應該是 blob 中繼資料的一部分。
> 如果未指定未壓縮的大小，Azure 資料總管會根據檔案大小來估計它。 將 `rawSizeBytes` blob 中繼資料上的[屬性](#ingestion-properties)設定為未壓縮的資料大小（以位元組為單位），即可提供原始資料大小。
> 
> 每個檔案 4 GB 有一個內嵌的未壓縮大小限制。

## <a name="ingestion-properties"></a>內嵌屬性

您可以指定透過 blob 中繼資料內嵌之 blob 的內嵌[屬性](../../../ingestion-properties.md)。
您可以設定下列屬性：

|屬性 | 描述|
|---|---|
| rawSizeBytes | 原始（未壓縮）資料的大小。 針對 Avro/ORC/Parquet，此值是套用格式特定壓縮之前的大小。|
| kustoTable |  現有目標資料表的名稱。 覆寫分頁 `Table` 上的集合 `Data Connection` 。 |
| kustoDataFormat |  資料格式。 覆寫 [**資料連線**] 分頁上設定的**資料格式**。 |
| kustoIngestionMappingReference |  要使用之現有內嵌對應的名稱。 覆寫在 [**資料連線**] 分頁上設定的資料**行對應**。|
| kustoIgnoreFirstRecord | 如果設定為 `true` ，Azure 資料總管會忽略 blob 的第一個資料列。 使用表格式格式資料（CSV、TSV 或類似）來忽略標頭。 |
| kustoExtentTags | 代表將附加至結果範圍之[標記](../extents-overview.md#extent-tagging)的字串。 |
| kustoCreationTime |  覆寫 blob 的[$IngestionTime](../../query/ingestiontimefunction.md?pivots=azuredataexplorer) ，格式為 ISO 8601 字串。 用於回填。 |

## <a name="events-routing"></a>事件路由

設定 Azure 資料總管叢集的 blob 儲存體連線時，請指定目標資料表屬性：
* 資料表名稱
* 資料格式
* 對應

此設定是您資料的預設路由，有時稱為 `static routing` 。
您也可以使用 blob 中繼資料，為每個 blob 指定目標資料表屬性。 資料會以內嵌[屬性](#ingestion-properties)指定的方式動態路由傳送。

以下範例示範如何在上傳屬性之前，先設定 blob 中繼資料。 Blob 會路由傳送至不同的資料表。

如需如何產生資料的詳細資訊，請參閱[範例程式碼](#generating-data)。

```csharp
// Blob is dynamically routed to table `Events`, ingested using `EventsMapping` data mapping
blob = container.GetBlockBlobReference(blobName2);
blob.Metadata.Add("rawSizeBytes", "4096‬"); // the uncompressed size is 4096 bytes
blob.Metadata.Add("kustoTable", "Events");
blob.Metadata.Add("kustoDataFormat", "json");
blob.Metadata.Add("kustoIngestionMappingReference", "EventsMapping");
blob.UploadFromFile(jsonCompressedLocalFileName);
```

## <a name="create-an-event-grid-subscription-in-your-storage-account"></a>在儲存體帳戶中建立 Event Grid 訂用帳戶

> [!NOTE]
> 為了達到最佳效能，請在 Azure 資料總管叢集所在的相同區域中建立所有資源。

### <a name="prerequisites"></a>必要條件

* [建立儲存體帳戶](/azure/storage/common/storage-quickstart-create-account)。
  事件方格通知訂閱可以在 Azure 儲存體帳戶上設定種類 `BlobStorage` 或 `StorageV2` 。
  也支援啟用[Data Lake Storage Gen2](/azure/storage/blobs/data-lake-storage-introduction) 。
* [建立事件中樞](/azure/event-hubs/event-hubs-create)。

### <a name="event-grid-subscription"></a>事件方格訂用帳戶
 
1. 在 Azure 入口網站中，尋找您的儲存體帳戶。
1. 在左側功能表上，選取 [**事件**] [  >  **事件訂**用帳戶]。

     :::image type="content" source="../images/eventgrid/create-event-grid-subscription-1.png" alt-text="建立事件方格訂用帳戶":::

1. 在 [建立事件訂用帳戶]**** 視窗的 [基本]**** 索引標籤內，提供下列值：

    :::image type="content" source="../images/eventgrid/create-event-grid-subscription-2.png" alt-text="建立要輸入的事件訂用帳戶值":::

    |**設定** | **建議的值** | **欄位描述**|
    |---|---|---|
    | 名稱 | *test-grid-connection* | 您想要建立之事件方格訂用帳戶的名稱。|
    | 事件結構描述 | *事件方格架構* | 應該用於事件格線的結構描述。 |
    | 主題類型 | *儲存體帳戶* | 事件格線主題的類型。 |
    | 來源資源 | *gridteststorage1* | 儲存體帳戶的名稱。 |
    | 系統主題名稱 | *gridteststorage1...* | Azure 儲存體發佈事件的系統主題。 此系統主題接著會將事件轉送至接收和處理事件的訂閱者。 |
    | 篩選事件種類 | *建立的 Blob* | 要取得通知的特定事件。 目前支援的類型為 Microsoft.storage.blobcreated。 建立訂用帳戶時，請務必選取它。|
    | 端點類型 | *事件中樞* | 要接收事件之端點的類型。 |
    | 端點 | *test-hub* | 您建立的事件中樞。 |

1. 如果您想要追蹤特定的主題，請選取 [**篩選**] 索引標籤。 設定通知的篩選條件，如下所示：
   * 選取 [啟用主旨篩選]****
   * [主旨**開頭為**] 欄位是主旨的*常*值前置詞。 因為套用的模式是*startswith*，所以它可以跨越多個容器、資料夾或 blob。 不允許使用萬用字元。
       * 若要在 blob 容器上定義篩選，欄位*必須*設定如下： *`/blobServices/default/containers/[container prefix]`* 。
       * 若要定義 blob 前置詞的篩選（或 Azure Data Lake Gen2 中的資料夾），欄位*必須*設定如下： *`/blobServices/default/containers/[container name]/blobs/[folder/blob prefix]`* 。
   * [主旨結尾為]**** 欄位是 Blob 的*常值*後置詞。 不允許使用萬用字元。
   * 區分**大小寫**的主旨符合欄位指出前置詞和尾碼篩選是否區分大小寫。
   * 如需篩選事件的詳細資訊，請參閱[Blob 儲存體事件](/azure/storage/blobs/storage-blob-event-overview#filtering-events)。
    
        :::image type="content" source="../images/eventgrid/filters-tab.png" alt-text="[篩選] 索引標籤事件方格":::

### <a name="data-ingestion-connection-to-azure-data-explorer"></a>Azure 資料總管的資料內嵌連接

* 透過 Azure 入口網站：[在 Azure 資料總管中建立事件方格資料連線](../../../ingest-data-event-grid.md#create-an-event-grid-data-connection-in-azure-data-explorer)。
* 使用 Kusto management .NET SDK：[新增事件方格資料連線](../../../data-connection-event-grid-csharp.md#add-an-event-grid-data-connection)
* 使用 Kusto 管理 Python SDK：[新增事件方格資料連線](../../../data-connection-event-grid-python.md#add-an-event-grid-data-connection)
* 使用[Azure Resource Manager 範本來新增事件方格資料連線](../../../data-connection-event-grid-resource-manager.md#azure-resource-manager-template-for-adding-an-event-grid-data-connection)

### <a name="generating-data"></a>產生資料

> [!NOTE]
> * 使用 `BlockBlob` 來產生資料。 不支援 `AppendBlob`。

以下範例會從本機檔案建立 blob、將內嵌屬性設定為 blob 中繼資料，並將其上傳：

 ```csharp
 var azureStorageAccountConnectionString=<storage_account_connection_string>;

var containerName=<container_name>;
var blobName=<blob_name>;
var localFileName=<file_to_upload>;

// Creating the container
var azureStorageAccount = CloudStorageAccount.Parse(azureStorageAccountConnectionString);
var blobClient = azureStorageAccount.CreateCloudBlobClient();
var container = blobClient.GetContainerReference(containerName);
container.CreateIfNotExists();

// Set ingestion properties in blob's metadata & uploading the blob
var blob = container.GetBlockBlobReference(blobName);
blob.Metadata.Add("rawSizeBytes", "4096‬"); // the uncompressed size is 4096 bytes
blob.Metadata.Add("kustoIgnoreFirstRecord", "true"); // First line of this csv file are headers
blob.UploadFromFile(csvCompressedLocalFileName);
```

> [!NOTE]
> 使用 Azure Data Lake Gen2 儲存體 SDK 需要使用 `CreateFile` 來上傳檔案，並 `Flush` 在結尾處，將 close 參數設為 "true"。
> 如需如何正確使用 Data Lake Gen2 SDK 的詳細範例，請參閱[使用 AZURE DATA LAKE SDK 上傳](../../../data-connection-event-grid-csharp.md#upload-file-using-azure-data-lake-sdk)檔案。

## <a name="blob-lifecycle"></a>Blob 生命週期

Azure 資料總管不會在內嵌之後刪除 blob。 使用[Azure blob 儲存體生命週期](/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal)來管理您的 Blob 刪除。 建議將 blob 保留三到五天。

## <a name="known-issues"></a>已知問題

* 使用 Azure 資料總管[匯出](../data-export/export-data-to-storage.md)事件方格內嵌所使用的檔案時，請注意： 
    * 如果提供給匯出命令的連接字串或提供給[外部資料表](../data-export/export-data-to-an-external-table.md)的連接字串是[ADLS Gen2 格式](../../api/connection-strings/storage.md#azure-data-lake-store)的連接字串（例如 `abfss://filesystem@accountname.dfs.core.windows.net` ），但未針對階層式命名空間啟用儲存體帳戶，則不會觸發事件方格通知。 
    * 如果未啟用階層命名空間的帳戶，連接字串必須使用[Blob 儲存體](../../api/connection-strings/storage.md#azure-storage-blob)格式（例如 `https://accountname.blob.core.windows.net` ）。 即使使用 ADLS Gen2 連接字串，匯出仍會如預期般運作，但不會觸發通知，事件方格內嵌也無法運作。
