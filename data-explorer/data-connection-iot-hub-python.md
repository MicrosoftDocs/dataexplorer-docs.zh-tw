---
title: 使用 Python 建立適用于 Azure 資料總管的 IoT 中樞資料連線
description: 在本文中，您將瞭解如何使用 Python 建立 Azure 資料總管的 IoT 中樞資料連線。
author: orspod
ms.author: orspodek
ms.reviewer: lugoldbe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 89fc499e0e74bcd521c8c2fbc7c2038536c097da
ms.sourcegitcommit: f7f3ecef858c1e8d132fc10d1e240dcd209163bd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2020
ms.locfileid: "88201569"
---
# <a name="create-an-iot-hub-data-connection-for-azure-data-explorer-by-using-python-preview"></a>使用 Python (Preview 建立適用于 Azure 資料總管的 IoT 中樞資料連線) 

> [!div class="op_single_selector"]
> * [入口網站](ingest-data-iot-hub.md)
> * [C#](data-connection-iot-hub-csharp.md)
> * [Python](data-connection-iot-hub-python.md)
> * [Azure Resource Manager 範本](data-connection-iot-hub-resource-manager.md)

[!INCLUDE [data-connector-intro](includes/data-connector-intro.md)]
在本文中，您會使用 Python 建立適用于 Azure 資料總管的 IoT 中樞資料連線。 

## <a name="prerequisites"></a>必要條件

* 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
* [Python 3.4+](https://www.python.org/downloads/)。
* 叢集[和資料庫](create-cluster-database-python.md)。
* [資料表和資料行對應](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)。
* [資料庫和資料表原則](database-table-policies-python.md) (選擇性) 。
* [已設定共用存取原則的 IoT 中樞](ingest-data-iot-hub.md#create-an-iot-hub)。

[!INCLUDE [data-explorer-data-connection-install-package-python](includes/data-explorer-data-connection-install-package-python.md)]

[!INCLUDE [data-explorer-authentication](includes/data-explorer-authentication.md)]

## <a name="add-an-iot-hub-data-connection"></a>新增 IoT 中樞資料連線 

下列範例會示範如何以程式設計方式加入 IoT 中樞資料連線。 請參閱 [將 Azure 資料總管資料表連線到 IoT 中樞](ingest-data-iot-hub.md#connect-azure-data-explorer-table-to-iot-hub) ，以使用 Azure 入口網站來新增 IoT 中樞資料連線。

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
| tenant_id | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxxx-xxxx-xxxxxxxxx* | 您的租用戶識別碼。 也稱為目錄識別碼。|
| subscriptionId | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxxx-xxxx-xxxxxxxxx* | 您用來建立資源的訂用帳戶識別碼。|
| client_id | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxxx-xxxx-xxxxxxxxx* | 應用程式的用戶端識別碼，可存取您租使用者中的資源。|
| client_secret | *xxxxxxxxxxxxxx* | 應用程式的用戶端密碼，可以存取您租使用者中的資源。 |
| resource_group_name | *testrg* | 包含您叢集的資源組名。|
| cluster_name | *mykustocluster* | 叢集的名稱。|
| database_name | *mykustodatabase* | 叢集中的目標資料庫名稱。|
| data_connection_name | *myeventhubconnect* | 所需的資料連線名稱。|
| table_name | *StormEvents* | 目標資料庫中目標資料表的名稱。|
| mapping_rule_name | *StormEvents_CSV_Mapping* | 與目標資料表相關之資料行對應的名稱。|
| data_format | *csv* | 訊息的資料格式。|
| iot_hub_resource_id | *資源識別碼* | 您的 IoT 中樞的資源識別碼，其中保存要用於內嵌的資料。|
| shared_access_policy_name | *iothubforread* | 共用存取原則的名稱，定義裝置和服務連接到 IoT 中樞的許可權。 |
| consumer_group | *$Default* | 事件中樞的取用者群組。|
| location | *Central US* | 資料連線資源的位置。|

[!INCLUDE [data-explorer-data-connection-clean-resources-python](includes/data-explorer-data-connection-clean-resources-python.md)]
