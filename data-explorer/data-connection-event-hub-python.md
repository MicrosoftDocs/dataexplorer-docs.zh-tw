---
title: 使用 Python 建立 Azure 資料總管的事件中樞資料連線
description: 在本文中，您將瞭解如何使用 Python 建立 Azure 資料總管的事件中樞資料連線。
author: orspod
ms.author: orspodek
ms.reviewer: lugoldbe
ms.service: data-explorer
ms.topic: how-to
ms.date: 10/07/2019
ms.openlocfilehash: b2877cb3de37610a9e526f659ccdb4a93d2d6568
ms.sourcegitcommit: 574296b9a84084de031684a65f32b6c1bd1a4858
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94714137"
---
# <a name="create-an-event-hub-data-connection-for-azure-data-explorer-by-using-python"></a>使用 Python 建立 Azure 資料總管的事件中樞資料連線

> [!div class="op_single_selector"]
> * [入口網站](ingest-data-event-hub.md)
> * [單鍵](one-click-event-hub.md)
> * [C#](data-connection-event-hub-csharp.md)
> * [Python](data-connection-event-hub-python.md)
> * [Azure Resource Manager 範本](data-connection-event-hub-resource-manager.md)

[!INCLUDE [data-connector-intro](includes/data-connector-intro.md)]
在本文中，您會使用 Python 建立 Azure 資料總管的事件中樞資料連線。 

## <a name="prerequisites"></a>Prerequisites

* 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
* [Python 3.4+](https://www.python.org/downloads/)。
* 叢集[和資料庫](create-cluster-database-python.md)。
* [資料表和資料行對應](./net-sdk-ingest-data.md#create-a-table-on-your-test-cluster)。
* [資料庫和資料表原則](database-table-policies-python.md) (選擇性) 。
* [具有內嵌資料的事件中樞](ingest-data-event-hub.md#create-an-event-hub)。

[!INCLUDE [data-explorer-data-connection-install-package-python](includes/data-explorer-data-connection-install-package-python.md)]

[!INCLUDE [data-explorer-authentication](includes/data-explorer-authentication.md)]

## <a name="add-an-event-hub-data-connection"></a>新增事件中樞資料連線

下列範例會示範如何以程式設計方式新增事件中樞資料連線。 請參閱 [連接到事件中樞](ingest-data-event-hub.md#connect-to-the-event-hub) ，以使用 Azure 入口網站新增事件中樞資料連線。

```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import EventHubDataConnection
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
#The event hub that is created as part of the Prerequisites
event_hub_resource_id = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx";
consumer_group = "$Default"
location = "Central US"
#The table and column mapping that are created as part of the Prerequisites
table_name = "StormEvents"
mapping_rule_name = "StormEvents_CSV_Mapping"
data_format = "csv"
#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.data_connections.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name, data_connection_name=data_connection_name,
                                        parameters=EventHubDataConnection(event_hub_resource_id=event_hub_resource_id, consumer_group=consumer_group, location=location,
                                                                            table_name=table_name, mapping_rule_name=mapping_rule_name, data_format=data_format))
```

|**設定** | **建議的值** | **欄位描述**|
|---|---|---|
| tenant_id | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxx-xxxxxxxxx* | 您的租用戶識別碼。 也稱為目錄識別碼。|
| subscriptionId | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxx-xxxxxxxxx* | 您用來建立資源的訂用帳戶識別碼。|
| client_id | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxx-xxxxxxxxx* | 應用程式的用戶端識別碼，可存取您租用戶中的資源。|
| client_secret | *xxxxxxxxxxxxxx* | 應用程式的用戶端密碼，可以存取您租用戶中的資源。 |
| resource_group_name | *>testrg* | 包含您叢集的資源組名。|
| cluster_name | *mykustocluster* | 叢集的名稱。|
| database_name | *mykustodatabase* | 叢集中目標資料庫的名稱。|
| data_connection_name | *myeventhubconnect* | 您的資料連線所需的名稱。|
| table_name | *StormEvents* | 目標資料庫中的目標資料表名稱。|
| mapping_rule_name | *StormEvents_CSV_Mapping* | 與目標資料表相關之資料行對應的名稱。|
| data_format | *Csv* | 訊息的資料格式。|
| event_hub_resource_id | *資源識別碼* | 您事件中樞的資源識別碼，其中包含要內嵌的資料。 |
| consumer_group | *$Default* | 事件中樞的取用者群組。|
| location | *Central US* | 資料連線資源的位置。|

[!INCLUDE [data-explorer-data-connection-clean-resources-python](includes/data-explorer-data-connection-clean-resources-python.md)]