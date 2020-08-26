---
title: 使用 C 新增 Azure 資料總管的叢集主體#
description: '在本文中，您將瞭解如何使用 c # 新增 Azure 資料總管的叢集主體。'
author: orspod
ms.author: orspodek
ms.reviewer: lugoldbe
ms.service: data-explorer
ms.topic: how-to
ms.date: 02/03/2020
ms.openlocfilehash: a7079235d3ab43bd193b7e4c0f7cbe25689a5d3e
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88872296"
---
# <a name="add-cluster-principals-for-azure-data-explorer-by-using-c"></a>使用 C 新增 Azure 資料總管的叢集主體#

> [!div class="op_single_selector"]
> * [C#](cluster-principal-csharp.md)
> * [Python](cluster-principal-python.md)
> * [Azure Resource Manager 範本](cluster-principal-resource-manager.md)

Azure 資料總管是一項快速又可高度調整的資料探索服務，可用於處理記錄和遙測資料。 在本文中，您會使用 c # 來新增 Azure 資料總管的叢集主體。

## <a name="prerequisites"></a>先決條件

* 如果尚未安裝 Visual Studio 2019，您可以下載並使用**免費的** [Visual Studio 2019 Community 版本](https://www.visualstudio.com/downloads/)。 務必在 Visual Studio 設定期間啟用 **Azure 開發**。
* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。
* [建立](create-cluster-database-csharp.md)叢集。

## <a name="install-c-nuget"></a>安裝 c # NuGet

* 請安裝 [kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)。
* 安裝 [ClientRuntime](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication) 驗證以進行驗證。

[!INCLUDE [data-explorer-authentication](includes/data-explorer-authentication.md)]

## <a name="add-a-cluster-principal"></a>新增叢集主體

下列範例會示範如何以程式設計方式新增叢集主體。

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
//The cluster that is created as part of the Prerequisites
var clusterName = "mykustocluster";
string principalAssignmentName = "clusterPrincipalAssignment1";
string principalId = "xxxxxxxx";//User email, application ID, or security group name
string role = "AllDatabasesAdmin";//AllDatabasesAdmin or AllDatabasesViewer
string tenantIdForPrincipal = tenantId;
string principalType = "App";//User, App, or Group

var clusterPrincipalAssignment = new ClusterPrincipalAssignment(principalId, role, principalType, tenantId: tenantIdForPrincipal);
await kustoManagementClient.ClusterPrincipalAssignments.CreateOrUpdateAsync(resourceGroupName, clusterName, principalAssignmentName, clusterPrincipalAssignment);
```

|**設定** | **建議的值** | **欄位描述**|
|---|---|---|
| tenantId | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxx-xxxxxxxxx* | 您的租用戶識別碼。 也稱為目錄識別碼。|
| subscriptionId | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxx-xxxxxxxxx* | 您用來建立資源的訂用帳戶識別碼。|
| clientId | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxx-xxxxxxxxx* | 應用程式的用戶端識別碼，可存取您租使用者中的資源。|
| clientSecret | *xxxxxxxxxxxxxx* | 應用程式的用戶端密碼，可存取您租使用者中的資源。 |
| resourceGroupName | *>testrg* | 包含您叢集的資源組名。|
| clusterName | *mykustocluster* | 叢集的名稱。|
| principalAssignmentName | *clusterPrincipalAssignment1* | 叢集主體資源的名稱。|
| principalId | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxx-xxxxxxxxx* | 主體識別碼，可以是使用者電子郵件、應用程式識別碼或安全性群組名稱。|
| 角色 (role) | *AllDatabasesAdmin* | 叢集主體的角色，可以是 ' AllDatabasesAdmin' ' 或 ' AllDatabasesViewer '。|
| tenantIdForPrincipal | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxx-xxxxxxxxx* | 主體的租使用者識別碼。|
| principalType | *App* | 主體的型別，可以是「使用者」、「應用程式」或「群組」|

## <a name="next-steps"></a>後續步驟

* [新增資料庫主體](database-principal-csharp.md)
