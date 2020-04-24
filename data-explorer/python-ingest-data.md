---
title: 使用 Azure 資料總管 Python 程式庫內嵌資料
description: 在本文中,您將瞭解如何使用 Python 將資料引入(載入)到 Azure 資料資源管理器中。
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 28151476ce96fabc92e04078396119d0eb8c2f17
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81501564"
---
# <a name="ingest-data-using-the-azure-data-explorer-python-library"></a>使用 Azure 資料總管 Python 程式庫內嵌資料

在本文中,您可以使用 Azure 資料資源管理員 Python 庫引入資料。 Azure 資料總管是一項快速又可高度調整的資料探索服務，可用於處理記錄和遙測資料。 Azure 資料總管提供兩個適用於 Python 的用戶端程式庫：[內嵌程式庫](https://github.com/Azure/azure-kusto-python/tree/master/azure-kusto-ingest)和[資料程式庫](https://github.com/Azure/azure-kusto-python/tree/master/azure-kusto-data)。 這些庫使您能夠將數據引入或載入到群集中,並從代碼中查詢數據。

首先,在群集中創建表和數據映射。 然後，您將叢集的擷取排入佇列並驗證結果。

本文也可作為[Azure 筆記本](https://notebooks.azure.com/ManojRaheja/libraries/KustoPythonSamples/html/QueuedIngestSingleBlob.ipynb)提供。

## <a name="prerequisites"></a>Prerequisites

* 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

* [Python 3.4+](https://www.python.org/downloads/)。

* [叢集與資料庫](create-cluster-database-portal.md)。

## <a name="install-the-data-and-ingest-libraries"></a>安裝資料並內嵌程式庫

安裝 *azure-kusto-data* 和 *azure-kusto-ingest*。

```
pip install azure-kusto-data
pip install azure-kusto-ingest
```

## <a name="add-import-statements-and-constants"></a>新增 Import 陳述式和常數

從 zure-kusto-data 匯入類別。

```python
from azure.kusto.data.request import KustoClient, KustoConnectionStringBuilder
from azure.kusto.data.exceptions import KustoServiceError
from azure.kusto.data.helpers import dataframe_from_result_table
```

為了驗證應用程式，Azure 資料總管會使用您的 AAD 租用戶識別碼。 若要尋找您的租用戶識別碼，請使用下列 URL，並以您的網域取代 *YourDomain*。

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

例如，如果您的網域為 *contoso.com*，則 URL 會是：[https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/)。 按一下此 URL 來查看結果；第一行如下所示。 

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

在此案例中，租用戶識別碼為 `6babcaad-604b-40ac-a9d7-9fd97c0b779f`。 先設定 AAD_TENANT_ID、KUSTO_URI、KUSTO_INGEST_URI 和 KUSTO_DATABASE 的值，再執行此程式碼。

```python
AAD_TENANT_ID = "<TenantId>"
KUSTO_URI = "https://<ClusterName>.<Region>.kusto.windows.net:443/"
KUSTO_INGEST_URI = "https://ingest-<ClusterName>.<Region>.kusto.windows.net:443/"
KUSTO_DATABASE = "<DatabaseName>"
```

現在來建構連接字串。 此範例使用服務驗證來存取叢集。 您還可以使用[AAD 應用程式憑證](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L24)[、AAD 應用程式金鑰](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L20)以及[AAD 使用者與密碼](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L34)。

您可以在稍後的步驟中建立目的地資料表和對應。

```python
KCSB_INGEST = KustoConnectionStringBuilder.with_aad_device_authentication(
    KUSTO_INGEST_URI, AAD_TENANT_ID)

KCSB_DATA = KustoConnectionStringBuilder.with_aad_device_authentication(
    KUSTO_URI, AAD_TENANT_ID)

DESTINATION_TABLE = "StormEvents"
DESTINATION_TABLE_COLUMN_MAPPING = "StormEvents_CSV_Mapping"
```

## <a name="set-source-file-information"></a>設定來源檔案資訊

匯入其他類別，並設定資料來源檔案的常數。 本範例使用裝載於 Azure Blob 儲存體的範例檔案。 **風暴事件**樣本數據集包含來自[國家環境資訊中心](https://www.ncdc.noaa.gov/stormevents/)的天氣相關數據。

```python
from azure.storage.blob import BlockBlobService
from azure.kusto.ingest import KustoIngestClient, IngestionProperties, FileDescriptor, BlobDescriptor, DataFormat, ReportLevel, ReportMethod

CONTAINER = "samplefiles"
ACCOUNT_NAME = "kustosamplefiles"
SAS_TOKEN = "?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D"
FILE_PATH = "StormEvents.csv"
FILE_SIZE = 64158321    # in bytes

BLOB_PATH = "https://" + ACCOUNT_NAME + ".blob.core.windows.net/" + \
    CONTAINER + "/" + FILE_PATH + SAS_TOKEN
```

## <a name="create-a-table-on-your-cluster"></a>在叢集上建立資料表

建立符合 StormEvents.csv 檔案中資料結構描述的資料表。 此程式碼執行時，會傳回與下列類似的訊息：若要登入，請使用網頁瀏覽器開啟頁面 https://microsoft.com/devicelogin，並輸入程式碼 F3W4VWZDM 來驗證**。 請遵循下列步驟來登入，然後返回執行下一個程式碼區塊。 建立連線的後續程式碼區塊需要您重新登入。

```python
KUSTO_CLIENT = KustoClient(KCSB_DATA)
CREATE_TABLE_COMMAND = ".create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)"

RESPONSE = KUSTO_CLIENT.execute_mgmt(KUSTO_DATABASE, CREATE_TABLE_COMMAND)

dataframe_from_result_table(RESPONSE.primary_results[0])
```

## <a name="define-ingestion-mapping"></a>定義擷取對應

將傳入的 CSV 資料對應到建立資料表時使用的資料行名稱與資料類型。 這會將來源資料欄位對應至目的地資料表資料行

```python
CREATE_MAPPING_COMMAND = """.create table StormEvents ingestion csv mapping 'StormEvents_CSV_Mapping' '[{"Name":"StartTime","datatype":"datetime","Ordinal":0}, {"Name":"EndTime","datatype":"datetime","Ordinal":1},{"Name":"EpisodeId","datatype":"int","Ordinal":2},{"Name":"EventId","datatype":"int","Ordinal":3},{"Name":"State","datatype":"string","Ordinal":4},{"Name":"EventType","datatype":"string","Ordinal":5},{"Name":"InjuriesDirect","datatype":"int","Ordinal":6},{"Name":"InjuriesIndirect","datatype":"int","Ordinal":7},{"Name":"DeathsDirect","datatype":"int","Ordinal":8},{"Name":"DeathsIndirect","datatype":"int","Ordinal":9},{"Name":"DamageProperty","datatype":"int","Ordinal":10},{"Name":"DamageCrops","datatype":"int","Ordinal":11},{"Name":"Source","datatype":"string","Ordinal":12},{"Name":"BeginLocation","datatype":"string","Ordinal":13},{"Name":"EndLocation","datatype":"string","Ordinal":14},{"Name":"BeginLat","datatype":"real","Ordinal":16},{"Name":"BeginLon","datatype":"real","Ordinal":17},{"Name":"EndLat","datatype":"real","Ordinal":18},{"Name":"EndLon","datatype":"real","Ordinal":19},{"Name":"EpisodeNarrative","datatype":"string","Ordinal":20},{"Name":"EventNarrative","datatype":"string","Ordinal":21},{"Name":"StormSummary","datatype":"dynamic","Ordinal":22}]'"""

RESPONSE = KUSTO_CLIENT.execute_mgmt(KUSTO_DATABASE, CREATE_MAPPING_COMMAND)

dataframe_from_result_table(RESPONSE.primary_results[0])
```

## <a name="queue-a-message-for-ingestion"></a>將訊息排入佇列以供擷取

將訊息放入佇列，以從 Blob 儲存體提取資料，並將該資料內嵌至 Azure 資料總管。

```python
INGESTION_CLIENT = KustoIngestClient(KCSB_INGEST)

# All ingestion properties are documented here: https://docs.microsoft.com/azure/kusto/management/data-ingest#ingestion-properties
INGESTION_PROPERTIES = IngestionProperties(database=KUSTO_DATABASE, table=DESTINATION_TABLE, dataFormat=DataFormat.CSV,
                                           mappingReference=DESTINATION_TABLE_COLUMN_MAPPING, additionalProperties={'ignoreFirstRecord': 'true'})
# FILE_SIZE is the raw size of the data in bytes
BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
INGESTION_CLIENT.ingest_from_blob(
    BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)

print('Done queuing up ingestion with Azure Data Explorer')
```

## <a name="query-data-that-was-ingested-into-the-table"></a>查詢已內嵌至資料表的資料

等待已排入佇列的擷取五到十分鐘，以排定將資料內嵌和載入至 Azure 資料總管。 然後執行下列程式碼，以取得 StormEvents 資料表中的記錄計數。

```python
QUERY = "StormEvents | count"

RESPONSE = KUSTO_CLIENT.execute_query(KUSTO_DATABASE, QUERY)

dataframe_from_result_table(RESPONSE.primary_results[0])
```

## <a name="run-troubleshooting-queries"></a>執行疑難排解查詢

登錄到[https://dataexplorer.azure.com](https://dataexplorer.azure.com)群集並連接到群集。 在資料庫中執行下列命令，以查看最後四個小時是否有任何擷取失敗。 先取代資料庫名稱，再執行。

```Kusto
.show ingestion failures
| where FailedOn > ago(4h) and Database == "<DatabaseName>"
```

執行下列命令，以檢視最後四個小時內的所有擷取作業狀態。 先取代資料庫名稱，再執行。

```Kusto
.show operations
| where StartedOn > ago(4h) and Database == "<DatabaseName>" and Table == "StormEvents" and Operation == "DataIngestPull"
| summarize arg_max(LastUpdatedOn, *) by OperationId
```

## <a name="clean-up-resources"></a>清除資源

如果您計劃關注我們的其他文章,請保留您創建的資源。 否則，請在資料庫中執行下列命令，來清除 StormEvents 資料表。

```Kusto
.drop table StormEvents
```

## <a name="next-steps"></a>後續步驟

* [使用 Python 查詢資料](python-query-data.md)
