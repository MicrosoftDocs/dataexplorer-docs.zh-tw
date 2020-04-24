---
title: 使用事件網格訂閱從儲存中引入 - Azure 資料資源管理員 |微軟文件
description: 本文介紹使用 Azure 數據資源管理器中的事件網格訂閱從存儲中引入。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: 682c39b11292c7e198dba46dad9b5bfa3b520c0f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521517"
---
# <a name="ingest-from-storage-using-event-grid-subscription"></a>使用事件網格訂閱從儲存中引入

Azure 資料資源管理員提供 Azure 儲存(Blob 儲存和 ADLSv2)的連續引入,Azure[事件網格](https://docs.microsoft.com/azure/event-grid/overview)訂閱用於 Blob 創建的通知,並透過事件中心將這些通知流式傳輸到 Kusto。

## <a name="data-format"></a>資料格式

* Blob 可以採用任何[支援支援的格式](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats)。
* 讓您可以支援[的壓縮](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats#supported-data-compression-formats)壓縮 Blob

> [!NOTE]
> 理想情況下,原始未壓縮的數據大小應是 blob 元數據的一部分。
> 如果未指定未壓縮的大小,Azure 資料資源管理員將根據檔案大小估計它。 通過將 Blob 中`rawSizeBytes`繼資料上的[屬性](#ingestion-properties)設定為以位元組為單位**的未壓縮**資料大小,提供原始資料大小。
> 請注意,每個檔有 4GB 的引入未壓縮大小限制。

## <a name="ingestion-properties"></a>內嵌屬性

您可以通過 blob[中繼資料指定](https://docs.microsoft.com/azure/data-explorer/ingestion-properties)blob 引入的屬性。
您可以設定下列屬性：

|屬性 | 描述|
|---|---|
| 原始位元組 | 原始(未壓縮)數據的大小。 對於 Avro/ORC/Parquet,這是應用特定於格式的壓縮之前的大小。|
| 庫斯托表 |  現有目標表的名稱。 覆蓋邊欄`Table`選項卡`Data Connection`上的 集。 |
| 庫斯托資料格式 |  資料格式。 覆蓋邊欄`Data format`選項卡`Data Connection`上的 集。 |
| 庫斯圖斯映射參考 |  要使用的現有引入映射的名稱。 覆蓋邊欄`Column mapping`選項卡`Data Connection`上的 集。|
| 函式庫斯托忽略第一記錄 | 如果設定為`true`,Azure 資料資源管理員將忽略 Blob 的第一行。 使用表格格式資料(CSV、TSV 或類似資料)忽略標頭。 |
| 庫斯托範圍標籤 | 表示將附加到結果範圍的[標記](../extents-overview.md#extent-tagging)的字串。 |
| 庫斯托創作時間 |  覆蓋 blob[的$IngestionTime,](../../query/ingestiontimefunction.md?pivots=azuredataexplorer)格式化為 ISO 8601 字串。 用於回填。 |

## <a name="events-routing"></a>事件路由

設置到 Azure 資料資源管理器群集的 Blob 儲存連接時,指定目標表屬性(表名稱、資料格式和映射)。 這是資料的預設路由,也稱為`static routig`。
您還可以使用 blob 中繼資料為每個 Blob 指定目標表屬性。 數據將按[引入屬性](#ingestion-properties)指定進行動態路由。

下面是一個範例,用於在上載 Blob 元數據之前將引入屬性設置為 Blob 元數據。 Blob 路由到不同的表。

有關如何產生資料的詳細資訊,請參考[範例代碼](#generating-data)。

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
> 為獲得最佳性能,請創建與 Azure 資料資源管理器群集相同的區域中的所有資源。

### <a name="prerequisites"></a>Prerequisites

* [建立儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)。 
  事件網格通知訂閱可以在 Azure 儲存帳戶的種類`BlobStorage``StorageV2`或 上設置。 
  支援[資料儲存第 2 代](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)。
* [建立事件中心](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)。

### <a name="event-grid-subscription"></a>事件方格訂用帳戶

* 選擇`Event Hub`Kusto 作為尾點類型,用於傳輸 blob 儲存事件通知。 `Event Grid schema`是通知的選定架構。 請注意,每個偶聯中心可以提供一個連接。
* Blob 儲存訂閱連線處理類型`Microsoft.Storage.BlobCreated`的通知 。 請確保在創建訂閱時選擇它。 請注意,其他類型的通知(如果選中)將被忽略。
* 一個訂閱可以通知一個容器中的存儲事件或更多。 如果要追蹤來自特定容器的檔案,請按如下方式設定通知的篩選器:設定連接時,請特別注意以下值: 
   * **主題以篩選器開頭**是 blob 容器*的文本*首碼。 由於套用的模式是 *startswith*，因此它可以跨越多個容器。 不允許使用萬用字元。
     必須*設定*為: *`/blobServices/default/containers/<prefix>`*。 例如 *:/blob服務/預設/容器/風暴事件-2020-*
   * [主旨結尾為]**** 欄位是 Blob 的*常值*後置詞。 不允許使用萬用字元。 可用於篩選檔副檔名。

可以在[存儲帳戶指南中的「如何創建事件網格訂閱」中找到](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-grid#create-an-event-grid-subscription-in-your-storage-account)詳細的演練。

### <a name="data-ingestion-connection-to-azure-data-explorer"></a>到 Azure 資料資源管理員的資料連線

* 透過 Azure 閘戶:[在 Azure 資料資源管理員中建立事件網格資料連線](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-grid#create-an-event-grid-data-connection-in-azure-data-explorer)。
* 使用 Kusto 管理 .NET SDK:[新增事件網格資料連線](https://docs.microsoft.com/azure/data-explorer/data-connection-event-grid-csharp#add-an-event-grid-data-connection)
* 使用 Kusto 管理 Python SDK:[新增事件網格資料連線](https://docs.microsoft.com/azure/data-explorer/data-connection-event-grid-python#add-an-event-grid-data-connection)
* 使用 ARM 樣本:[用於新增事件網格資料連線的 Azure 資源管理員樣本](https://docs.microsoft.com/azure/data-explorer/data-connection-event-grid-resource-manager#azure-resource-manager-template-for-adding-an-event-grid-data-connection)

### <a name="generating-data"></a>組建資料

> [!NOTE]
> * 用於`BlockBlob`生成數據。 不支援 `AppendBlob`。
<!--> * Using ADLSv2 storage requires using `CreateFile` API for uploading blobs and flush at the end. 
    Kusto will get 2 notificatiopns: when blob is created and when stream is flushed. First notification arrives before the data is ready and ignored. Second notification is processed.-->

下面是從本地檔案建立 Blob、將引入屬性設定為 blob 中繼資料並上傳它的範例:

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

Azure 資料資源管理器不會在引入后刪除 blob,但會將其保留三到五天。 使用[Azure Blob 儲存生命週期](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal)來管理 Blob 刪除。