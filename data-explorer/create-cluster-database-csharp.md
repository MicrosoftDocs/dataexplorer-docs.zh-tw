---
title: 使用 C 建立 &数据库的 Azure 資料資源管理器群集#
description: 了解如何使用 C# 建立 Azure 資料總管叢集與資料庫
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 0cdd649e6d031865e1d48a3cc38f58dbdb63392c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81497053"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-c"></a>使用 C# 建立 Azure 資料總管叢集與資料庫

> [!div class="op_single_selector"]
> * [入口網站](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Azure Resource Manager 範本](create-cluster-database-resource-manager.md)

Azure 資料總管是快速、完全受控的資料分析服務，可即時分析來自應用程式、網站、IoT 裝置等的大量資料流。 若要使用 Azure 資料總管，請先建立叢集，然後在該叢集中建立一或多個資料庫。 然後將資料內嵌 (載入) 至資料庫，讓您可以對資料執行查詢。 在本文中,使用 C# 創建群集和資料庫。

## <a name="prerequisites"></a>Prerequisites

* 如果尚未安裝 Visual Studio 2019，您可以下載並使用**免費的** [Visual Studio 2019 Community 版本](https://www.visualstudio.com/downloads/)。 請確保在視覺化工作室設定期間開啟**Azure 開發**。
* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](includes/data-explorer-data-connection-install-nuget-csharp.md)]

## <a name="authentication"></a>驗證
為了運行本文中的示例,我們需要一個可以訪問資源的 Azure AD 應用程式和服務主體。 檢查[創建 Azure AD 應用程式](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)以建立免費的 Azure AD 應用程式,並在訂閱範圍內添加角色分配。 它並展示如何取得`Directory (tenant) ID`、`Application ID``Client Secret`與 。

## <a name="create-the-azure-data-explorer-cluster"></a>建立 Azure 資料總管叢集

1. 使用下列程式碼建立您的叢集：

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
    var clusterName = "mykustocluster";
    var location = "Central US";
    var skuName = "Standard_D13_v2";
    var tier = "Standard";
    var capacity = 5;
    var sku = new AzureSku(skuName, tier, capacity);
    var cluster = new Cluster(location, sku);
    await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
    ```

   |**設定** | **建議的值** | **欄位描述**|
   |---|---|---|
   | clusterName | *mykustocluster* | 所需的叢集名稱。|
   | skuName | *Standard_D13_v2* | 將用於叢集的 SKU。 |
   | tier | *Standard* | SKU 層。 |
   | 處理能力 | *number* | 群集的實例數。 |
   | resourceGroupName | *testrg* | 將在其中建立叢集的資源群組名稱。 |

    > [!NOTE]
    > **創建群集**是一項長時間運行的操作,因此強烈建議使用 CreateOrUpdateAsync,而不是 CreateOrUpdate。 

1. 執行下列命令來檢查是否已成功建立叢集：

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

如果結果中包含有 `Succeeded` 值的 `ProvisioningState`，表示已成功建立叢集。

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>在 Azure 資料總管叢集中建立資料庫

1. 使用下列程式碼建立您的資料庫：

    ```csharp
    var hotCachePeriod = new TimeSpan(3650, 0, 0, 0);
    var softDeletePeriod = new TimeSpan(3650, 0, 0, 0);
    var databaseName = "mykustodatabase";
    var database = new ReadWriteDatabase(location: location, softDeletePeriod: softDeletePeriod, hotCachePeriod: hotCachePeriod);

    await kustoManagementClient.Databases.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, database);
    ```

        [!NOTE]
        If you are using C# version 2.0.0 or below, use Database instead of ReadWriteDatabase.

   |**設定** | **建議的值** | **欄位描述**|
   |---|---|---|
   | clusterName | *mykustocluster* | 將在其中建立資料庫的叢集名稱。|
   | databaseName | *mykustodatabase* | 您的資料庫名稱。|
   | resourceGroupName | *testrg* | 將在其中建立叢集的資源群組名稱。 |
   | softDeletePeriod | *3650:00:00:00* | 將保留資料以供查詢的時間長度。 |
   | hotCachePeriod | *3650:00:00:00* | 資料將保留在快取中的時間長度。 |

2. 執行下列命令以查看您所建立的資料庫：

    ```csharp
    kustoManagementClient.Databases.Get(resourceGroupName, clusterName, databaseName) as ReadWriteDatabase;
    ```

您此時有一個叢集和一個資料庫。

## <a name="clean-up-resources"></a>清除資源

* 如果您計劃關注我們的其他文章,請保留您創建的資源。
* 若要清除資源，請刪除叢集。 您刪除叢集時，也會刪除其中的所有資料庫。 使用下列命令刪除您的叢集：

    ```csharp
    kustoManagementClient.Clusters.Delete(resourceGroupName, clusterName);
    ```

## <a name="next-steps"></a>後續步驟

* [使用 Azure 資料總管 .NET Standard SDK 內嵌資料 (預覽)](net-standard-ingest-data.md)
