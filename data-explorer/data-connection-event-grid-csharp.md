---
title: 使用 Czure 資料資源管理員建立事件網格資料連接#
description: 在本文中,您將瞭解如何使用 C# 為 Azure 資料資源管理器創建事件網格數據連接。
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: d2432d302640d20bb199972bc686dadab41278f1
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81497807"
---
# <a name="create-an-event-grid-data-connection-for-azure-data-explorer-by-using-c"></a>使用 Czure 資料資源管理員建立事件網格資料連接#

> [!div class="op_single_selector"]
> * [入口網站](ingest-data-event-grid.md)
> * [C#](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Azure Resource Manager 範本](data-connection-event-grid-resource-manager.md)


Azure 資料總管是一項快速又可高度調整的資料探索服務，可用於處理記錄和遙測資料。 Azure 資料資源管理員提供從事件中心、IoT 中心和寫入 Blob 容器的 Blob 的引入(資料載入)。 在本文中,可以使用 C# 為 Azure 資料資源管理器創建事件網格數據連接。

## <a name="prerequisites"></a>Prerequisites

* 如果尚未安裝 Visual Studio 2019，您可以下載並使用**免費的** [Visual Studio 2019 Community 版本](https://www.visualstudio.com/downloads/)。 請確保在視覺化工作室設定期間開啟**Azure 開發**。
* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。
* [建立叢集與資料庫](create-cluster-database-csharp.md)
* 建立[表和列映射](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)
* 設定[資料庫與表原則](database-table-policies-csharp.md)(選擇性的)
* 使用[事件格線訂閱建立儲存帳號](ingest-data-event-grid.md#create-an-event-grid-subscription-in-your-storage-account)。

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](includes/data-explorer-data-connection-install-nuget-csharp.md)]

[!INCLUDE [data-explorer-authentication](includes/data-explorer-authentication.md)]

## <a name="add-an-event-grid-data-connection"></a>新增事件網格資料連線

下面的範例展示如何以程式設計方式添加事件網格數據連接。 有關使用 Azure 門戶新增事件網格資料連線,請參閱[在 Azure 資料資源管理器中建立事件網格資料連接](ingest-data-event-grid.md#create-an-event-grid-data-connection-in-azure-data-explorer)。

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
| tenantId | *xxxxxx-xxxxxx-xxx-xxx-xxxxxxxxx* | 您的租用戶識別碼。 也稱為目錄 ID。|
| subscriptionId | *xxxxxx-xxxxxx-xxx-xxx-xxxxxxxxx* | 用於資源創建的訂閱 ID。|
| clientId | *xxxxxx-xxxxxx-xxx-xxx-xxxxxxxxx* | 可以訪問租戶中資源的應用程式的客戶端 ID。|
| clientSecret | *xxxxxxxxxxxxxxx* | 可以訪問租戶中資源的應用程式的用戶端機密。 |
| resourceGroupName | *testrg* | 包含群集的資源組的名稱。|
| clusterName | *mykustocluster* | 群集的名稱。|
| databaseName | *mykustodatabase* | 群集中的目標資料庫的名稱。|
| 資料連線名稱 | *myeventhubconnect* | 數據連接的所需名稱。|
| tableName | *風暴事件* | 目標資料庫中的目標表的名稱。|
| 對應規則名稱 | *StormEvents_CSV_Mapping* | 與目標表相關的列映射的名稱。|
| 資料格式 | *Csv* | 消息的數據格式。|
| 事件HubResourceId | *資源識別碼* | 事件格線設定為發送事件的事件中心的資源 ID。 |
| 儲存帳戶資源 Id | *資源識別碼* | 儲存帳戶的資源 ID,用於保存要引入的數據。 |
| 消費者群體 | *$Default* | 事件中心的消費者組。|
| location | *美國中部* | 數據連接資源的位置。|

## <a name="generate-sample-data"></a>產生範例資料

既然 Azure 資料檔案總管及儲存體帳戶已經連線，您可以建立範例資料並上傳至 Blob 儲存體。

此指令碼會在儲存體帳戶中建立新的容器，將現有的檔案 (以 Blob 形式) 上傳至該容器，然後列出容器中的 Blob。

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

// Set metadata and upload file to blob
var blob = container.GetBlockBlobReference(blobName);
blob.Metadata.Add("rawSizeBytes", "4096‬"); // the uncompressed size is 4096 bytes
blob.Metadata.Add("kustoIngestionMappingReference", "mapping_v2‬");
blob.UploadFromFile(localFileName);

// List blobs
var blobs = container.ListBlobs();
```

> [!NOTE]
> Azure 資料資源管理員不會刪除引入後的 Blob。 通過使用[Azure Blob 儲存生命週期](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal)來管理 Blob 刪除,將 Blob 保留三到五天。

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](includes/data-explorer-data-connection-clean-resources-csharp.md)]
