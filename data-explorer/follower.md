---
title: 使用資料的資料庫功能，在 Azure 中附加資料庫資料總管
description: 瞭解如何使用「資料後資料庫」功能，在 Azure 資料總管中附加資料庫。
author: orspod
ms.author: orspodek
ms.reviewer: gabilehner
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: b1855d12872c291e1ae45f62da1ad1fe0e792617
ms.sourcegitcommit: aacea5c4c397479e8254c1fe6ed0b2f333307b14
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86470106"
---
# <a name="use-follower-database-to-attach-databases-in-azure-data-explorer"></a>使用在 Azure 資料總管中的資料，來連接資料庫

「後置**資料庫**」功能可讓您將位於不同叢集中的資料庫附加至您的 Azure 資料總管叢集。 「執行程式」**資料庫**是以*唯讀*模式附加，因此您可以在內嵌至**領導者資料庫**的資料上，查看資料並執行查詢。 「資料後」資料庫會同步處理領導者資料庫中的變更。 由於同步處理，資料可用性會有幾秒鐘到幾分鐘的資料延遲。 時間延遲的長度取決於領導者資料庫中繼資料的整體大小。 領導者和執行者資料庫會使用相同的儲存體帳戶來提取資料。 儲存區是由領導者資料庫所擁有。 「取用的資料庫」會在不需要內嵌資料的情況下，就能流覽資料。 因為附加的資料庫是唯讀資料庫，所以無法修改資料庫中的資料、資料表和原則，但快取[原則](#configure-caching-policy)、[主體](#manage-principals)和[許可權](#manage-permissions)除外。 無法刪除附加的資料庫。 它們必須由領導者或其他人卸離，然後才可以刪除。 

使用「進行中」功能將資料庫附加至不同的叢集，是用來做為基礎結構，以便在組織與小組之間共用資料。 此功能適用于隔離計算資源，以保護生產環境不受非生產使用案例的限制。 您也可以將 Azure 資料總管叢集的成本與對資料執行查詢的合作物件建立關聯。

## <a name="which-databases-are-followed"></a>會遵循哪些資料庫？

* 叢集可以遵循一個資料庫、數個資料庫或領導者叢集的所有資料庫。 
* 單一叢集可以遵循來自多個領導者叢集的資料庫。 
* 叢集可以同時包含進行中的資料庫和領導者資料庫

## <a name="prerequisites"></a>必要條件

1. 如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。
1. 為領導者和進行中[建立叢集和資料庫](create-cluster-database-portal.md)。
1. 使用內嵌[總覽](/azure/data-explorer/ingest-data-overview)中所討論的各種方法之一，將[資料](ingest-sample-data.md)內嵌至領導者資料庫。

## <a name="attach-a-database"></a>附加資料庫

您可以使用各種方法來附加資料庫。 在本文中，我們會討論如何使用 c #、Python 或 Azure Resource Manager 範本附加資料庫。 若要附加資料庫，您必須擁有領導者叢集和消費者叢集上至少具有參與者角色的使用者、群組、服務主體或受控識別。 您可以使用[Azure 入口網站](/azure/role-based-access-control/role-assignments-portal)、 [PowerShell](/azure/role-based-access-control/role-assignments-powershell)、 [Azure CLI](/azure/role-based-access-control/role-assignments-cli)和[ARM 範本](/azure/role-based-access-control/role-assignments-template)來新增或移除角色指派。 您可以深入瞭解[Azure 角色型存取控制（AZURE RBAC）](/azure/role-based-access-control/overview)和[不同的角色](/azure/role-based-access-control/rbac-and-directory-admin-roles)。 

### <a name="attach-a-database-using-c"></a>使用 C 附加資料庫#

#### <a name="needed-nugets"></a>必要的 Nuget

* 請安裝[kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)。
* 安裝[ClientRuntime 以進行驗證](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication)。

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

### <a name="attach-a-database-using-python"></a>使用 Python 附加資料庫

#### <a name="needed-modules"></a>需要的模組

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

### <a name="attach-a-database-using-an-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本附加資料庫

在本節中，您會瞭解如何使用[Azure Resource Manager 範本](/azure/azure-resource-manager/management/overview)，將資料庫附加至現有的叢集。 

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
            "apiVersion": "2020-02-15",
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

您可以[使用 Azure 入口網站](https://portal.azure.com)或使用 powershell 來部署 Azure Resource Manager 範本。

   ![範本部署](media/follower/template-deployment.png)


|**設定**  |**說明**  |
|---------|---------|
|進行中的叢集名稱     |  進行中的叢集名稱;將部署範本的位置。  |
|附加的資料庫設定名稱    |    附加的資料庫設定物件的名稱。 此名稱可以是在叢集層級上唯一的任何字串。     |
|資料庫名稱     |      要遵循的資料庫名稱。 如果您想要追蹤所有領導者的資料庫，請使用 ' * '。   |
|領導者叢集資源識別碼    |   領導者叢集的資源識別碼。      |
|預設主體修改種類    |   預設的主體修改種類。 可以是 `Union` 、 `Replace` 或 `None` 。 如需預設主體修改種類的詳細資訊，請參閱[principal 修改種類控制命令](kusto/management/cluster-follower.md#alter-follower-database-principals-modification-kind)。      |
|Location   |   所有資源的位置。 領導人和進行者必須位於相同的位置。       |
 
### <a name="verify-that-the-database-was-successfully-attached"></a>確認資料庫已成功附加

若要確認資料庫是否已成功附加，請在[Azure 入口網站](https://portal.azure.com)中尋找您附加的資料庫。 

1. 流覽至 [進行中] 叢集並選取 [**資料庫**]
1. 在 [資料庫] 清單中搜尋新的唯讀資料庫。

    ![唯讀的進行中資料庫](media/follower/read-only-follower-database.png)

或者：

1. 流覽至領導者叢集並選取 [**資料庫**]
2. 檢查相關資料庫是否標示為**與其他人共用**  >  **[是]**

    ![讀取和寫入附加的資料庫](media/follower/read-write-databases-shared.png)

## <a name="detach-the-follower-database-using-c"></a>使用 C 卸離資料的後向資料庫# 

### <a name="detach-the-attached-follower-database-from-the-follower-cluster"></a>卸離已連接的資料來源資料庫與進行中的叢集

進行中的叢集可以卸離任何附加的資料庫，如下所示：

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

若要從第一方卸離資料庫，您必須具有使用者、群組、服務主體，或在使用中叢集至少具有「參與者」角色的受控識別。
在上述範例中，我們使用服務主體。

### <a name="detach-the-attached-follower-database-from-the-leader-cluster"></a>從領導者叢集卸離附加的資料來源資料庫

領導者叢集可以卸離任何附加的資料庫，如下所示：

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

若要從領導者端卸離資料庫，您必須在領導者叢集上擁有具有至少參與者角色的使用者、群組、服務主體或受控識別。 在上述範例中，我們使用服務主體。

## <a name="detach-the-follower-database-using-python"></a>使用 Python 卸離資料的後向資料庫

### <a name="detach-the-attached-follower-database-from-the-follower-cluster"></a>卸離已連接的資料來源資料庫與進行中的叢集

進行中的叢集可以卸離任何附加的資料庫，如下所示：

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
若要從第一方卸離資料庫，您必須具有使用者、群組、服務主體，或在使用中叢集至少具有「參與者」角色的受控識別。
在上述範例中，我們使用服務主體。

### <a name="detach-the-attached-follower-database-from-the-leader-cluster"></a>從領導者叢集卸離附加的資料來源資料庫

領導者叢集可以卸離任何附加的資料庫，如下所示：

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

若要從領導者端卸離資料庫，您必須在領導者叢集上擁有具有至少參與者角色的使用者、群組、服務主體或受控識別。
在上述範例中，我們使用服務主體。

## <a name="manage-principals-permissions-and-caching-policy"></a>管理主體、許可權和快取原則

### <a name="manage-principals"></a>管理主體

附加資料庫時，請指定「**預設主體修改種類**」。 預設會保留已[授權主體](kusto/management/access-control/index.md#authorization)的領導者資料庫集合

|**種類** |**說明**  |
|---------|---------|
|**Union**     |   附加的資料庫主體一律會包含原始資料庫主體，加上加入至資料後資料庫的額外新主體。      |
|**取代**   |    不會繼承原始資料庫的主體。 必須為附加的資料庫建立新的主體。     |
|**無**   |   附加的資料庫主體只包含原始資料庫的主體，且沒有其他主體。      |

如需使用控制命令來設定授權主體的詳細資訊，請參閱[控制管理使用](kusto/management/cluster-follower.md)中叢集的命令。

### <a name="manage-permissions"></a>管理權限

管理唯讀資料庫許可權與所有資料庫類型相同。 請參閱[管理 Azure 入口網站中的許可權](manage-database-permissions.md#manage-permissions-in-the-azure-portal)。

### <a name="configure-caching-policy"></a>設定快取原則

「後向資料庫管理員」可以在主控叢集上修改附加資料庫或其任何資料表的快取[原則](kusto/management/cache-policy.md)。 預設值是保留資料庫和資料表層級快取原則的領導者資料庫集合。 例如，您可以在領導者資料庫上有30天的快取原則來執行每月報表，並在執行者資料庫上使用三天的快取原則，只查詢最近的資料進行疑難排解。 如需使用控制命令在資料後資料庫或資料表上設定快取原則的詳細資訊，請參閱[控制用於管理](kusto/management/cluster-follower.md)資料型叢集的命令。

## <a name="limitations"></a>限制

* 「進行中」和「領導者」叢集必須位於相同的區域。
* [資料流程](ingest-data-streaming.md)內嵌無法用於所遵循的資料庫。
* 在領導者和後向叢集上不支援使用[客戶管理的金鑰](security.md#customer-managed-keys-with-azure-key-vault)進行資料加密。 
* 卸離另一個叢集之前，您無法刪除該資料庫。
* 卸離資料庫之前，您無法刪除已附加至不同叢集的叢集。

## <a name="next-steps"></a>後續步驟

* 如需有關「進行中」叢集設定的詳細資訊，請參閱[控制管理使用](kusto/management/cluster-follower.md)中叢集的命令。
