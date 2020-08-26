---
title: 使用事件方格訂用帳戶從儲存體內嵌-Azure 資料總管
description: 本文說明如何在 Azure 資料總管中使用事件方格訂用帳戶從儲存體內嵌。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 08/13/2020
ms.openlocfilehash: 5369f3166e3700740be2a30927da793a8d21e05c
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88874897"
---
# <a name="connect-to-event-grid"></a>連線至事件方格

事件方格是一種可接聽 Azure 儲存體的管線，並會更新 Azure 資料總管，以便在訂閱的事件發生時提取資訊。 Azure 資料總管提供 Azure 儲存體 (Blob 儲存體的連續內嵌，) 以及使用 [Azure Event Grid](/azure/event-grid/overview) 訂用帳戶的 Azure 事件方格訂用帳戶來建立通知，並透過事件中樞將這些通知串流至 azure 資料總管。

事件方格內嵌管線會經歷數個步驟。 您要在 Azure 資料總管中建立 [特定格式的資料](#data-format) 將會內嵌的目標資料表和。 然後，您會在 Azure 資料總管中建立事件方格資料連線。 事件方格資料連線需要知道 [事件路由](#set-events-routing) 資訊，例如要將資料傳送到哪個資料表和資料表對應。 您也可以指定內嵌 [屬性](#set-ingestion-properties)，以描述要內嵌的資料、目標資料表及對應。 您可以透過 [Azure 入口網站](ingest-data-event-grid.md)、以 [c #](data-connection-event-grid-csharp.md) 或 [Python](data-connection-event-grid-python.md)程式設計方式，或使用 [Azure Resource Manager 範本](data-connection-event-grid-resource-manager.md)來管理此進程。

## <a name="data-format"></a>資料格式

* 請參閱 [支援的格式](ingestion-supported-formats.md)。
* 請參閱 [支援的壓縮](ingestion-supported-formats.md#supported-data-compression-formats)。
  * 原始未壓縮的資料大小應該是 blob 中繼資料的一部分，否則 Azure 資料總管將會加以評估。  每個檔案的內嵌未壓縮大小限制為 4 GB。
 
## <a name="set-ingestion-properties"></a>設定內嵌屬性

您可以透過 blob 中繼資料指定 blob 內嵌的內嵌 [屬性](ingestion-properties.md) 。
您可以設定下列屬性：

[!INCLUDE [ingestion-properties-event-grid](includes/ingestion-properties-event-grid.md)]

## <a name="set-events-routing"></a>設定事件路由

設定 Azure 資料總管叢集的 blob 儲存體連線時，請指定目標資料表屬性：
* 資料表名稱
* 資料格式
* 對應

這項設定是您資料的預設路由，有時也稱為 `static routing` 。
您也可以使用 blob 中繼資料，為每個 blob 指定目標資料表屬性。 資料會動態路由，如內嵌 [屬性](#set-ingestion-properties)所指定。

下列範例示範如何在上傳 blob 中繼資料之前，先設定這些屬性。 Blob 會路由傳送至不同的資料表。

如需詳細資訊，請參閱 [產生資料](#generate-data)。

```csharp
// Blob is dynamically routed to table `Events`, ingested using `EventsMapping` data mapping
blob = container.GetBlockBlobReference(blobName2);
blob.Metadata.Add("rawSizeBytes", "4096‬"); // the uncompressed size is 4096 bytes
blob.Metadata.Add("kustoTable", "Events");
blob.Metadata.Add("kustoDataFormat", "json");
blob.Metadata.Add("kustoIngestionMappingReference", "EventsMapping");
blob.UploadFromFile(jsonCompressedLocalFileName);
```

### <a name="generate-data"></a>產生資料

> [!NOTE]
> 用 `BlockBlob` 來產生資料。 不支援 `AppendBlob`。

您可以從本機檔案建立 blob、將內嵌屬性設定為 blob 中繼資料，然後將它上傳，如下所示：

 ```csharp
 var azureStorageAccountConnectionString=<storage_account_connection_string>;

var containerName=<container_name>;
var blobName=<blob_name>;
var localFileName=<file_to_upload>;

// Create the container
var azureStorageAccount = CloudStorageAccount.Parse(azureStorageAccountConnectionString);
var blobClient = azureStorageAccount.CreateCloudBlobClient();
var container = blobClient.GetContainerReference(containerName);
container.CreateIfNotExists();

// Set ingestion properties in blob metadata and upload the blob
var blob = container.GetBlockBlobReference(blobName);
blob.Metadata.Add("rawSizeBytes", "4096‬"); // the uncompressed size is 4096 bytes
blob.Metadata.Add("kustoIgnoreFirstRecord", "true"); // First line of this csv file are headers
blob.UploadFromFile(csvCompressedLocalFileName);
```

> [!NOTE]
> 使用 Azure Data Lake Gen2 storage SDK 需要使用 `CreateFile` 來上傳檔案，並 `Flush` 在結尾參數設定為 "true" 的結尾。
> 如需 Data Lake Gen2 SDK 正確使用方式的詳細範例，請參閱 [使用 AZURE DATA LAKE SDK 上傳](data-connection-event-grid-csharp.md#upload-file-using-azure-data-lake-sdk)檔案。

## <a name="delete-blobs-using-storage-lifecycle"></a>使用儲存體生命週期刪除 blob

Azure 資料總管不會在內嵌之後刪除 blob。 使用 [Azure blob 儲存體生命週期](/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal) 來管理您的 Blob 刪除。 建議將 blob 保留三至五天。

## <a name="known-event-grid-issues"></a>已知的事件方格問題

* 當您使用 Azure 資料總管 [匯出](kusto/management/data-export/export-data-to-storage.md) 用於事件方格內嵌的檔案時，請注意： 
    * 如果提供給匯出命令的連接字串或提供給 [外部資料表](kusto/management/data-export/export-data-to-an-external-table.md) 的連接字串是 [ADLS Gen2 格式](kusto/api/connection-strings/storage.md#azure-data-lake-store) 的連接字串 (例如 `abfss://filesystem@accountname.dfs.core.windows.net`) 但未針對階層命名空間啟用儲存體帳戶，就不會觸發事件方格通知。 
    * 如果未啟用階層命名空間的帳戶，則連接字串必須使用 [Blob](kusto/api/connection-strings/storage.md#azure-storage-blob) 儲存格式 (例如 `https://accountname.blob.core.windows.net`) 。 即使使用 ADLS Gen2 連接字串，匯出也會如預期般運作，但不會觸發通知，且事件方格內嵌將無法運作。

## <a name="next-steps"></a>後續步驟

* [訂閱 Event Grid 通知，以便將 Blob 擷取至 Azure 資料總管](ingest-data-event-grid.md)
* [使用 C 建立 Azure 資料總管的事件中樞資料連線#](data-connection-event-hub-csharp.md)
* [使用 Python 建立 Azure 資料總管的事件方格資料連線](data-connection-event-grid-python.md)
* [使用 Azure Resource Manager 範本建立 Azure 資料總管的事件方格資料連線](data-connection-event-grid-resource-manager.md)
