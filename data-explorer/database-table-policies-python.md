---
title: '使用 Azure 資料總管 Python 程式庫建立 Azure 資料總管叢集和資料庫的原則 '
description: 在本文中，您將瞭解如何使用 Python 建立原則。
author: orspod
ms.author: orspodek
ms.reviewer: lugoldbe
ms.service: data-explorer
ms.topic: how-to
ms.date: 09/24/2019
ms.openlocfilehash: 8febe9934234370b217ab4f5a2591fd0b5adb759
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88873010"
---
# <a name="create-database-and-table-policies-for-azure-data-explorer-by-using-python"></a>使用 Python 建立 Azure 資料總管的資料庫和資料表原則

> [!div class="op_single_selector"]
> * [C#](database-table-policies-csharp.md)
> * [Python](database-table-policies-python.md)
>

Azure 資料總管是一項快速又可高度調整的資料探索服務，可用於處理記錄和遙測資料。 在本文中，您會使用 Python 建立 Azure 資料總管的資料庫和資料表原則。

## <a name="prerequisites"></a>Prerequisites

* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。
* [測試叢集和資料庫](create-cluster-database-python.md)
* [測試資料表](python-ingest-data.md#create-a-table-on-your-cluster)

## <a name="install-the-data-libraries"></a>安裝資料連結庫

```
pip install azure-common
pip install azure-mgmt-kusto
pip install azure-kusto-data (Optional, for changing table's policies)
```

## <a name="authentication"></a>驗證
若要執行本文中的範例，我們需要可存取資源 Azure AD 應用程式和服務主體。 您可以使用相同的 Azure AD 應用程式，從 [測試叢集和資料庫](create-cluster-database-csharp.md#authentication)進行驗證。 如果您想要使用不同的 Azure AD 應用程式，請參閱 [建立 Azure AD 應用](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) 程式，以建立免費的 Azure AD 應用程式，並在訂用帳戶範圍新增角色指派。 它也會顯示如何取得 `Directory (tenant) ID` 、 `Application ID` 和 `Client Secret` 。 您可能需要將新的 Azure AD 應用程式新增為資料庫中的主體，請參閱 [管理 Azure 資料總管資料庫許可權](manage-database-permissions.md)。    

## <a name="alter-database-retention-policy"></a>Alter database 保留原則
設定包含10天虛刪除期間的保留原則。

```python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import DatabaseUpdate
from azure.common.credentials import ServicePrincipalCredentials
import datetime

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, subscription_id)

resource_group_name = "testrg";
#The cluster and database that are created as part of the Prerequisites
cluster_name = "mykustocluster";
database_name = "mykustodatabase";

#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kustoManagementClient.databases.update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name,
                                           parameters=DatabaseUpdate(soft_delete_period=datetime.timedelta(days=10)))
```

## <a name="alter-database-cache-policy"></a>Alter database cache 原則
設定資料庫的快取原則，最後五天的資料將會在叢集 SSD 上。

```python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import DatabaseUpdate
from azure.common.credentials import ServicePrincipalCredentials
import datetime

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, subscription_id)

resource_group_name = "testrg";
#The cluster and database that are created as part of the Prerequisites
cluster_name = "mykustocluster";
database_name = "mykustodatabase";

#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kustoManagementClient.databases.update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name,
                                           parameters=DatabaseUpdate(hot_cache_period=datetime.timedelta(days=5)))
```

## <a name="alter-table-cache-policy"></a>Alter table cache 原則
設定資料表的快取原則，最後五天的資料將會在叢集 SSD 上。

```python
from azure.kusto.data.request import KustoClient, KustoConnectionStringBuilder
kusto_uri = "https://<ClusterName>.<Region>.kusto.windows.net"
database_name = "<DatabaseName>"
#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
#Application ID
client_id_to_add = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

kusto_connection_string_builder = KustoConnectionStringBuilder.with_aad_application_key_authentication(connection_string=kusto_uri, aad_app_id=client_id, app_key=client_secret, authority_id=tenant_id)

#The table that is created as part of the Prerequisites
table_name = "<TableName>"
caching_policy = r'hot = 5d'
command = '.alter table {} policy caching '.format(table_name) +  caching_policy
kusto_client.execute_mgmt(database_name, command)
```

## <a name="add-a-new-principal-for-database"></a>為資料庫新增主體
將新的 Azure AD 應用程式新增為資料庫的系統管理員主體

```python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import DatabasePrincipal
from azure.common.credentials import ServicePrincipalCredentials

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
#Application ID
client_id_to_add = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, subscription_id)

resource_group_name = "testrg";
#The cluster and database that are created as part of the Prerequisites
cluster_name = "mykustocluster";
database_name = "mykustodatabase";
role = "Admin"
principle_name = "<database_principle_name>";
type_name = "App"
kustoManagementClient.databases.add_principals(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name,
                         value=[DatabasePrincipal(role=role, name=principle_name, type=type_name, app_id=client_id_to_add, tenant_name=tenant_id)])
```

## <a name="next-steps"></a>後續步驟

* [閱讀有關資料庫和資料表原則的詳細資訊](kusto/management/policies.md)
