---
title: 使用 Python 為 Azure 資料資源管理員建立 IoT 中心資料連線
description: 在本文中,您將瞭解如何使用 Python 為 Azure 資料資源管理器創建 IoT 中心數據連接。
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 4357e53a36274aaefdfc379c73a0f2dc1e0e2bbc
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81497677"
---
# <a name="create-an-iot-hub-data-connection-for-azure-data-explorer-by-using-python-preview"></a>使用 Python(預覽)為 Azure 資料資源管理員建立 IoT 中心資料連線

> [!div class="op_single_selector"]
> * [入口網站](ingest-data-iot-hub.md)
> * [C#](data-connection-iot-hub-csharp.md)
> * [Python](data-connection-iot-hub-python.md)
> * [Azure Resource Manager 範本](data-connection-iot-hub-resource-manager.md)

在本文中,可以使用 Python 為 Azure 資料資源管理器創建 IoT 中心數據連接。 Azure 資料總管是一項快速又可高度調整的資料探索服務，可用於處理記錄和遙測資料。 Azure 資料資源管理員提供從事件中心、IoT 中心和寫入 Blob 容器的 Blob 的引入或數據載入。

## <a name="prerequisites"></a>Prerequisites

* 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

* [Python 3.4+](https://www.python.org/downloads/)。

* [叢集與資料庫](create-cluster-database-python.md)。

* [表和列對應](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)。

* [資料庫和表策略](database-table-policies-python.md)(可選)。

* [設定了分享存取策略的 IoT 中心](ingest-data-iot-hub.md#create-an-iot-hub)。

[!INCLUDE [data-explorer-data-connection-install-package-python](includes/data-explorer-data-connection-install-package-python.md)]

[!INCLUDE [data-explorer-authentication](includes/data-explorer-authentication.md)]

## <a name="add-an-iot-hub-data-connection"></a>新增 IoT 中心資料連線 

下面的範例展示如何以程式設計方式添加IoT中心數據連接。 有關使用 Azure 門戶新增 Iot 中心資料連線,請參閱[將 Azure 資料資源管理員表連接到 IoT 中心](ingest-data-iot-hub.md#connect-azure-data-explorer-table-to-iot-hub)。

```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import IotHubDataConnection
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
#The cluster and database that are created as part of the Prerequisites
cluster_name = "mykustocluster"
database_name = "mykustodatabase"
data_connection_name = "myeventhubconnect"
#The IoT hub that is created as part of the Prerequisites
iot_hub_resource_id = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Devices/IotHubs/xxxxxx";
shared_access_policy_name = "iothubforread"
consumer_group = "$Default"
location = "Central US"
#The table and column mapping that are created as part of the Prerequisites
table_name = "StormEvents"
mapping_rule_name = "StormEvents_CSV_Mapping"
data_format = "csv"

#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.data_connections.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name, data_connection_name=data_connection_name,
                                            parameters=IotHubDataConnection(iot_hub_resource_id=iot_hub_resource_id, shared_access_policy_name=shared_access_policy_name, 
                                                                                consumer_group=consumer_group, table_name=table_name, location=location, mapping_rule_name=mapping_rule_name, data_format=data_format))
```

|**設定** | **建議的值** | **欄位描述**|
|---|---|---|
| tenant_id | *xxxxxx-xxxxxx-xxx-xxx-xxxxxxxxx* | 您的租用戶識別碼。 也稱為目錄 ID。|
| subscriptionId | *xxxxxx-xxxxxx-xxx-xxx-xxxxxxxxx* | 用於資源創建的訂閱 ID。|
| client_id | *xxxxxx-xxxxxx-xxx-xxx-xxxxxxxxx* | 可以訪問租戶中資源的應用程式的客戶端 ID。|
| client_secret | *xxxxxxxxxxxxxxx* | 可以訪問租戶中資源的應用程式的用戶端機密。 |
| resource_group_name | *testrg* | 包含群集的資源組的名稱。|
| cluster_name | *mykustocluster* | 群集的名稱。|
| database_name | *mykustodatabase* | 群集中的目標資料庫的名稱。|
| data_connection_name | *myeventhubconnect* | 數據連接的所需名稱。|
| table_name | *風暴事件* | 目標資料庫中的目標表的名稱。|
| mapping_rule_name | *StormEvents_CSV_Mapping* | 與目標表相關的列映射的名稱。|
| data_format | *Csv* | 消息的數據格式。|
| iot_hub_resource_id | *資源識別碼* | 保存要引入的數據的 IoT 中心的資源 ID。|
| shared_access_policy_name | *伊烏特福雷德* | 定義設備和服務連接到 IoT 中心的許可權的共享存取策略的名稱。 |
| consumer_group | *$Default* | 事件中心的消費者組。|
| location | *美國中部* | 數據連接資源的位置。|

[!INCLUDE [data-explorer-data-connection-clean-resources-python](includes/data-explorer-data-connection-clean-resources-python.md)]
