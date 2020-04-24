---
title: 使用 Czure 資料資源管理員加入資料庫主體#
description: 在本文中,您將瞭解如何使用 C# 為 Azure 資料資源管理器添加資料庫主體。
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 17107c1164b0ff84600776afd32feb4ef40256ff
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81498665"
---
# <a name="add-database-principals-for-azure-data-explorer-by-using-c"></a>使用 Czure 資料資源管理員加入資料庫主體#

> [!div class="op_single_selector"]
> * [C#](database-principal-csharp.md)
> * [Python](database-principal-python.md)
> * [Azure Resource Manager 範本](database-principal-resource-manager.md)

Azure 資料總管是一項快速又可高度調整的資料探索服務，可用於處理記錄和遙測資料。 在本文中,可以使用 C# 為 Azure 數據資源管理器添加資料庫主體。

## <a name="prerequisites"></a>Prerequisites

* 如果尚未安裝 Visual Studio 2019，您可以下載並使用**免費的** [Visual Studio 2019 Community 版本](https://www.visualstudio.com/downloads/)。 請確保在視覺化工作室設定期間開啟**Azure 開發**。
* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。
* [建立叢集與資料庫](create-cluster-database-csharp.md)。

## <a name="install-c-nuget"></a>安裝 C# NuGet

* 安裝[Microsoft.Azure.管理.kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)。
* 安裝[Microsoft.Rest.用戶端執行時.Azure.](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication)身份驗證。

[!INCLUDE [data-explorer-authentication](includes/data-explorer-authentication.md)]

## <a name="add-a-database-principal"></a>新增資料庫主體

下面的範例展示如何以程式設計方式添加資料庫主體。

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var kustoManagementClient = new KustoManagementClient(serviceCreds)
{
    SubscriptionId = subscriptionId
};

var resourceGroupName = "testrg";
//The cluster and database that are created as part of the Prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
string principalAssignmentName = "databasePrincipalAssignment1";
string principalId = "xxxxxxxx";//User email, application ID, or security group name
string role = "Admin";//Admin, Ingestor, Monitor, User, UnrestrictedViewers, Viewer
string tenantIdForPrincipal = tenantId;
string principalType = "App";//User, App, or Group

var databasePrincipalAssignment = new DatabasePrincipalAssignment(principalId, role, principalType, tenantId: tenantIdForPrincipal);
await kustoManagementClient.DatabasePrincipalAssignments.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, principalAssignmentName, databasePrincipalAssignment);
```

|**設定** | **建議的值** | **欄位描述**|
|---|---|---|
| tenantId | *xxxxxx-xxxxxx-xxx-xxx-xxxxxxxxx* | 您的租用戶識別碼。 也稱為目錄 ID。|
| subscriptionId | *xxxxxx-xxxxxx-xxx-xxx-xxxxxxxxx* | 用於資源創建的訂閱 ID。|
| clientId | *xxxxxx-xxxxxx-xxx-xxx-xxxxxxxxx* | 可以訪問租戶中資源的應用程式的客戶端 ID。|
| clientSecret | *xxxxxxxxxxxxxxx* | 可以訪問租戶中資源的應用程式的用戶端機密。 |
| resourceGroupName | *testrg* | 包含群集的資源組的名稱。|
| clusterName | *mykustocluster* | 群集的名稱。|
| databaseName | *mykustodatabase* | 您的資料庫名稱。|
| 主體配置名稱 | *資料庫主體配置1* | 資料庫主體資源的名稱。|
| principalId | *xxxxxx-xxxxxx-xxx-xxx-xxxxxxxxx* | 主體ID,可以是使用者電子郵件、應用程式ID或安全組名稱。|
| 角色 (role) | *管理員* | 資料庫主體的角色,可以是"管理員","Ingestor","監視器","使用者","無限制查看器","查看器"。|
| 租戶 Idfor 主要 | *xxxxxx-xxxxxx-xxx-xxx-xxxxxxxxx* | 主體的租戶 ID。|
| principalType | *應用程式* | 主體的類型,可以是"使用者"、"應用"或"組"|

## <a name="next-steps"></a>後續步驟

* [使用 Azure 資料資源管理員節點庫引入資料](node-ingest-data.md)
