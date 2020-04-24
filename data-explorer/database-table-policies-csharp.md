---
title: 使用 Azure 資料資源管理員 C# SDK 建立原則
description: 在本文中,您將學習如何使用 C# 創建策略。
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: ee9d740a3bd9748611b4e822f5204eee2633b1bf
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81496884"
---
# <a name="create-database-and-table-policies-for-azure-data-explorer-by-using-c"></a>使用 Czure 資料資源管理員建立資料庫和表原則#

> [!div class="op_single_selector"]
> * [C#](database-table-policies-csharp.md)
> * [Python](database-table-policies-python.md)
>

Azure 資料總管是一項快速又可高度調整的資料探索服務，可用於處理記錄和遙測資料。 在本文中,您將使用 C# 為 Azure 資料資源管理器創建資料庫和表策略。

## <a name="prerequisites"></a>Prerequisites

* Visual Studio 2019。 如果你沒有視覺工作室2019,你可以下載和使用*免費*[的視覺工作室社區2019。](https://www.visualstudio.com/downloads/) 請務必在可視化工作室設定期間選擇**Azure 開發**。
* Azure 訂用帳戶。 如果需要,可以在開始之前建立[一個免費的 Azure 帳戶](https://azure.microsoft.com/free/)。
* [測試叢集與資料庫](create-cluster-database-csharp.md)。
* [測試表](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)。

## <a name="install-c-nuget"></a>安裝 C# NuGet

* 安裝[Azure 資料資源管理員 (Kusto) NuGet 套件](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)。
* 安裝[Microsoft. Azure.Kusto.Data.NET 標準NuGet 套件](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Data.NETStandard/)。 (可選,用於更改表策略。
* 安裝[Microsoft.身份模型.用戶端.ActiveDirectory NuGet 包](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/),用於身份驗證。

## <a name="authentication"></a>驗證
要運行本文中的範例,需要一個可以存取資源的 Azure 活動目錄 (Azure AD) 應用程式和服務主體。 您可以使用同一 Azure AD 應用程式從[測試群集和資料庫](create-cluster-database-csharp.md#authentication)進行身份驗證。 如果要使用其他 Azure AD 應用程式,請參閱[創建 Azure AD 應用程式](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)以建立免費的 Azure AD 應用程式,並在訂閱範圍內添加角色分配。 本文還展示如何取得`Directory (tenant) ID`、`Application ID``Client secret`與 。 您可能需要將新的 Azure AD 應用程式添加為資料庫中的主體。 有關詳細資訊,請參閱管理[Azure 資料資源管理員資料庫權限](https://docs.microsoft.com/azure/data-explorer/manage-database-permissions)。

## <a name="alter-database-retention-policy"></a>變更資料庫保留原則
設置具有 10 天軟刪除期的保留策略。
    
```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
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
//The cluster and database that are created as part of the prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
await kustoManagementClient.Databases.UpdateAsync(resourceGroupName, clusterName, databaseName, new DatabaseUpdate(softDeletePeriod: TimeSpan.FromDays(10)));
```

## <a name="alter-database-cache-policy"></a>變更資料庫快取原則
為資料庫設置緩存策略。 前五天的數據將位於群集 SSD 上。

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
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
//The cluster and database that are created as part of the prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
await kustoManagementClient.Databases.UpdateAsync(resourceGroupName, clusterName, databaseName, new DatabaseUpdate(hotCachePeriod: TimeSpan.FromDays(5)));
```

## <a name="alter-table-cache-policy"></a>變更表快取原則
為表設置緩存策略。 前五天的數據將位於群集 SSD 上。

```csharp
var kustoUri = "https://<ClusterName>.<Region>.kusto.windows.net:443/";
var databaseName = "<DatabaseName>";
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var tableName = "<TableName>"

var kustoConnectionStringBuilder =
    new KustoConnectionStringBuilder(kustoUri)
    {
        FederatedSecurity = true,
        InitialCatalog = databaseName,
        ApplicationClientId = clientId,
        ApplicationKey = clientSecret,
        Authority = tenantId
    };

using (var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder))
{
    //dataHotSpan and indexHotSpan should have the same value
    var hotSpan = TimeSpan.FromDays(5);
    var command1 = CslCommandGenerator.GenerateAlterTableCachingPolicyCommand(tableName: tableName,
                    dataHotSpan: hotSpan, indexHotSpan: hotSpan);

    kustoClient.ExecuteControlCommand(command);
}
```

## <a name="add-a-new-principal-for-the-database"></a>為資料庫新增新主體
添加新的 Azure AD 應用程式作為資料庫的管理員主體。

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
var clientIdToAdd = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
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
//The cluster and database that are created as part of the prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
await kustoManagementClient.Databases.AddPrincipalsAsync(resourceGroupName, clusterName, databaseName,
                new DatabasePrincipalListRequest()
                {
                    Value = new List<DatabasePrincipal>()
                    {
                        new DatabasePrincipal("Admin", "<database_principle_name>", "App", appId: clientIdToAdd, tenantName:tenantId)
                    }
                });
```
## <a name="next-steps"></a>後續步驟

* [閱讀有關資料庫和表原則的詳細資訊](kusto/management/policies.md)
