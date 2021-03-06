---
title: 使用 C 設定 Azure 資料總管叢集上的串流內嵌#
description: 瞭解如何設定 Azure 資料總管叢集，並開始使用 C 進行串流內嵌來載入資料#
author: orspod
ms.author: orspodek
ms.reviewer: alexefro
ms.service: data-explorer
ms.topic: how-to
ms.date: 07/13/2020
ms.openlocfilehash: 72525e15427d7c2135f881bc63e34826791050f2
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88874829"
---
# <a name="configure-streaming-ingestion-on-your-azure-data-explorer-cluster-using-c"></a>使用 C 設定 Azure 資料總管叢集上的串流內嵌#

> [!div class="op_single_selector"]
> * [入口網站](ingest-data-streaming.md)
> * [C#](ingest-data-streaming-csharp.md)

[!INCLUDE [ingest-data-streaming-intro](includes/ingest-data-streaming-intro.md)]

## <a name="prerequisites"></a>先決條件

* 如果您未安裝 Visual Studio 2019，請下載並使用 **免費**的 [Visual Studio 2019 社區版](https://www.visualstudio.com/downloads/)。  在 Visual Studio 設定期間啟用 **Azure 開發** 。
* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。
* 建立 [Azure 資料總管叢集和資料庫](create-cluster-database-csharp.md)
   
## <a name="enable-streaming-ingestion-on-your-cluster-using-c"></a>使用 C 在您的叢集上啟用串流內嵌#

### <a name="enable-streaming-ingestion-while-creating-a-new-cluster"></a>建立新叢集時啟用串流內嵌

您可以在建立新的 Azure 資料總管叢集時啟用串流內嵌。

```csharp
...
var cluster = new Cluster(location, sku, enableStreamingIngest:true);
...
```

### <a name="enable-streaming-ingestion-on-an-existing-cluster"></a>在現有的叢集上啟用串流內嵌

若要在 Azure 資料總管叢集上啟用串流內嵌，請執行下列程式碼：

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
    var clusterName = "mystreamingcluster";
    var clusterUpdateParameters = new ClusterUpdate(enableStreamingIngest: true);

    await kustoManagementClient.Clusters.UpdateAsync(resourceGroupName, clusterName, clusterUpdateParameters);
```

> [!WARNING]
> 在啟用串流內嵌之前，請先檢查 [限制](#limitations) 。

## <a name="create-a-target-table-and-define-the-policy-using-c"></a>使用 C 建立目標資料表並定義原則#

若要建立資料表，並在此資料表上定義串流內嵌原則，請執行下列程式碼：

```csharp
    var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
    var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
    var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
    var databaseName = "StreamingTestDb";
    var tableName = "TestTable";
    var kcsb = new KustoConnectionStringBuilder("https://mystreamingcluster.westcentralus.kusto.windows.net", databaseName);
    kcsb = kcsb.WithAadApplicationKeyAuthentication(clientId, clientSecret, tenantId);

    var tableSchema = new TableSchema(
        tableName,
        new ColumnSchema[]
        {
            new ColumnSchema("TimeStamp", "System.DateTime"),
            new ColumnSchema("Name",      "System.String"),
            new ColumnSchema("Metric",    "System.int"),
            new ColumnSchema("Source",    "System.String"),
        });

    var tableCreateCommand = CslCommandGenerator.GenerateTableCreateCommand(tableSchema);
    var tablePolicyAlterCommand = CslCommandGenerator.GenerateTableAlterStreamingIngestionPolicyCommand(tableName, isEnabled: true);
    using (var client = KustoClientFactory.CreateCslAdminProvider(kcsb))
    {
        client.ExecuteControlCommand(tableCreateCommand);

        client.ExecuteControlCommand(tablePolicyAlterCommand);
    }
```

[!INCLUDE [ingest-data-streaming-use](includes/ingest-data-streaming-types.md)]

[!INCLUDE [ingest-data-streaming-disable](includes/ingest-data-streaming-disable.md)]

### <a name="drop-the-streaming-ingestion-policy-using-c"></a>使用 C 放置串流內嵌原則#

若要從資料表中卸載串流內嵌原則，請執行下列程式碼：
    
```csharp
        var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
        var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
        var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
        var databaseName = "StreamingTestDb";
        var tableName = "TestTable";
        var kcsb = new KustoConnectionStringBuilder("https://mystreamingcluster.westcentralus.kusto.windows.net", databaseName);
        kcsb = kcsb.WithAadApplicationKeyAuthentication(clientId, clientSecret, tenantId);
    
        var tablePolicyDropCommand = CslCommandGenerator.GenerateTableStreamingIngestionPolicyDropCommand(databaseName, tableName);
        using (var client = KustoClientFactory.CreateCslAdminProvider(kcsb))
        {
            client.ExecuteControlCommand(tablePolicyDropCommand);
        }
```

### <a name="disable-streaming-ingestion-using-c"></a>使用 C 停用串流內嵌#

若要停用叢集中的串流內嵌，請執行下列程式碼：
    
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
        var clusterName = "mystreamingcluster";
        var clusterUpdateParameters = new ClusterUpdate(enableStreamingIngest: false);
    
        await kustoManagementClient.Clusters.UpdateAsync(resourceGroupName, clusterName, clusterUpdateParameters);
```
    
[!INCLUDE [ingest-data-streaming-limitations](includes/ingest-data-streaming-limitations.md)]

## <a name="next-steps"></a>後續步驟

* [在 Azure 資料總管中查詢資料](web-query-data.md)
