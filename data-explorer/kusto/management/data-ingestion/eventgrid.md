---
title: 使用事件方格訂用帳戶從儲存體內嵌-Azure 資料總管 |Microsoft Docs
description: 本文說明如何在 Azure 資料總管中使用事件方格訂用帳戶從儲存體內嵌。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: 43012752889d534d8f74943cfa6c528b2c72cd8b
ms.sourcegitcommit: 8e097319ea989661e1958efaa1586459d2b69292
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/15/2020
ms.locfileid: "84780638"
---
# <a name="ingest-from-storage-using-event-grid-subscription"></a>使用事件方格訂用帳戶從儲存體內嵌

Azure 資料總管使用[Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview)訂用帳戶，從 Azure 儲存體（blob 儲存體和 ADLSv2）提供持續的內嵌，用於建立 Blob 的通知，並透過事件中樞將這些通知串流至 Kusto。

## <a name="data-format"></a>資料格式

* Blob 可以是任何一種[支援的格式](../../../ingestion-supported-formats.md)。
* 可以在任何[支援的壓縮](../../../ingestion-supported-formats.md#supported-data-compression-formats)中壓縮 blob

> [!NOTE]
> 理想的情況是，原始未壓縮的資料大小應該是 blob 中繼資料的一部分。
> 如果未指定未壓縮的大小，Azure 資料總管會根據檔案大小來評估。 將 `rawSizeBytes` blob 中繼資料上的[屬性](#ingestion-properties)設定為**未壓縮**的資料大小（以位元組為單位），以提供原始資料大小。
> 請注意，每個檔案4GB 會有一個內嵌的未壓縮大小限制。

## <a name="ingestion-properties"></a>內嵌屬性

您可以指定透過 blob 中繼資料內嵌之 blob 的內嵌[屬性](../../../ingestion-properties.md)。
您可以設定下列屬性：

|屬性 | 描述|
|---|---|
| rawSizeBytes | 原始（未壓縮）資料的大小。 針對 Avro/ORC/Parquet，此值是套用格式特定壓縮之前的大小。|
| kustoTable |  現有目標資料表的名稱。 覆寫分頁 `Table` 上的集合 `Data Connection` 。 |
| kustoDataFormat |  資料格式。 覆寫分頁 `Data format` 上的集合 `Data Connection` 。 |
| kustoIngestionMappingReference |  要使用之現有內嵌對應的名稱。 覆寫分頁 `Column mapping` 上的集合 `Data Connection` 。|
| kustoIgnoreFirstRecord | 如果設定為 `true` ，Azure 資料總管會忽略 blob 的第一個資料列。 使用表格式格式資料（CSV、TSV 或類似）來忽略標頭。 |
| kustoExtentTags | 代表將附加至結果範圍之[標記](../extents-overview.md#extent-tagging)的字串。 |
| kustoCreationTime |  覆寫 blob 的[$IngestionTime](../../query/ingestiontimefunction.md?pivots=azuredataexplorer) ，格式為 ISO 8601 字串。 用於回填。 |

## <a name="events-routing"></a>事件路由

設定 Azure 資料總管叢集的 blob 儲存體連線時，請指定目標資料表屬性（資料表名稱、資料格式和對應）。 此設定是您資料的預設路由，也稱為 `static routing` 。
您也可以使用 blob 中繼資料，為每個 blob 指定目標資料表屬性。 資料將會依內嵌[屬性](#ingestion-properties)的指定動態路由傳送。

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

## <a name="create-event-grid-subscription"></a>建立事件方格訂用帳戶

> [!Note]
> 為了達到最佳效能，請在 Azure 資料總管叢集所在的相同區域中建立所有資源。

### <a name="prerequisites"></a>必要條件

* [建立儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)。 
  事件方格通知訂閱可以在或類型的 Azure 儲存體帳戶上 `BlobStorage` 設定 `StorageV2` 。 
  也支援啟用[Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction) 。
* [建立事件中樞](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)。

### <a name="event-grid-subscription"></a>事件方格訂用帳戶

* Kusto 選取 `Event Hub` 為端點類型，用於傳輸 blob 儲存體事件通知。 `Event Grid schema`這是所選取的通知架構。 每個偶數中樞都可以服務一個連接。
* Blob 儲存體訂閱連接會處理類型的通知 `Microsoft.Storage.BlobCreated` 。 建立訂用帳戶時，請務必選取它。 若已選取，則會忽略其他類型的通知。
* 一個訂用帳戶可以在一個或多個容器中通知儲存體事件。 如果您想要追蹤特定容器中的檔案，請設定通知的篩選，如下所示：設定連接時，請特別注意下列值： 
   * **Subject 開頭為**filter 是 blob 容器的*常*值前置詞。 由於套用的模式是 *startswith*，因此它可以跨越多個容器。 不允許使用萬用字元。
     *必須*設定如下： *`/blobServices/default/containers/<prefix>`* 。 例如： */blobServices/default/containers/StormEvents-2020-*
   * [主旨結尾為]**** 欄位是 Blob 的*常值*後置詞。 不允許使用萬用字元。 適合用來篩選副檔名。

如需詳細的逐步解說，請參閱如何在您的[儲存體帳戶中建立事件方格訂用帳戶](../../../ingest-data-event-grid.md#create-an-event-grid-subscription-in-your-storage-account)指南。

### <a name="data-ingestion-connection-to-azure-data-explorer"></a>Azure 資料總管的資料內嵌連接

* 透過 Azure 入口網站：[在 Azure 資料總管中建立事件方格資料連線](../../../ingest-data-event-grid.md#create-an-event-grid-data-connection-in-azure-data-explorer)。
* 使用 Kusto management .NET SDK：[新增事件方格資料連線](../../../data-connection-event-grid-csharp.md#add-an-event-grid-data-connection)
* 使用 Kusto 管理 Python SDK：[新增事件方格資料連線](../../../data-connection-event-grid-python.md#add-an-event-grid-data-connection)
* 使用 ARM 範本：[用於新增事件方格資料連線的 Azure Resource Manager 範本](../../../data-connection-event-grid-resource-manager.md#azure-resource-manager-template-for-adding-an-event-grid-data-connection)

### <a name="generating-data"></a>產生資料

> [!NOTE]
> * 使用 `BlockBlob` 來產生資料。 不支援 `AppendBlob`。
<!--> * Using ADLSv2 storage requires using `CreateFile` API for uploading blobs and flush at the end. 
    Kusto will get 2 notificatiopns: when blob is created and when stream is flushed. First notification arrives before the data is ready and ignored. Second notification is processed.-->

以下是從本機檔案建立 blob、設定將屬性內嵌到 blob 中繼資料並將其上傳的範例：

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

## <a name="blob-lifecycle"></a>Blob 生命週期

Azure 資料總管不會在內嵌後刪除 blob。 使用[Azure blob 儲存體生命週期](/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal)來管理您的 Blob 刪除。 建議將 blob 保留三到五天。

## <a name="known-issues"></a>已知問題

使用 Azure 資料總管[匯出](../data-export/export-data-to-storage.md)事件方格內嵌所使用的檔案時，請注意： 
* 如果提供給匯出命令的連接字串或提供給[外部資料表](../data-export/export-data-to-an-external-table.md)的連接字串是[ADLS Gen2 格式](../../api/connection-strings/storage.md#azure-data-lake-store)的連接字串（例如 `abfss://filesystem@accountname.dfs.core.windows.net` ），但未針對階層式命名空間啟用儲存體帳戶，則不會觸發事件方格通知。 
* 如果未啟用階層命名空間的帳戶，連接字串必須使用[Blob 儲存體](../../api/connection-strings/storage.md#azure-storage-blob)格式（例如 `https://accountname.blob.core.windows.net` ）。 即使使用 ADLS Gen2 連接字串，匯出仍會如預期般運作，但不會觸發通知，事件方格內嵌也無法運作。
