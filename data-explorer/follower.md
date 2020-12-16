---
title: 使用「資料連線資料庫」功能在 Azure 資料總管中附加資料庫
description: 瞭解如何使用「資料庫」資料庫功能，在 Azure 資料總管中附加資料庫。
author: orspod
ms.author: orspodek
ms.reviewer: gabilehner
ms.service: data-explorer
ms.topic: how-to
ms.date: 10/06/2020
ms.openlocfilehash: 37e32e1fe84cd73f268de4400eec529815d3ce8f
ms.sourcegitcommit: 35236fefb52978ce9a09bc36affd5321acb039a4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2020
ms.locfileid: "97514080"
---
# <a name="use-follower-database-to-attach-databases-in-azure-data-explorer"></a>使用資料的資料庫在 Azure 資料總管中附加資料庫

您可以使用「使用中」 **資料庫** 功能，將位於不同叢集中的資料庫連接到您的 Azure 資料總管叢集中。 在唯讀模式中 **，會以***唯讀* 模式附加資料，讓您可以在內嵌至 **領導者資料庫** 的資料上，查看資料並執行查詢。 資料的資料庫會同步處理領導者資料庫中的變更。 由於同步處理的緣故，資料可用性會有幾秒鐘到幾分鐘的資料延遲。 時間延遲的長度取決於領導者資料庫中繼資料的整體大小。 領導人和執行者資料庫會使用相同的儲存體帳戶來提取資料。 儲存體是由領導者資料庫所擁有。 取用資料庫會在不需要內嵌資料的情況下查看資料。 因為附加的資料庫是唯讀資料庫，所以無法修改資料庫中的資料、資料表和原則，但快取 [原則](#configure-caching-policy)、 [主體](#manage-principals)和 [許可權](#manage-permissions)除外。 無法刪除附加的資料庫。 它們必須由領導者或客戶卸離，然後才能刪除。 

使用「進行中」功能將資料庫附加至不同的叢集時，會用來做為在組織和小組之間共用資料的基礎結構。 這項功能有助於隔離計算資源，以保護生產環境免于非生產環境的使用案例。 您也可以用來將 Azure 資料總管叢集的成本與對資料執行查詢的合作物件產生關聯。

## <a name="which-databases-are-followed"></a>遵循哪些資料庫？

* 叢集可以遵循一個資料庫、數個資料庫或領導者叢集的所有資料庫。 
* 單一叢集可以遵循來自多個領導者叢集的資料庫。 
* 叢集可以同時包含可供使用的資料庫和領導者資料庫

## <a name="prerequisites"></a>必要條件

1. 如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。
1. 建立適用于領導人和程式的叢集[和資料庫](create-cluster-database-portal.md)。
1. 使用內嵌[總覽](./ingest-data-overview.md)中所討論的其中一種方法，將[資料](ingest-sample-data.md)內嵌至領導者資料庫。

## <a name="attach-a-database"></a>附加資料庫

您可以使用各種方法來附加資料庫。 在本文中，我們將討論如何使用 c #、Python、Powershell 或 Azure Resource Manager 範本附加資料庫。 若要附加資料庫，您必須擁有使用者、群組、服務主體或受控識別，且在領導者叢集和消費者叢集上至少具有「參與者」角色。 您可以使用 [Azure 入口網站](/azure/role-based-access-control/role-assignments-portal)、 [PowerShell](/azure/role-based-access-control/role-assignments-powershell)、 [Azure CLI](/azure/role-based-access-control/role-assignments-cli) 和 [ARM 範本](/azure/role-based-access-control/role-assignments-template)來新增或移除角色指派。 您可以 (Azure RBAC) 和[不同的角色](/azure/role-based-access-control/rbac-and-directory-admin-roles)，深入瞭解[azure 角色型存取控制](/azure/role-based-access-control/overview)。 


# <a name="c"></a>[C#](#tab/csharp)

### <a name="attach-a-database-using-c"></a>使用 C 附加資料庫#

#### <a name="needed-nugets"></a>需要的 Nuget

* 安裝 [Microsoft.Azure.Management.kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)。
* 安裝 [ClientRuntime 驗證以進行驗證](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication)。

#### <a name="example"></a>範例

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

# <a name="python"></a>[Python](#tab/python)

### <a name="attach-a-database-using-python"></a>使用 Python 附加資料庫

#### <a name="needed-modules"></a>需要的模組

```
pip install azure-common
pip install azure-mgmt-kusto
```

#### <a name="example"></a>範例

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

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

### <a name="attach-a-database-using-powershell"></a>使用 Powershell 附加資料庫

#### <a name="needed-modules"></a>需要的模組

```
Install : Az.Kusto
```

#### <a name="example"></a>範例

```Powershell
$FollowerClustername = 'follower'
$FollowerClusterSubscriptionID = 'xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx'
$FollowerResourceGroupName = 'followerResouceGroup'
$DatabaseName = "db"  ## Can be specific database name or * for all databases
$LeaderClustername = 'leader'
$LeaderClusterSubscriptionID = 'xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx'
$LeaderClusterResourceGroup = 'leaderResouceGroup'
$DefaultPrincipalsModificationKind = 'Union'
##Construct the LeaderClusterResourceId and Location
$getleadercluster = Get-AzKustoCluster -Name $LeaderClustername -ResourceGroupName $LeaderClusterResourceGroup -SubscriptionId $LeaderClusterSubscriptionID -ErrorAction Stop
$LeaderClusterResourceid = $getleadercluster.Id
$Location = $getleadercluster.Location
##Handle the config name if all databases needs to be followed
if($DatabaseName -eq '*')  {
        $configname = $FollowerClustername + 'config'
       } 
else {
        $configname = $DatabaseName   
     }
New-AzKustoAttachedDatabaseConfiguration -ClusterName $FollowerClustername `
    -Name $configname `
    -ResourceGroupName $FollowerResourceGroupName `
    -SubscriptionId $FollowerClusterSubscriptionID `
    -DatabaseName $DatabaseName `
    -ClusterResourceId $LeaderClusterResourceid `
    -DefaultPrincipalsModificationKind $DefaultPrincipalsModificationKind `
    -Location $Location `
    -ErrorAction Stop 
```

# <a name="resource-manager-template"></a>[Resource Manager 範本](#tab/azure-resource-manager)

### <a name="attach-a-database-using-an-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本附加資料庫

在本節中，您將瞭解如何使用 [Azure Resource Manager 範本](/azure/azure-resource-manager/management/overview)，將資料庫附加至現有的叢集。 

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

您可以 [使用 Azure 入口網站](https://portal.azure.com) 或使用 powershell 來部署 Azure Resource Manager 範本。

   ![範本部署](media/follower/template-deployment.png)

|**設定**  |**說明**  |
|---------|---------|
|進行的叢集名稱     |  進行中的叢集名稱;將部署範本的位置。  |
|附加的資料庫設定名稱    |    附加的資料庫設定物件的名稱。 名稱可以是在叢集層級唯一的任何字串。     |
|資料庫名稱     |      要遵循的資料庫名稱。 如果您想要追蹤所有領導者的資料庫，請使用 ' * '。   |
|領導者叢集資源識別碼    |   領導者叢集的資源識別碼。      |
|預設主體修改種類    |   預設的主要修改種類。 可以是 `Union` 、 `Replace` 或 `None` 。 如需預設主體修改種類的詳細資訊，請參閱 [主體修改種類控制命令](kusto/management/cluster-follower.md#alter-follower-database-principals-modification-kind)。      |
|位置   |   所有資源的位置。 領導人和採用者必須位於相同的位置。       |

---

## <a name="verify-that-the-database-was-successfully-attached"></a>確認資料庫已成功附加

若要確認資料庫是否成功附加，請在 [Azure 入口網站](https://portal.azure.com)中尋找您附加的資料庫。 您可以確認已成功將資料庫連接到「成功 [」或「](#check-your-follower-cluster) [領導者](#check-your-leader-cluster) 」叢集中。

### <a name="check-your-follower-cluster"></a>檢查您的對等叢集  

1. 流覽至「移至」叢集，然後選取 [**資料庫**]
1. 在 [資料庫] 清單中搜尋新的唯讀資料庫。

    ![唯讀的資料從資料庫](media/follower/read-only-follower-database.png)

### <a name="check-your-leader-cluster"></a>檢查您的領導者叢集

1. 流覽至領導者叢集，然後選取 [**資料庫**]
2. 檢查相關的資料庫是否已標示為 **與其他人共用** 的資料庫  >  **是**

    ![讀取和寫入附加的資料庫](media/follower/read-write-databases-shared.png)

## <a name="detach-the-follower-database"></a>卸離的資料庫  

> [!NOTE]
> 若要將資料庫從從上或領導人卸離，您必須擁有使用者、群組、服務主體或受控識別，而且在您要從中卸離資料庫的叢集上必須有至少的參與者角色。 在下列範例中，我們會使用服務主體。

# <a name="c"></a>[C#](#tab/csharp)

### <a name="detach-the-attached-follower-database-from-the-follower-cluster-using-c"></a>使用 C 將連接的資料從從的資料庫卸離#


您可以如下所示，將任何附加的資料連線資料庫卸離：

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

### <a name="detach-the-attached-follower-database-from-the-leader-cluster-using-c"></a>使用 C 從領導者叢集卸離連結的資料連線資料庫#

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

# <a name="python"></a>[Python](#tab/python)

### <a name="detach-the-attached-follower-database-from-the-follower-cluster-using-python"></a>使用 Python 將連接的資料從從中移除的資料庫卸離

您可以依照下列方式，將任何附加的資料庫卸離：

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

### <a name="detach-the-attached-follower-database-from-the-leader-cluster-using-python"></a>使用 Python 從領導者叢集卸離連結的資料連線資料庫

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

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

### <a name="detach-a-database-using-powershell"></a>使用 Powershell 卸離資料庫

#### <a name="needed-modules"></a>需要的模組

```
Install : Az.Kusto
```

#### <a name="example"></a>範例

```Powershell
$FollowerClustername = 'follower'
$FollowerClusterSubscriptionID = 'xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx'
$FollowerResourceGroupName = 'followerResouceGroup'
$DatabaseName = "sanjn"  ## Can be specific database name or * for all databases

##Construct the Configuration name 
$confignameraw = (Get-AzKustoAttachedDatabaseConfiguration -ClusterName $FollowerClustername -ResourceGroupName $FollowerResourceGroupName -SubscriptionId $FollowerClusterSubscriptionID) | Where-Object {$_.DatabaseName -eq $DatabaseName }
$configname =$confignameraw.Name.Split("/")[1]

Remove-AzKustoAttachedDatabaseConfiguration -ClusterName $FollowerClustername -Name $configname -ResourceGroupName $FollowerResourceGroupName
```

# <a name="resource-manager-template"></a>[Resource Manager 範本](#tab/azure-resource-manager)

使用 c #、Python 或 PowerShell 卸[離資料的資料庫](#detach-the-follower-database)。

---

## <a name="manage-principals-permissions-and-caching-policy"></a>管理主體、許可權和快取原則

### <a name="manage-principals"></a>管理主體

附加資料庫時，請指定「 **預設主體修改種類**」。 預設會保留已[授權主體](kusto/management/access-control/index.md#authorization)的領導者資料庫集合

|**種類** |**說明**  |
|---------|---------|
|**Union**     |   附加的資料庫主體一律會包含原始資料庫主體，再加上其他新增至 [資料] 資料庫的新主體。      |
|**Replace**   |    沒有繼承自原始資料庫的主體。 必須針對附加的資料庫建立新的主體。     |
|**無**   |   附加的資料庫主體只包含原始資料庫的主體，而不含其他主體。      |

如需使用控制命令設定授權主體的詳細資訊，請參閱 [控制管理者叢集的控制命令](kusto/management/cluster-follower.md)。

### <a name="manage-permissions"></a>管理權限

管理唯讀資料庫許可權與所有資料庫類型的管理方式相同。 請參閱 [Azure 入口網站中的管理許可權](manage-database-permissions.md#manage-permissions-in-the-azure-portal)。

### <a name="configure-caching-policy"></a>設定快取原則

資料的資料庫管理員可以修改所連接資料庫的快取 [原則](kusto/management/cache-policy.md) ，或其裝載叢集上的任何資料表。 預設為保留資料庫和資料表層級快取原則的領導者資料庫集合。 例如，您可以在領導者資料庫上有30天的快取原則，以便執行每月報告，以及在移出資料庫上使用三天的快取原則，以查詢最新的資料進行疑難排解。 如需使用控制命令來設定資料的資料庫或資料表的快取原則的詳細資訊，請參閱控制管理資料叢集的 [控制命令](kusto/management/cluster-follower.md)。

## <a name="notes"></a>備忘錄

* 如果領導人/執行程式群集的資料庫之間發生衝突，則當所有資料庫後面都有一個執行的叢集時，它們會以下列方式解決：
  * 在「取用者」叢 *集中建立的資料庫名稱會* 優先于在領導者叢集上建立相同名稱的資料庫。 因此，您必須將移入叢集的資料庫 *db* (或重新命名) ，才能讓該進行中的叢集擁有領導者的資料庫 *db*。
  * 系統會從其中一個領導者叢集中任意選擇一個名為 *DB* 的資料庫，然後再從 *其中一個* 領導者叢集中選擇，不會再執行一次以上。
* 用來顯示叢集 [活動記錄和](kusto/management/systeminfo.md) 執行歷程記錄的命令，將會顯示在執行程式叢集上的活動和歷程記錄，而其結果集將不會包含領導人叢集 (s 的) 。
  * 例如：在執行 `.show queries` 程式叢集上執行的命令只會顯示在資料庫上執行的查詢，並在後面接著執行的資料，而不會對領導者叢集中的相同資料庫執行查詢。
  
## <a name="limitations"></a>限制

* 合作的和領導人叢集必須位於相同的區域中。
* [串流](ingest-data-streaming.md) 內嵌無法用於所要遵循的資料庫。
* 使用 [客戶管理的金鑰](security.md#customer-managed-keys-with-azure-key-vault) 進行資料加密，不支援在領導者和消費者叢集上使用。 
* 卸離之前，您無法刪除附加至不同叢集的資料庫。
* 在卸離資料庫之前，您無法刪除有附加至不同叢集之資料庫的叢集。

## <a name="next-steps"></a>後續步驟

* 如需有關「進行」叢集設定的詳細資訊，請參閱 [控制管理資料叢集的控制命令](kusto/management/cluster-follower.md)。
