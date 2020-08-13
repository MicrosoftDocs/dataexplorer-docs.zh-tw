---
title: 使用 C 建立適用于 Azure 資料總管的 IoT 中樞資料連線#
description: '在本文中，您將瞭解如何使用 c # 來建立 Azure 資料總管的 IoT 中樞資料連線。'
author: orspod
ms.author: orspodek
ms.reviewer: lugoldbe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: fa6d65b8a3db0d00849f4def77da5d09c0e9b694
ms.sourcegitcommit: f7f3ecef858c1e8d132fc10d1e240dcd209163bd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2020
ms.locfileid: "88201304"
---
# <a name="create-an-iot-hub-data-connection-for-azure-data-explorer-by-using-c-preview"></a>使用 c # (Preview 建立適用于 Azure 資料總管的 IoT 中樞資料連線) 

> [!div class="op_single_selector"]
> * [入口網站](ingest-data-iot-hub.md)
> * [C#](data-connection-iot-hub-csharp.md)
> * [Python](data-connection-iot-hub-python.md)
> * [Azure Resource Manager 範本](data-connection-iot-hub-resource-manager.md)

[!INCLUDE [data-connector-intro](includes/data-connector-intro.md)]
在本文中，您會使用 c # 來建立 Azure 資料總管的 IoT 中樞資料連線。

## <a name="prerequisites"></a>必要條件

* 如果尚未安裝 Visual Studio 2019，您可以下載並使用**免費的** [Visual Studio 2019 Community 版本](https://www.visualstudio.com/downloads/)。 務必在 Visual Studio 設定期間啟用 **Azure 開發**。
* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。
* 建立叢集 [和資料庫](create-cluster-database-csharp.md)
* 建立 [資料表和資料行對應](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)
*  (選擇性) 設定[資料庫和資料表原則](database-table-policies-csharp.md)
* 建立 [已設定共用存取原則的 IoT 中樞](ingest-data-iot-hub.md#create-an-iot-hub)。

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](includes/data-explorer-data-connection-install-nuget-csharp.md)]

[!INCLUDE [data-explorer-authentication](includes/data-explorer-authentication.md)]

## <a name="add-an-iot-hub-data-connection"></a>新增 IoT 中樞資料連線 

下列範例會示範如何以程式設計方式加入 IoT 中樞資料連線。 請參閱 [將 Azure 資料總管資料表連線到 IoT 中樞](ingest-data-iot-hub.md#connect-azure-data-explorer-table-to-iot-hub) ，以使用 Azure 入口網站來新增 IoT 中樞資料連線。

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
//The IoT hub that is created as part of the Prerequisites
var iotHubResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Devices/IotHubs/xxxxxx";
var sharedAccessPolicyName = "iothubforread";
var consumerGroup = "$Default";
var location = "Central US";
//The table and column mapping are created as part of the Prerequisites
var tableName = "StormEvents";
var mappingRuleName = "StormEvents_CSV_Mapping";
var dataFormat = DataFormat.CSV;

await kustoManagementClient.DataConnections.CreateOrUpdate(resourceGroupName, clusterName, databaseName, dataConnectionName,
            new IotHubDataConnection(iotHubResourceId, consumerGroup, sharedAccessPolicyName, tableName: tableName, location: location, mappingRuleName: mappingRuleName, dataFormat: dataFormat));
```

|**設定** | **建議的值** | **欄位描述**|
|---|---|---|
| tenantId | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxxx-xxxx-xxxxxxxxx* | 您的租用戶識別碼。 也稱為目錄識別碼。|
| subscriptionId | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxxx-xxxx-xxxxxxxxx* | 您用來建立資源的訂用帳戶識別碼。|
| clientId | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxxx-xxxx-xxxxxxxxx* | 應用程式的用戶端識別碼，可存取您租使用者中的資源。|
| clientSecret | *xxxxxxxxxxxxxx* | 應用程式的用戶端密碼，可以存取您租使用者中的資源。 |
| resourceGroupName | *testrg* | 包含您叢集的資源組名。|
| clusterName | *mykustocluster* | 叢集的名稱。|
| databaseName | *mykustodatabase* | 叢集中的目標資料庫名稱。|
| dataConnectionName | *myeventhubconnect* | 所需的資料連線名稱。|
| tableName | *StormEvents* | 目標資料庫中目標資料表的名稱。|
| mappingRuleName | *StormEvents_CSV_Mapping* | 與目標資料表相關之資料行對應的名稱。|
| dataFormat | *csv* | 訊息的資料格式。|
| iotHubResourceId | *資源識別碼* | 您的 IoT 中樞的資源識別碼，其中保存要用於內嵌的資料。 |
| sharedAccessPolicyName | *iothubforread* | 共用存取原則的名稱，定義裝置和服務連接到 IoT 中樞的許可權。 |
| consumerGroup | *$Default* | 事件中樞的取用者群組。|
| location | *Central US* | 資料連線資源的位置。|

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](includes/data-explorer-data-connection-clean-resources-csharp.md)]
