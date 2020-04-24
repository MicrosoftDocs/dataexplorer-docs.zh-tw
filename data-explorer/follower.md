---
title: 使用跟隨器資料庫功能在 Azure 資料資源管理員中附加資料庫
description: 瞭解如何使用關注者資料庫功能在 Azure 資料資源管理器中附加資料庫。
author: orspod
ms.author: orspodek
ms.reviewer: gabilehner
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: 4c385551f8983219dddf840f7a2d590809b030a3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81496715"
---
# <a name="use-follower-database-to-attach-databases-in-azure-data-explorer"></a>使用跟隨者資料庫在 Azure 資料資源管理員中附加資料庫

**關注者資料庫**功能允許您將位於不同群集中的資料庫附加到 Azure 資料資源管理器群集。 **跟隨者資料庫**以*唯讀*模式附加,因此可以查看資料並運行對引入**到引線資料庫中**的數據的查詢。 關注者資料庫同步引線資料庫中的更改。 由於同步,數據可用性會延遲幾秒鐘到幾分鐘。 時滯的長度取決於引線資料庫元數據的總體大小。 領導者和關注者資料庫使用相同的存儲帳戶來獲取數據。 存儲歸引線資料庫所有。 跟隨者資料庫無需攝用數據即可查看數據。 由於附加的資料庫是唯讀資料庫,因此資料庫中的數據、表和策略不能修改,但[快取策略](#configure-caching-policy)、[主體](#manage-principals)和[許可權](#manage-permissions)除外。 無法刪除附加的資料庫。 必須由領導者或跟隨者分離它們,只有這樣才能將其刪除。 

使用跟隨器功能將資料庫附加到其他群集用作基礎結構,用於在組織和團隊之間共享數據。 此功能可用於隔離計算資源,以保護生產環境免受非生產用例的影響。 跟隨器還可用於將 Azure 資料資源管理器群集的成本與運行數據查詢的一方相關聯。

## <a name="which-databases-are-followed"></a>遵循哪些資料庫?

* 群集可以遵循一個資料庫、多個資料庫或引線群集的所有資料庫。 
* 單個群集可以跟蹤來自多個引線群集的資料庫。 
* 叢集可以同時包含跟隨者資料庫和引線資料庫

## <a name="prerequisites"></a>Prerequisites

1. 如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。
1. 為領導者和跟隨者[創建群集和 DB。](/azure/data-explorer/create-cluster-database-portal)
1. 使用[引入概述](/azure/data-explorer/ingest-data-overview)中討論的各種方法之一[將數據](/azure/data-explorer/ingest-sample-data)引入引線資料庫。

## <a name="attach-a-database"></a>附加資料庫

可以使用多種方法來附加資料庫。 在本文中,我們將討論使用 C# 或 Azure 資源管理器範本附加資料庫。 要附加資料庫,您必須對引線群集和跟隨器群集具有許可權。 有關權限的詳細資訊,請參閱[管理權限](#manage-permissions)。

### <a name="attach-a-database-using-c"></a>使用 C 連線資料庫#

#### <a name="needed-nugets"></a>所需的 NuGets

* 安裝[Microsoft.Azure.管理.kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)。
* 安裝[Microsoft.Rest.ClientRuntime.Azure.身份驗證](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication)。

#### <a name="code-example"></a>程式碼範例

```Csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var leaderSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var followerSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new KustoManagementClient(serviceCreds){
    SubscriptionId = followerSubscriptionId
};

var followerResourceGroupName = "followerResouceGroup";
var leaderResourceGroup = "leaderResouceGroup";
var leaderClusterName = "leader";
var followerClusterName = "follower";
var attachedDatabaseConfigurationName = "uniqueNameForAttachedDatabaseConfiguration";
var databaseName = "db"; // Can be specific database name or * for all databases
var defaultPrincipalsModificationKind = "Union"; 
var location = "North Central US";

AttachedDatabaseConfiguration attachedDatabaseConfigurationProperties = new AttachedDatabaseConfiguration()
{
    ClusterResourceId = $"/subscriptions/{leaderSubscriptionId}/resourceGroups/{leaderResourceGroup}/providers/Microsoft.Kusto/Clusters/{leaderClusterName}",
    DatabaseName = databaseName,
    DefaultPrincipalsModificationKind = defaultPrincipalsModificationKind,
    Location = location
};

var attachedDatabaseConfigurations = resourceManagementClient.AttachedDatabaseConfigurations.CreateOrUpdate(followerResourceGroupName, followerClusterName, attachedDatabaseConfigurationName, attachedDatabaseConfigurationProperties);
```

### <a name="attach-a-database-using-python"></a>使用 Python 連線資料庫

#### <a name="needed-modules"></a>所需的模組

```
pip install azure-common
pip install azure-mgmt-kusto
```

#### <a name="code-example"></a>程式碼範例

```python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import AttachedDatabaseConfiguration
from azure.common.credentials import ServicePrincipalCredentials
import datetime

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
follower_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
leader_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, follower_subscription_id)

follower_resource_group_name = "followerResouceGroup"
leader_resouce_group_name = "leaderResouceGroup"
follower_cluster_name = "follower"
leader_cluster_name = "leader"
attached_database_Configuration_name = "uniqueNameForAttachedDatabaseConfiguration"
database_name  = "db" # Can be specific database name or * for all databases
default_principals_modification_kind  = "Union"
location = "North Central US"
cluster_resource_id = "/subscriptions/" + leader_subscription_id + "/resourceGroups/" + leader_resouce_group_name + "/providers/Microsoft.Kusto/Clusters/" + leader_cluster_name

attached_database_configuration_properties = AttachedDatabaseConfiguration(cluster_resource_id = cluster_resource_id, database_name = database_name, default_principals_modification_kind = default_principals_modification_kind, location = location)

#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.attached_database_configurations.create_or_update(follower_resource_group_name, follower_cluster_name, attached_database_Configuration_name, attached_database_configuration_properties)
```

### <a name="attach-a-database-using-an-azure-resource-manager-template"></a>使用 Azure 資源管理員樣本附加資料庫

在本節中,您將學習使用[Azure 資源管理器範本](/azure/azure-resource-manager/management/overview)將資料庫附加到現有 cluser。 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "followerClusterName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Name of the cluster to which the database will be attached."
            }
        },
        "attachedDatabaseConfigurationsName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Name of the attached database configurations to create."
            }
        },
        "databaseName": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The name of the database to follow. You can follow all databases by using '*'."
            }
        },
        "leaderClusterResourceId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The resource ID of the leader cluster."
            }
        },
        "defaultPrincipalsModificationKind": {
            "type": "string",
            "defaultValue": "Union",
            "metadata": {
                "description": "The default principal modification kind."
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Location for all resources."
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "[concat(parameters('followerClusterName'), '/', parameters('attachedDatabaseConfigurationsName'))]",
            "type": "Microsoft.Kusto/clusters/attachedDatabaseConfigurations",
            "apiVersion": "2019-09-07",
            "location": "[parameters('location')]",
            "properties": {
                "databaseName": "[parameters('databaseName')]",
                "clusterResourceId": "[parameters('leaderClusterResourceId')]",
                "defaultPrincipalsModificationKind": "[parameters('defaultPrincipalsModificationKind')]"
            }
        }
    ]
}
```

### <a name="deploy-the-template"></a>部署範本 

可以使用[Azure 門戶](https://portal.azure.com)或使用電源外殼部署 Azure 資源管理器範本。

   ![樣本部署](media/follower/template-deployment.png)


|**設定**  |**說明**  |
|---------|---------|
|關注者群組名稱     |  跟隨者群集的名稱;範本的部署位置。  |
|附加資料庫設定名稱    |    附加的資料庫配置物件的名稱。 該名稱可以是群集級別上唯一的任何字串。     |
|資料庫名稱     |      要遵循的資料庫的名稱。 如果要跟蹤領導者的所有資料庫,請使用"*"。   |
|領導者群集資源識別碼    |   引線群集的資源 ID。      |
|預設主體修改類型    |   默認主體修改類型。 可以是`Union`或`Replace``None`。 有關預設主體修改類型的詳細資訊,請參閱[主體修改類控制命令](kusto/management/cluster-follower.md#alter-follower-database-principals-modification-kind)。      |
|Location   |   所有資源的位置。 領導者和跟隨者必須位於同一位置。       |
 
### <a name="verify-that-the-database-was-successfully-attached"></a>驗證資料庫是否已成功連線

要驗證資料庫是否已成功連接,請在[Azure 門戶](https://portal.azure.com)中找到附加的資料庫。 

1. 瀏覽到跟隨者群集並選擇**資料庫**
1. 在資料庫清單中搜索新的唯讀資料庫。

    ![唯讀跟隨器資料庫](media/follower/read-only-follower-database.png)

或者：

1. 導覽引線叢集並選擇**資料庫**
2. 檢查相關資料庫已標記為 **"共用與其他人** > **共用是"**

    ![讀取及寫入附加資料庫](media/follower/read-write-databases-shared.png)

## <a name="detach-the-follower-database-using-c"></a>使用 C 分離跟隨者資料庫# 

### <a name="detach-the-attached-follower-database-from-the-follower-cluster"></a>將附加的跟隨者資料庫從跟隨器群集中分離出來

跟隨者群集可以按如下方式分離任何附加的資料庫:

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var leaderSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var followerSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new KustoManagementClient(serviceCreds){
    SubscriptionId = followerSubscriptionId
};

var followerResourceGroupName = "testrg";
//The cluster and database that are created as part of the prerequisites
var followerClusterName = "follower";
var attachedDatabaseConfigurationsName = "uniqueName";

resourceManagementClient.AttachedDatabaseConfigurations.Delete(followerResourceGroupName, followerClusterName, attachedDatabaseConfigurationsName);
```

### <a name="detach-the-attached-follower-database-from-the-leader-cluster"></a>從引線群集分離附加的跟隨者資料庫

引線群集可以按如下方式分離任何附加的資料庫:

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var leaderSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var followerSubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new KustoManagementClient(serviceCreds){
    SubscriptionId = leaderSubscriptionId
};

var leaderResourceGroupName = "testrg";
var followerResourceGroupName = "followerResouceGroup";
var leaderClusterName = "leader";
var followerClusterName = "follower";
//The cluster and database that are created as part of the Prerequisites
var followerDatabaseDefinition = new FollowerDatabaseDefinition()
    {
        AttachedDatabaseConfigurationName = "uniqueName",
        ClusterResourceId = $"/subscriptions/{followerSubscriptionId}/resourceGroups/{followerResourceGroupName}/providers/Microsoft.Kusto/Clusters/{followerClusterName}"
    };

resourceManagementClient.Clusters.DetachFollowerDatabases(leaderResourceGroupName, leaderClusterName, followerDatabaseDefinition);
```

## <a name="detach-the-follower-database-using-python"></a>使用 Python 分離跟隨者資料庫

### <a name="detach-the-attached-follower-database-from-the-follower-cluster"></a>將附加的跟隨者資料庫從跟隨器群集中分離出來

跟隨者群集可以按如下方式分離任何附加的資料庫:

```python
from azure.mgmt.kusto import KustoManagementClient
from azure.common.credentials import ServicePrincipalCredentials
import datetime

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
follower_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, follower_subscription_id)

follower_resource_group_name = "followerResouceGroup"
follower_cluster_name = "follower"
attached_database_configurationName = "uniqueName"

#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.attached_database_configurations.delete(follower_resource_group_name, follower_cluster_name, attached_database_configurationName)
```

### <a name="detach-the-attached-follower-database-from-the-leader-cluster"></a>從引線群集分離附加的跟隨者資料庫

引線群集可以按如下方式分離任何附加的資料庫:

```python

from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import FollowerDatabaseDefinition
from azure.common.credentials import ServicePrincipalCredentials
import datetime

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
follower_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
leader_subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, follower_subscription_id)

follower_resource_group_name = "followerResourceGroup"
leader_resource_group_name = "leaderResourceGroup"
follower_cluster_name = "follower"
leader_cluster_name = "leader"
attached_database_configuration_name = "uniqueName"
location = "North Central US"
cluster_resource_id = "/subscriptions/" + follower_subscription_id + "/resourceGroups/" + follower_resource_group_name + "/providers/Microsoft.Kusto/Clusters/" + follower_cluster_name


#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.clusters.detach_follower_databases(resource_group_name = leader_resource_group_name, cluster_name = leader_cluster_name, cluster_resource_id = cluster_resource_id, attached_database_configuration_name = attached_database_configuration_name)
```

## <a name="manage-principals-permissions-and-caching-policy"></a>管理主體、權限和快取原則

### <a name="manage-principals"></a>管理主體

附加資料庫時,指定 **「默認主體修改類型」。** 預設值是保留[授權主體](kusto/management/access-control/index.md#authorization)的引線資料庫集合

|**種類** |**說明**  |
|---------|---------|
|**Union**     |   附加的資料庫主體將始終包括原始資料庫主體以及添加到關注者資料庫的其他新主體。      |
|**取代**   |    沒有從原始資料庫繼承主體。 必須為附加的資料庫創建新主體。     |
|**None**   |   附加的資料庫主體僅包括原始資料庫的主體,沒有其他主體。      |

有關使用控制命令設定授權主體的詳細資訊,請參閱[用於管理跟隨者叢集的控制命令](kusto/management/cluster-follower.md)。

### <a name="manage-permissions"></a>管理權限

管理唯讀資料庫許可權與所有資料庫類型相同。 請參考[Azure 門戶中的管理權限](/azure/data-explorer/manage-database-permissions#manage-permissions-in-the-azure-portal)。

### <a name="configure-caching-policy"></a>設定快取原則

跟隨者資料庫管理員可以修改附加資料庫或其託管群集上任何表的[快取原則](kusto/management/cache-policy.md)。 默認值是保留資料庫和表級緩存策略的引線資料庫集合。 例如,您可以對引線資料庫具有 30 天的緩存策略,用於運行月度報告,在跟隨者資料庫上使用三天緩存策略,以便僅查詢用於故障排除的最近數據。 有關使用控制命令在跟隨程式資料庫或表上設定快取策略的詳細資訊,請參閱[用於管理跟隨器叢集的控制項命令](kusto/management/cluster-follower.md)。

## <a name="limitations"></a>限制

* 跟隨者和引線群集必須位於同一區域。
* [流引入](/azure/data-explorer/ingest-data-streaming)不能用於正在跟蹤的資料庫。
* 領導者和跟隨者群集不支援使用[客戶託管金鑰](/azure/data-explorer/security#customer-managed-keys-with-azure-key-vault)進行數據加密。 
* 在分離資料庫之前,無法刪除附加到其他群集的資料庫。
* 在分離資料庫之前,不能刪除將資料庫附加到其他群集的群集。
* 不能阻止具有附加跟隨者或引線資料庫的群集。 

## <a name="next-steps"></a>後續步驟

* 有關跟隨器叢集設定的資訊,請參閱[用於管理跟隨器群集的控制項指令](kusto/management/cluster-follower.md)。
