---
title: 使用 Python 為 Azure 資料資源管理員加入資料庫主體
description: 在本文中,您將瞭解如何使用 Python 為 Azure 資料資源管理器添加資料庫主體。
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 3ddb34692158fdc7e6d4f7786f84b8c059bc0621
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81498691"
---
# <a name="add-database-principals-for-azure-data-explorer-by-using-python"></a>使用 Python 為 Azure 資料資源管理員加入資料庫主體

> [!div class="op_single_selector"]
> * [C#](database-principal-csharp.md)
> * [Python](database-principal-python.md)
> * [Azure Resource Manager 範本](database-principal-resource-manager.md)

Azure 資料總管是一項快速又可高度調整的資料探索服務，可用於處理記錄和遙測資料。 在本文中,可以使用 Python 為 Azure 資料資源管理器添加資料庫主體。

## <a name="prerequisites"></a>Prerequisites

* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。
* [建立叢集和資料庫](create-cluster-database-python.md)

## <a name="install-python-package"></a>安裝 Python 套件

若要為 Azure 資料總管 (Kusto) 安裝 Python 套件，請開啟在其路徑中有 Python 的命令提示字元。 請執行這個命令：

```
pip install azure-common
pip install azure-mgmt-kusto
```

[!INCLUDE [data-explorer-authentication](includes/data-explorer-authentication.md)]

## <a name="add-a-database-principal"></a>新增資料庫主體

下面的範例展示如何以程式設計方式添加資料庫主體。

```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import DatabasePrincipalAssignment
from azure.common.credentials import ServicePrincipalCredentials

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

resource_group_name = "testrg"
#The cluster and database that is created as part of the Prerequisites
cluster_name = "mykustocluster"
database_name = "mykustodatabase"
principal_assignment_name = "clusterPrincipalAssignment1"
#User email, application ID, or security group name
principal_id = "xxxxxxxx"
#AllDatabasesAdmin or AllDatabasesViewer
role = "AllDatabasesAdmin"
tenant_id_for_principal = tenantId
#User, App, or Group
principal_type = "App"

#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.database_principal_assignments.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name, principal_assignment_name= principal_assignment_name, parameters=DatabasePrincipalAssignment(principal_id=principal_id, role=role, tenant_id=tenant_id_for_principal, principal_type=principal_type))
```

|**設定** | **建議的值** | **欄位描述**|
|---|---|---|
| tenant_id | *xxxxxx-xxxxxx-xxx-xxx-xxxxxxxxx* | 您的租用戶識別碼。 也稱為目錄 ID。|
| subscription_id | *xxxxxx-xxxxxx-xxx-xxx-xxxxxxxxx* | 用於資源創建的訂閱 ID。|
| client_id | *xxxxxx-xxxxxx-xxx-xxx-xxxxxxxxx* | 可以訪問租戶中資源的應用程式的客戶端 ID。|
| client_secret | *xxxxxxxxxxxxxxx* | 可以訪問租戶中資源的應用程式的用戶端機密。 |
| resource_group_name | *testrg* | 包含群集的資源組的名稱。|
| cluster_name | *mykustocluster* | 群集的名稱。|
| database_name | *mykustodatabase* | 您的資料庫名稱。|
| principal_assignment_name | *資料庫主體配置1* | 資料庫主體資源的名稱。|
| principal_id | *xxxxxx-xxxxxx-xxx-xxx-xxxxxxxxx* | 主體ID,可以是使用者電子郵件、應用程式ID或安全組名稱。|
| 角色 (role) | *管理員* | 資料庫主體的角色,可以是"管理員","Ingestor","監視器","使用者","無限制查看器","查看器"。|
| tenant_id_for_principal | *xxxxxx-xxxxxx-xxx-xxx-xxxxxxxxx* | 主體的租戶 ID。|
| principal_type | *應用程式* | 主體的類型,可以是"使用者"、"應用"或"組"|

## <a name="next-steps"></a>後續步驟

* [使用 Azure 資料資源管理員 Python 函式庫引入資料](python-ingest-data.md)
