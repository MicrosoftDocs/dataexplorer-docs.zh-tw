---
title: 使用 C 建立 Azure 資料總管的事件方格資料連線#
description: '在本文中，您將瞭解如何使用 c # 建立 Azure 資料總管的事件方格資料連線。'
author: orspod
ms.author: orspodek
ms.reviewer: lugoldbe
ms.service: data-explorer
ms.topic: how-to
ms.date: 10/07/2019
ms.openlocfilehash: 5ba4f61d051a89d0fd3851f3e5be4f344ea79e0b
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88874132"
---
# <a name="create-an-event-grid-data-connection-for-azure-data-explorer-by-using-c"></a>使用 C 建立 Azure 資料總管的事件方格資料連線#

> [!div class="op_single_selector"]
> * [入口網站](ingest-data-event-grid.md)
> * [C#](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Azure Resource Manager 範本](data-connection-event-grid-resource-manager.md)


[!INCLUDE [data-connector-intro](includes/data-connector-intro.md)]
 在本文中，您會使用 c # 來建立 Azure 資料總管的事件方格資料連線。

## <a name="prerequisites"></a>先決條件

* 如果尚未安裝 Visual Studio 2019，您可以下載並使用**免費的** [Visual Studio 2019 Community 版本](https://www.visualstudio.com/downloads/)。 務必在 Visual Studio 設定期間啟用 **Azure 開發**。
* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。
* 建立叢集 [和資料庫](create-cluster-database-csharp.md)
* 建立 [資料表和資料行對應](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)
*  (選擇性的) 設定[資料庫和資料表原則](database-table-policies-csharp.md)
* [使用事件方格訂用帳戶建立儲存體帳戶](ingest-data-event-grid.md)。

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](includes/data-explorer-data-connection-install-nuget-csharp.md)]

[!INCLUDE [data-explorer-authentication](includes/data-explorer-authentication.md)]

## <a name="add-an-event-grid-data-connection"></a>新增事件方格資料連線

下列範例會示範如何以程式設計的方式加入事件方格資料連線。 請參閱 [在 Azure 資料總管中建立 Event grid 資料](ingest-data-event-grid.md#create-an-event-grid-data-connection-in-azure-data-explorer) 連線，以使用 Azure 入口網站來新增事件方格資料連線。

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
var credential = new ClientCredential(clientId, clientSecret);
var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);

var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);

var kustoManagementClient = new KustoManagementClient(credentials)
{
    SubscriptionId = subscriptionId
};

var resourceGroupName = "testrg";
//The cluster and database that are created as part of the Prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
var dataConnectionName = "myeventhubconnect";
//The event hub and storage account that are created as part of the Prerequisites
var eventHubResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx";
var storageAccountResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Storage/storageAccounts/xxxxxx";
var consumerGroup = "$Default";
var location = "Central US";
//The table and column mapping are created as part of the Prerequisites
var tableName = "StormEvents";
var mappingRuleName = "StormEvents_CSV_Mapping";
var dataFormat = DataFormat.CSV;

await kustoManagementClient.DataConnections.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, dataConnectionName,
    new EventGridDataConnection(storageAccountResourceId, eventHubResourceId, consumerGroup, tableName: tableName, location: location, mappingRuleName: mappingRuleName, dataFormat: dataFormat));
```

|**設定** | **建議的值** | **欄位描述**|
|---|---|---|
| tenantId | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxx-xxxxxxxxx* | 您的租用戶識別碼。 也稱為目錄識別碼。|
| subscriptionId | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxx-xxxxxxxxx* | 您用來建立資源的訂用帳戶識別碼。|
| clientId | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxx-xxxxxxxxx* | 應用程式的用戶端識別碼，可存取您租使用者中的資源。|
| clientSecret | *xxxxxxxxxxxxxx* | 應用程式的用戶端密碼，可存取您租使用者中的資源。 |
| resourceGroupName | *>testrg* | 包含您叢集的資源組名。|
| clusterName | *mykustocluster* | 叢集的名稱。|
| databaseName | *mykustodatabase* | 叢集中目標資料庫的名稱。|
| dataConnectionName | *myeventhubconnect* | 您的資料連線所需的名稱。|
| tableName | *StormEvents* | 目標資料庫中的目標資料表名稱。|
| mappingRuleName | *StormEvents_CSV_Mapping* | 與目標資料表相關之資料行對應的名稱。|
| dataFormat | *Csv* | 訊息的資料格式。|
| eventHubResourceId | *資源識別碼* | 事件中樞設定為傳送事件的事件中樞資源識別碼。 |
| storageAccountResourceId | *資源識別碼* | 儲存體帳戶的資源識別碼，其中包含要內嵌的資料。 |
| consumerGroup | *$Default* | 事件中樞的取用者群組。|
| location | *Central US* | 資料連線資源的位置。|

## <a name="generate-sample-data"></a>產生範例資料

既然 Azure 資料總管和儲存體帳戶已連接，您可以建立範例資料，並將它上傳至儲存體。

> [!NOTE]
> Azure 資料總管不會在內嵌後刪除 blob。 使用 [Azure Blob 儲存體生命週期](/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal) 來管理 blob 刪除，以將 blob 保留三至五天。

### <a name="upload-file-using-azure-blob-storage-sdk"></a>使用 Azure Blob 儲存體 SDK 來上傳檔案

下列程式碼片段會在您的儲存體帳戶中建立新的容器、將現有的檔案 (作為 blob) 上傳至該容器，然後列出容器中的 blob。

```csharp
var azureStorageAccountConnectionString=<storage_account_connection_string>;

var containerName = <container_name>;
var blobName = <blob_name>;
var localFileName = <file_to_upload>;
var uncompressedSizeInBytes = <uncompressed_size_in_bytes>;
var mapping = <mappingReference>;

// Creating the container
var azureStorageAccount = CloudStorageAccount.Parse(azureStorageAccountConnectionString);
var blobClient = azureStorageAccount.CreateCloudBlobClient();
var container = blobClient.GetContainerReference(containerName);
container.CreateIfNotExists();

// Set metadata and upload file to blob
var blob = container.GetBlockBlobReference(blobName);
blob.Metadata.Add("rawSizeBytes", uncompressedSizeInBytes);
blob.Metadata.Add("kustoIngestionMappingReference", mapping);
blob.UploadFromFile(localFileName);

// List blobs
var blobs = container.ListBlobs();
```

### <a name="upload-file-using-azure-data-lake-sdk"></a>使用 Azure Data Lake SDK 來上傳檔案

使用 Data Lake Storage Gen2 時，您可以使用 [AZURE DATA LAKE SDK](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) 將檔案上傳至儲存體。 下列程式碼片段會在您的 Azure Data Lake 儲存體中建立新的檔案系統，並將具有中繼資料的本機檔案上傳至該檔案系統。

```csharp
var accountName = <storage_account_name>;
var accountKey = <storage_account_key>;
var fileSystemName = <file_system_name>;
var fileName = <file_name>;
var localFileName = <file_to_upload>;
var uncompressedSizeInBytes = <uncompressed_size_in_bytes>;
var mapping = <mapping_reference>;

var sharedKeyCredential = new StorageSharedKeyCredential(accountName, accountKey);
var dfsUri = "https://" + accountName + ".dfs.core.windows.net";
var dataLakeServiceClient = new DataLakeServiceClient(new Uri(dfsUri), sharedKeyCredential);

// Create the filesystem and an empty file
var dataLakeFileSystemClient = dataLakeServiceClient.CreateFileSystem(fileSystemName).Value;
var dataLakeFileClient = dataLakeFileSystemClient.CreateFile(fileName).Value;

// Set metadata
IDictionary<String, String> metadata = new Dictionary<string, string>();
metadata.Add("rawSizeBytes", uncompressedSizeInBytes);
metadata.Add("kustoIngestionMappingReference", mapping);
dataLakeFileClient.SetMetadata(metadata);

// Write to the file and close it
var fileStream = File.OpenRead(localFileName);
var fileSize = fileStream.Length;
dataLakeFileClient.Append(fileStream, offset: 0);
dataLakeFileClient.Flush(position: fileSize, close: true); // Note: This line triggers the event being processed by the data connection
```

> [!NOTE]
> 使用 [AZURE DATA LAKE SDK](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) 來上傳檔案時，第一次呼叫 [CreateFile](/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.createfile?view=azure-dotnet) 時，會觸發大小為0的事件方格事件，且 Azure 資料總管會忽略此事件。 當呼叫 flush 並將 "close" 參數設定為 "true" 時，就會觸發另一個事件。 此事件表示這是最後的更新，而檔案資料流程已關閉。 事件方格資料連線會處理這個事件。 如需有關排清的詳細資訊，請參閱 [Azure Data Lake flush 方法](/dotnet/api/azure.storage.files.datalake.datalakefileclient.flush?view=azure-dotnet)。

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](includes/data-explorer-data-connection-clean-resources-csharp.md)]
