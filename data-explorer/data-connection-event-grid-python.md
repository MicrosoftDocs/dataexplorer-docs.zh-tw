---
title: 使用 Python 建立 Azure 資料總管的 Event Grid 資料連線
description: 在本文中，您將瞭解如何使用 Python 建立 Azure 資料總管的 Event Grid 資料連線。
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 6e8bb6797200c3f3591fa8bf46e9b3ff7d4da49f
ms.sourcegitcommit: 0d15903613ad6466d49888ea4dff7bab32dc5b23
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/06/2020
ms.locfileid: "86013897"
---
# <a name="create-an-event-grid-data-connection-for-azure-data-explorer-by-using-python"></a>使用 Python 建立 Azure 資料總管的 Event Grid 資料連線

> [!div class="op_single_selector"]
> * [入口網站](ingest-data-event-grid.md)
> * [C#](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Azure Resource Manager 範本](data-connection-event-grid-resource-manager.md)

在本文中，您會使用 Python 建立 Azure 資料總管的 Event Grid 資料連線。 Azure 資料總管是一項快速又可高度調整的資料探索服務，可用於處理記錄和遙測資料。 Azure 資料總管可從事件中樞、IoT 中樞和寫入 blob 容器的 blob，提供內嵌或資料載入。

## <a name="prerequisites"></a>必要條件

* 具有有效訂用帳戶的 Azure 帳戶。 [建立免費帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

* [Python 3.4+](https://www.python.org/downloads/)。

* 叢集[和資料庫](create-cluster-database-python.md)。

* [資料表和資料行對應](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)。

* [資料庫和資料表原則](database-table-policies-csharp.md)（選擇性）。

* [具有事件方格訂用帳戶的儲存體帳戶](../data-explorer/kusto/management/data-ingestion/eventgrid.md#create-an-event-grid-subscription-in-your-storage-account)。

[!INCLUDE [data-explorer-data-connection-install-package-python](includes/data-explorer-data-connection-install-package-python.md)]

[!INCLUDE [data-explorer-authentication](includes/data-explorer-authentication.md)]

## <a name="add-an-event-grid-data-connection"></a>新增事件方格資料連線

下列範例會示範如何以程式設計方式加入事件方格資料連線。 如需使用 Azure 入口網站新增事件方格資料連線，請參閱[在 Azure 中建立事件方格資料連線資料總管](ingest-data-event-grid.md#create-an-event-grid-data-connection-in-azure-data-explorer)。


```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import EventGridDataConnection
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
#The event hub and storage account that are created as part of the Prerequisites
event_hub_resource_id = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx"
storage_account_resource_id = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Storage/storageAccounts/xxxxxx"
consumer_group = "$Default"
location = "Central US"
#The table and column mapping that are created as part of the Prerequisites
table_name = "StormEvents"
mapping_rule_name = "StormEvents_CSV_Mapping"
data_format = "csv"

#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.data_connections.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name, data_connection_name=data_connection_name,
                                            parameters=EventGridDataConnection(storage_account_resource_id=storage_account_resource_id, event_hub_resource_id=event_hub_resource_id, 
                                                                                consumer_group=consumer_group, table_name=table_name, location=location, mapping_rule_name=mapping_rule_name, data_format=data_format))
```
|**設定** | **建議的值** | **欄位描述**|
|---|---|---|
| tenant_id | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxxx-xxxx-xxxxxxxxx* | 您的租用戶識別碼。 也稱為目錄識別碼。|
| subscription_id | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxxx-xxxx-xxxxxxxxx* | 您用來建立資源的訂用帳戶識別碼。|
| client_id | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxxx-xxxx-xxxxxxxxx* | 應用程式的用戶端識別碼，可存取您租使用者中的資源。|
| client_secret | *xxxxxxxxxxxxxx* | 應用程式的用戶端密碼，可以存取您租使用者中的資源。 |
| resource_group_name | *testrg* | 包含您叢集的資源組名。|
| cluster_name | *mykustocluster* | 叢集的名稱。|
| database_name | *mykustodatabase* | 叢集中的目標資料庫名稱。|
| data_connection_name | *myeventhubconnect* | 所需的資料連線名稱。|
| table_name | *StormEvents* | 目標資料庫中目標資料表的名稱。|
| mapping_rule_name | *StormEvents_CSV_Mapping* | 與目標資料表相關之資料行對應的名稱。|
| data_format | *csv* | 訊息的資料格式。|
| event_hub_resource_id | *資源識別碼* | 事件中樞的資源識別碼，其中事件方格已設定為傳送事件。 |
| storage_account_resource_id | *資源識別碼* | 儲存體帳戶的資源識別碼，其中保存要內嵌的資料。 |
| consumer_group | *$Default* | 事件中樞的取用者群組。|
| location | *美國中部* | 資料連線資源的位置。|

[!INCLUDE [data-explorer-data-connection-clean-resources-python](includes/data-explorer-data-connection-clean-resources-python.md)]
