---
title: 使用 Czure 資料資源管理員建立事件中心資料連線#
description: 在本文中,您將瞭解如何使用 C# 為 Azure 資料資源管理器創建事件中心數據連接。
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 54f7541321b50ae4d3cf679d9224f70cb21a46ab
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81497768"
---
# <a name="create-an-event-hub-data-connection-for-azure-data-explorer-by-using-c"></a>使用 Czure 資料資源管理員建立事件中心資料連線#

> [!div class="op_single_selector"]
> * [入口網站](ingest-data-event-hub.md)
> * [C#](data-connection-event-hub-csharp.md)
> * [Python](data-connection-event-hub-python.md)
> * [Azure Resource Manager 範本](data-connection-event-hub-resource-manager.md)

Azure 資料總管是一項快速又可高度調整的資料探索服務，可用於處理記錄和遙測資料。 Azure 資料資源管理員提供從事件中心、IoT 中心和寫入 Blob 容器的 Blob 的引入(資料載入)。 在本文中,可以使用 C# 為 Azure 資料資源管理器創建事件中心數據連接。

## <a name="prerequisites"></a>Prerequisites

* 如果尚未安裝 Visual Studio 2019，您可以下載並使用**免費的** [Visual Studio 2019 Community 版本](https://www.visualstudio.com/downloads/)。 請確保在視覺化工作室設定期間開啟**Azure 開發**。
* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。
* [建立叢集與資料庫](create-cluster-database-csharp.md)
* 建立[表和列映射](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)
* 設定[資料庫與表原則](database-table-policies-csharp.md)(選擇性的)
* 建立[以引入的資料中心](ingest-data-event-hub.md#create-an-event-hub)。 

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](includes/data-explorer-data-connection-install-nuget-csharp.md)]

[!INCLUDE [data-explorer-authentication](includes/data-explorer-authentication.md)]

## <a name="add-an-event-hub-data-connection"></a>新增事件中心資料連線

下面的範例展示如何以程式設計方式添加事件中心數據連接。 有關使用 Azure 門戶新增事件中心資料連線,請參閱[連接到事件中心](ingest-data-event-hub.md#connect-to-the-event-hub)。

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
//The event hub that is created as part of the Prerequisites
var eventHubResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx";
var consumerGroup = "$Default";
var location = "Central US";
//The table and column mapping are created as part of the Prerequisites
var tableName = "StormEvents";
var mappingRuleName = "StormEvents_CSV_Mapping";
var dataFormat = DataFormat.CSV;
await kustoManagementClient.DataConnections.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, dataConnectionName, 
    new EventHubDataConnection(eventHubResourceId, consumerGroup, location: location, tableName: tableName, mappingRuleName: mappingRuleName, dataFormat: dataFormat));
```

|**設定** | **建議的值** | **欄位描述**|
|---|---|---|
| tenantId | *xxxxxx-xxxxxx-xxx-xxx-xxxxxxxxx* | 您的租用戶識別碼。 也稱為目錄 ID。|
| subscriptionId | *xxxxxx-xxxxxx-xxx-xxx-xxxxxxxxx* | 用於資源創建的訂閱 ID。|
| clientId | *xxxxxx-xxxxxx-xxx-xxx-xxxxxxxxx* | 可以訪問租戶中資源的應用程式的客戶端 ID。|
| clientSecret | *xxxxxxxxxxxxxxx* | 可以訪問租戶中資源的應用程式的用戶端機密。|
| resourceGroupName | *testrg* | 包含群集的資源組的名稱。|
| clusterName | *mykustocluster* | 群集的名稱。|
| databaseName | *mykustodatabase* | 群集中的目標資料庫的名稱。|
| 資料連線名稱 | *myeventhubconnect* | 數據連接的所需名稱。|
| tableName | *風暴事件* | 目標資料庫中的目標表的名稱。|
| 對應規則名稱 | *StormEvents_CSV_Mapping* | 與目標表相關的列映射的名稱。|
| 資料格式 | *Csv* | 消息的數據格式。|
| 事件HubResourceId | *資源識別碼* | 事件中心的資源 ID,用於保存要引入的數據。 |
| 消費者群體 | *$Default* | 事件中心的消費者組。|
| location | *美國中部* | 數據連接資源的位置。|

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](includes/data-explorer-data-connection-clean-resources-csharp.md)]
