---
title: 將 JSON 格式的資料內嵌至 Azure 資料總管
description: 瞭解如何將 JSON 格式的資料內嵌至 Azure 資料總管。
author: orspod
ms.author: orspodek
ms.reviewer: kerend
ms.service: data-explorer
ms.topic: how-to
ms.date: 05/19/2020
ms.openlocfilehash: 7132542b4387c9146c337a2440b2211d2977ec72
ms.sourcegitcommit: 3eabd78305d32cd9b8a6bd1d76877ddc19d8ac63
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94548915"
---
# <a name="ingest-json-formatted-sample-data-into-azure-data-explorer"></a>將 JSON 格式的範例資料內嵌至 Azure 資料總管

本文說明如何將 JSON 格式的資料內嵌至 Azure 資料總管資料庫。 您將從簡單的原始和對應 JSON 範例開始，繼續進行多線條 JSON，然後處理包含陣列和字典的更複雜 JSON 架構。  這些範例詳細說明使用 Kusto 查詢語言來擷取 JSON 格式資料的程式 (KQL) 、c # 或 Python。 Kusto 查詢語言 `ingest` 控制命令會直接執行至引擎端點。 在生產案例中，會使用用戶端程式庫或資料連線，將內嵌執行至資料管理服務。 使用 azure [資料總管 Python 程式庫](python-ingest-data.md) 來讀取內嵌資料，並 [使用 AZURE 資料總管 .NET Standard SDK 內嵌資料](./net-sdk-ingest-data.md) ，以取得有關使用這些用戶端程式庫擷取資料的逐步解說。

## <a name="prerequisites"></a>必要條件

[測試叢集和資料庫](create-cluster-database-portal.md)

## <a name="the-json-format"></a>JSON 格式

Azure 資料總管支援兩種 JSON 檔案格式：
* `json`：以行分隔的 JSON。 輸入資料中的每一行都只有一筆 JSON 記錄。
* `multijson`：多線條 JSON。 剖析器會忽略行分隔符號，並將先前位置的記錄讀入有效 JSON 的結尾。

### <a name="ingest-and-map-json-formatted-data"></a>內嵌和對應 JSON 格式化資料

JSON 格式化資料的內嵌需要您使用內嵌 [屬性](ingestion-properties.md)來指定 *格式* 。 JSON 資料的內嵌需要 [對應](kusto/management/mappings.md)，此對應會將 json 來源專案對應至其目標資料行。 擷取資料時，請使用 `IngestionMapping` 屬性，並將其 `ingestionMappingReference` (用於預先定義的對應) 內嵌屬性或其 `IngestionMappings` 屬性。 本文將使用內嵌 `ingestionMappingReference` 屬性，此屬性是在用於內嵌的資料表上預先定義的。 在下列範例中，我們一開始會先將 JSON 記錄擷取成單一資料行資料表的原始資料。 接著，我們將使用對應，將每個屬性內嵌至其對應的資料行。 

### <a name="simple-json-example"></a>簡單 JSON 範例

下列範例是具有平面結構的簡單 JSON。 資料具有溫度和濕度資訊，由數個裝置收集。 每一筆記錄都會標示識別碼和時間戳記。

```json
{
    "timestamp": "2019-05-02 15:23:50.0369439",
    "deviceId": "2945c8aa-f13e-4c48-4473-b81440bb5ca2",
    "messageId": "7f316225-839a-4593-92b5-1812949279b3",
    "temperature": 31.0301639051317,
    "humidity": 62.0791099602725
}
```

## <a name="ingest-raw-json-records"></a>內嵌原始 JSON 記錄 

在此範例中，您會將 JSON 記錄以原始資料的形式內嵌至單一資料行資料表。 資料操作、使用查詢和更新原則會在內嵌資料之後完成。

# <a name="kql"></a>[KQL](#tab/kusto-query-language)

使用 Kusto 查詢語言，以原始 JSON 格式內嵌資料。

1. 登入 [https://dataexplorer.azure.com](https://dataexplorer.azure.com)。

1. 選取 [新增叢集]。

1. 在 [新增叢集] 對話方塊中，以 `https://<ClusterName>.<Region>.kusto.windows.net/` 格式輸入您的叢集 URL，然後選取 [新增]。

1. 貼上下列命令，然後選取 [ **執行** ] 以建立資料表。

    ```kusto
    .create table RawEvents (Event: dynamic)
    ```

    此查詢會建立具有 `Event` [動態](kusto/query/scalar-data-types/dynamic.md) 資料類型之單一資料行的資料表。

1. 建立 JSON 對應。

    ```kusto
    .create table RawEvents ingestion json mapping 'RawEventMapping' '[{"column":"Event","Properties":{"path":"$"}}]'
    ```

    此命令會建立對應，並將 JSON 根路徑對應 `$` 到資料 `Event` 行。

1. 將資料內嵌到 `RawEvents` 資料表中。

    ```kusto
    .ingest into table RawEvents ('https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json') with '{"format":json, "ingestionMappingReference":"DiagnosticRawRecordsMapping"}'
    ```

# <a name="c"></a>[C#](#tab/c-sharp)

使用 c # 以原始 JSON 格式內嵌資料。

1. 建立 `RawEvents` 資料表。

    ```csharp
    var kustoUri = "https://<ClusterName>.<Region>.kusto.windows.net:443/";
    var kustoConnectionStringBuilder =
        new KustoConnectionStringBuilder(ingestUri)
        {
            FederatedSecurity = true,
            InitialCatalog = database,
            UserID = user,
            Password = password,
            Authority = tenantId
        };
    var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder);

    var table = "RawEvents";
    var command =
        CslCommandGenerator.GenerateTableCreateCommand(
            table,
            new[]
            {
                Tuple.Create("Events", "System.Object"),
            });

    kustoClient.ExecuteControlCommand(command);
    ```

1. 建立 JSON 對應。
    
    ```csharp
    var tableMapping = "RawEventMapping";
    var command =
        CslCommandGenerator.GenerateTableMappingCreateCommand(
            Data.Ingestion.IngestionMappingKind.Json,
            tableName,
            tableMapping,
            new[] {
            new ColumnMapping {ColumnName = "Events", Properties = new Dictionary<string, string>() {
                {"path","$"} }
            } });

    kustoClient.ExecuteControlCommand(command);
    ```
    此命令會建立對應，並將 JSON 根路徑對應 `$` 到資料 `Event` 行。

1. 將資料內嵌到 `RawEvents` 資料表中。

    ```csharp
    var ingestUri = "https://ingest-<ClusterName>.<Region>.kusto.windows.net:443/";
    var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json"; 
    var ingestConnectionStringBuilder =
        new KustoConnectionStringBuilder(ingestUri)
        {
            FederatedSecurity = true,
            InitialCatalog = database,
            UserID = user,
            Password = password,
            Authority = tenantId
        };
    var ingestClient = KustoIngestFactory.CreateQueuedIngestClient(ingestConnectionStringBuilder);
    
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.json,
            IngestionMapping = new IngestionMapping()
            {
                IngestionMappingReference = tableMapping
            }
        };

    ingestClient.IngestFromStorageAsync(blobPath, properties);
    ```

> [!NOTE]
> 資料會根據 [批次處理原則](kusto/management/batchingpolicy.md)進行匯總，因此會延遲幾分鐘。

# <a name="python"></a>[Python](#tab/python)

使用 Python 以原始 JSON 格式內嵌資料。

1. 建立 `RawEvents` 資料表。

    ```python
    KUSTO_URI = "https://<ClusterName>.<Region>.kusto.windows.net:443/"
    KCSB_DATA = KustoConnectionStringBuilder.with_aad_device_authentication(KUSTO_URI, AAD_TENANT_ID)
    KUSTO_CLIENT = KustoClient(KCSB_DATA)
    TABLE = "RawEvents"

    CREATE_TABLE_COMMAND = ".create table " + TABLE + " (Events: dynamic)"
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_TABLE_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. 建立 JSON 對應。

    ```python
    MAPPING = "RawEventMapping"
    CREATE_MAPPING_COMMAND = ".create table " + TABLE + " ingestion json mapping '" + MAPPING + """' '[{"column":"Event","path":"$"}]'"""
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_MAPPING_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. 將資料內嵌到 `RawEvents` 資料表中。

    ```python
    INGEST_URI = "https://ingest-<ClusterName>.<Region>.kusto.windows.net:443/"
    KCSB_INGEST = KustoConnectionStringBuilder.with_aad_device_authentication(INGEST_URI, AAD_TENANT_ID)
    INGESTION_CLIENT = KustoIngestClient(KCSB_INGEST)
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json'
    
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.json, mappingReference=MAPPING)
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

    > [!NOTE]
    > 資料會根據 [批次處理原則](kusto/management/batchingpolicy.md)進行匯總，因此會延遲幾分鐘。

---

## <a name="ingest-mapped-json-records"></a>內嵌對應的 JSON 記錄

在此範例中，您會內嵌 JSON 記錄資料。 每個 JSON 屬性都會對應到資料表中的單一資料行。 

# <a name="kql"></a>[KQL](#tab/kusto-query-language)

1. 使用與 JSON 輸入資料類似的架構來建立新的資料表。 我們將使用此資料表來取得下列所有範例和內嵌命令。 

    ```kusto
    .create table Events (Time: datetime, Device: string, MessageId: string, Temperature: double, Humidity: double)
    ```

1. 建立 JSON 對應。

    ```kusto
    .create table Events ingestion json mapping 'FlatEventMapping' '[{"column":"Time","Properties":{"path":"$.timestamp"}},{"column":"Device","Properties":{"path":"$.deviceId"}},{"column":"MessageId","Properties":{"path":"$.messageId"}},{"column":"Temperature","Properties":{"path":"$.temperature"}},{"column":"Humidity","Properties":{"path":"$.humidity"}}]'
    ```

    在這個對應中，如同資料表架構所定義， `timestamp` 專案將會以資料類型的形式內嵌至 `Time` `datetime` 資料行。

1. 將資料內嵌到 `Events` 資料表中。

    ```kusto
    .ingest into table Events ('https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json') with '{"format":"json", "ingestionMappingReference":"FlatEventMapping"}'
    ```

    檔案 ' simple.json ' 有一些行分隔的 JSON 記錄。 格式為 `json` ，而且內嵌命令中使用的對應是 `FlatEventMapping` 您所建立的。

# <a name="c"></a>[C#](#tab/c-sharp)

1. 使用與 JSON 輸入資料類似的架構來建立新的資料表。 我們將使用此資料表來取得下列所有範例和內嵌命令。 

    ```csharp
    var table = "Events";
    var command =
        CslCommandGenerator.GenerateTableCreateCommand(
            table,
            new[]
            {
                Tuple.Create("Time", "System.DateTime"),
                Tuple.Create("Device", "System.String"),
                Tuple.Create("MessageId", "System.String"),
                Tuple.Create("Temperature", "System.Double"),
                Tuple.Create("Humidity", "System.Double"),
            });

    kustoClient.ExecuteControlCommand(command);
    ```

1. 建立 JSON 對應。

    ```csharp
    var tableMapping = "FlatEventMapping";
    var command =
         CslCommandGenerator.GenerateTableMappingCreateCommand(
            Data.Ingestion.IngestionMappingKind.Json,
            "",
            tableMapping,
            new[]
            {
               new ColumnMapping() {ColumnName = "Time", Properties = new Dictionary<string, string>() {{ MappingConsts.Path, "$.timestamp"} } },
               new ColumnMapping() {ColumnName = "Device", Properties = new Dictionary<string, string>() {{ MappingConsts.Path, "$.deviceId" } } },
               new ColumnMapping() {ColumnName = "MessageId", Properties = new Dictionary<string, string>() {{ MappingConsts.Path, "$.messageId" } } },
               new ColumnMapping() {ColumnName = "Temperature", Properties = new Dictionary<string, string>() {{ MappingConsts.Path, "$.temperature" } } },
               new ColumnMapping() { ColumnName= "Humidity", Properties = new Dictionary<string, string>() {{ MappingConsts.Path, "$.humidity" } } },
            });

    kustoClient.ExecuteControlCommand(command);
    ```

    在這個對應中，如同資料表架構所定義， `timestamp` 專案將會以資料類型的形式內嵌至 `Time` `datetime` 資料行。    

1. 將資料內嵌到 `Events` 資料表中。

    ```csharp
    var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json";
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.json,
            IngestionMapping = new IngestionMapping()
            {
                IngestionMappingReference = tableMapping
            }
        };

    ingestClient.IngestFromStorageAsync(blobPath, properties);
    ```

    檔案 ' simple.json ' 有一些行分隔的 JSON 記錄。 格式為 `json` ，而且內嵌命令中使用的對應是 `FlatEventMapping` 您所建立的。

# <a name="python"></a>[Python](#tab/python)

1. 使用與 JSON 輸入資料類似的架構來建立新的資料表。 我們將使用此資料表來取得下列所有範例和內嵌命令。 

    ```python
    TABLE = "RawEvents"
    CREATE_TABLE_COMMAND = ".create table " + TABLE + " (Time: datetime, Device: string, MessageId: string, Temperature: double, Humidity: double)"
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_TABLE_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. 建立 JSON 對應。

    ```python
    MAPPING = "FlatEventMapping"
    CREATE_MAPPING_COMMAND = ".create table Events ingestion json mapping '" + MAPPING + """' '[{"column":"Time","Properties":{"path":"$.timestamp"}},{"column":"Device","Properties":{"path":"$.deviceId"}},{"column":"MessageId","Properties":{"path":"$.messageId"}},{"column":"Temperature","Properties":{"path":"$.temperature"}},{"column":"Humidity","Properties":{"path":"$.humidity"}}]'""" 
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_MAPPING_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. 將資料內嵌到 `Events` 資料表中。

    ```python
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json'
    
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.json, mappingReference=MAPPING)
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

    檔案 ' simple.json ' 有幾行分隔的 JSON 記錄。 格式為 `json` ，而且內嵌命令中使用的對應是 `FlatEventMapping` 您所建立的。    
---

## <a name="ingest-multi-lined-json-records"></a>內嵌多線條 JSON 記錄

在此範例中，您會內嵌多個線條的 JSON 記錄。 每個 JSON 屬性都會對應到資料表中的單一資料行。 檔案 ' multilined.json ' 有一些縮排的 JSON 記錄。 格式會 `multijson` 告知引擎讀取 JSON 結構的記錄。

# <a name="kql"></a>[KQL](#tab/kusto-query-language)

將資料內嵌到 `Events` 資料表中。

```kusto
.ingest into table Events ('https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/multilined.json') with '{"format":"multijson", "ingestionMappingReference":"FlatEventMapping"}'
```

# <a name="c"></a>[C#](#tab/c-sharp)

將資料內嵌到 `Events` 資料表中。

```csharp
var tableMapping = "FlatEventMapping";
var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/multilined.json";
var properties =
    new KustoQueuedIngestionProperties(database, table)
    {
        Format = DataSourceFormat.multijson,
        IngestionMapping = new IngestionMapping()
        {
            IngestionMappingReference = tableMapping
        }
    };

ingestClient.IngestFromStorageAsync(blobPath, properties);
```

# <a name="python"></a>[Python](#tab/python)

將資料內嵌到 `Events` 資料表中。

```python
MAPPING = "FlatEventMapping"
BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/multilined.json'
INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.multijson, mappingReference=MAPPING)
BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
INGESTION_CLIENT.ingest_from_blob(
    BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
```

---

## <a name="ingest-json-records-containing-arrays"></a>內嵌包含陣列的 JSON 記錄

陣列資料類型是已排序的值集合。 JSON 陣列的內嵌是由 [更新原則](kusto/management/update-policy.md)所完成。 JSON 會依原樣內嵌至中繼資料表。 更新原則會在資料表上執行預先定義的函數 `RawEvents` ，並將結果 reingesting 至目標資料表。 我們將內嵌具有下列結構的資料：

```json
{
    "records": 
    [
        {
            "timestamp": "2019-05-02 15:23:50.0000000",
            "deviceId": "ddbc1bf5-096f-42c0-a771-bc3dca77ac71",
            "messageId": "7f316225-839a-4593-92b5-1812949279b3",
            "temperature": 31.0301639051317,
            "humidity": 62.0791099602725
        },
        {
            "timestamp": "2019-05-02 15:23:51.0000000",
            "deviceId": "ddbc1bf5-096f-42c0-a771-bc3dca77ac71",
            "messageId": "57de2821-7581-40e4-861e-ea3bde102364",
            "temperature": 33.7529423105311,
            "humidity": 75.4787976739364
        }
    ]
}
```

# <a name="kql"></a>[KQL](#tab/kusto-query-language)

1. 建立 `update policy` 擴充集合的函式， `records` 讓集合中的每個值都使用運算子接收個別的資料列 `mv-expand` 。 我們將使用資料表 `RawEvents` 做為來源資料表和 `Events` 目標資料表。

    ```kusto
    .create function EventRecordsExpand() {
        RawEvents
        | mv-expand records = Event
        | project
            Time = todatetime(records["timestamp"]),
            Device = tostring(records["deviceId"]),
            MessageId = tostring(records["messageId"]),
            Temperature = todouble(records["temperature"]),
            Humidity = todouble(records["humidity"])
    }
    ```

1. 函數所接收的架構必須符合目標資料表的架構。 使用 `getschema` 運算子來檢查架構。

    ```kusto
    EventRecordsExpand() | getschema
    ```

1. 將更新原則新增至目標資料表。 這項原則會自動對中繼資料表中任何新內嵌的資料執行查詢 `RawEvents` ，並將結果內嵌到 `Events` 資料表中。 定義零保留原則，以避免保存中繼資料表。

    ```kusto
    .alter table Events policy update @'[{"Source": "RawEvents", "Query": "EventRecordsExpand()", "IsEnabled": "True"}]'
    ```

1. 將資料內嵌到 `RawEvents` 資料表中。

    ```kusto
    .ingest into table RawEvents ('https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/array.json') with '{"format":"multijson", "ingestionMappingReference":"RawEventMapping"}'
    ```

1. 檢查資料表中的資料 `Events` 。

    ```kusto
    Events
    ```

# <a name="c"></a>[C#](#tab/c-sharp)

1. 建立擴充集合的更新函式， `records` 讓集合中的每個值都使用運算子接收個別的資料列 `mv-expand` 。 我們將使用資料表 `RawEvents` 做為來源資料表和 `Events` 目標資料表。   

    ```csharp
    var command =
        CslCommandGenerator.GenerateCreateFunctionCommand(
            "EventRecordsExpand",
            "UpdateFunctions",
            string.Empty,
            null,
            @"RawEvents
                | mv-expand records = Event
                | project
                    Time = todatetime(records['timestamp']),
                    Device = tostring(records['deviceId']),
                    MessageId = tostring(records['messageId']),
                    Temperature = todouble(records['temperature']),
                    Humidity = todouble(records['humidity'])",
            ifNotExists: false);

    kustoClient.ExecuteControlCommand(command);
    ```

    > [!NOTE]
    > 函數所接收的架構必須符合目標資料表的架構。

1. 將更新原則新增至目標資料表。 這項原則會自動對中繼資料表中任何新內嵌的資料執行查詢 `RawEvents` ，並將其結果內嵌 `Events` 到資料表中。 定義零保留原則，以避免保存中繼資料表。

    ```csharp
    var command =
        ".alter table Events policy update @'[{'Source': 'RawEvents', 'Query': 'EventRecordsExpand()', 'IsEnabled': 'True'}]";

    kustoClient.ExecuteControlCommand(command);
    ```

1. 將資料內嵌到 `RawEvents` 資料表中。

    ```csharp
    var table = "RawEvents";
    var tableMapping = "RawEventMapping";
    var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/array.json";
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.multijson,
            IngestionMapping = new IngestionMapping()
            {
                IngestionMappingReference = tableMapping
            }
        };

    ingestClient.IngestFromStorageAsync(blobPath, properties);
    ```
    
1. 檢查資料表中的資料 `Events` 。

# <a name="python"></a>[Python](#tab/python)

1. 建立擴充集合的更新函式， `records` 讓集合中的每個值都使用運算子接收個別的資料列 `mv-expand` 。 我們將使用資料表 `RawEvents` 做為來源資料表和 `Events` 目標資料表。   

    ```python
    CREATE_FUNCTION_COMMAND = 
        '''.create function EventRecordsExpand() { 
            RawEvents
            | mv-expand records = Event
            | project
                Time = todatetime(records["timestamp"]),
                Device = tostring(records["deviceId"]),
                MessageId = tostring(records["messageId"]),
                Temperature = todouble(records["temperature"]),
                Humidity = todouble(records["humidity"])
            }'''
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_FUNCTION_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

    > [!NOTE]
    > 函數所接收的架構必須符合目標資料表的架構。

1. 將更新原則新增至目標資料表。 這項原則會自動對中繼資料表中任何新內嵌的資料執行查詢 `RawEvents` ，並將其結果內嵌 `Events` 到資料表中。 定義零保留原則，以避免保存中繼資料表。

    ```python
    CREATE_UPDATE_POLICY_COMMAND = 
        """.alter table Events policy update @'[{'Source': 'RawEvents', 'Query': 'EventRecordsExpand()', 'IsEnabled': 'True'}]"""
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_UPDATE_POLICY_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. 將資料內嵌到 `RawEvents` 資料表中。

    ```python
    TABLE = "RawEvents"
    MAPPING = "RawEventMapping"
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/array.json'
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.multijson, mappingReference=MAPPING)
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

1. 檢查資料表中的資料 `Events` 。

---    

## <a name="ingest-json-records-containing-dictionaries"></a>內嵌包含字典的 JSON 記錄

字典結構化 JSON 包含機碼值組。 Json 記錄會使用中的邏輯運算式來進行內嵌對應 `JsonPath` 。 您可以使用下列結構內嵌資料：

```json
{
    "event": 
    [
        {
            "Key": "timestamp",
            "Value": "2019-05-02 15:23:50.0000000"
        },
        {
            "Key": "deviceId",
            "Value": "ddbc1bf5-096f-42c0-a771-bc3dca77ac71"
        },
        {
            "Key": "messageId",
            "Value": "7f316225-839a-4593-92b5-1812949279b3"
        },
        {
            "Key": "temperature",
            "Value": 31.0301639051317
        },
        {
            "Key": "humidity",
            "Value": 62.0791099602725
        }
    ]
}
```

# <a name="kql"></a>[KQL](#tab/kusto-query-language)

1. 建立 JSON 對應。

    ```kusto
    .create table Events ingestion json mapping 'KeyValueEventMapping' '[{"column":"a","Properties":{"path":"$.event[?(@.Key == \'timestamp\')]"}},{"column":"b","Properties":{"path":"$.event[?(@.Key == \'deviceId\')]"}},{"column":"c","Properties":{"path":"$.event[?(@.Key == \'messageId\')]"}},{"column":"d","Properties":{"path":"$.event[?(@.Key == \'temperature\')]"}},{"column":"Humidity","datatype":"string","Properties":{"path":"$.event[?(@.Key == \'humidity\')]"}}]'
    ```

1. 將資料內嵌到 `Events` 資料表中。

    ```kusto
    .ingest into table Events ('https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/dictionary.json') with '{"format":"multijson", "ingestionMappingReference":"KeyValueEventMapping"}'
    ```

# <a name="c"></a>[C#](#tab/c-sharp)

1. 建立 JSON 對應。

    ```csharp
    var tableName = "Events";
    var tableMapping = "KeyValueEventMapping";
    var command =
         CslCommandGenerator.GenerateTableMappingCreateCommand(
            Data.Ingestion.IngestionMappingKind.Json,
            "",
            tableMapping,
            new[]
            {
                new ColumnMapping() { ColumnName = "Time", Properties = new Dictionary<string, string>() { {
                    MappingConsts.Path,
                    "$.event[?(@.Key == 'timestamp')]"
                } } },
                    new ColumnMapping() { ColumnName = "Device", Properties = new Dictionary<string, string>() { {
                    MappingConsts.Path,
                    "$.event[?(@.Key == 'deviceId')]"
                } } }, new ColumnMapping() { ColumnName = "MessageId", Properties = new Dictionary<string, string>() { {
                    MappingConsts.Path,
                    "$.event[?(@.Key == 'messageId')]"
                } } }, new ColumnMapping() { ColumnName = "Temperature", Properties = new Dictionary<string, string>() { {
                    MappingConsts.Path,
                    "$.event[?(@.Key == 'temperature')]"
                } } }, new ColumnMapping() { ColumnName = "Humidity", Properties = new Dictionary<string, string>() { {
                    MappingConsts.Path,
                    "$.event[?(@.Key == 'humidity')]"
                } } },
            });

    kustoClient.ExecuteControlCommand(command);
    ```

1. 將資料內嵌到 `Events` 資料表中。

    ```csharp
    var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/dictionary.json";
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.multijson,
            IngestionMapping = new IngestionMapping()
            {
                IngestionMappingReference = tableMapping
            }
        };
    ingestClient.IngestFromStorageAsync(blobPath, properties);
    ```

# <a name="python"></a>[Python](#tab/python)

1. 建立 JSON 對應。

    ```python
    MAPPING = "KeyValueEventMapping"
    CREATE_MAPPING_COMMAND = ".create table Events ingestion json mapping '" + MAPPING + """' '[{"column":"Time","path":"$.event[?(@.Key == 'timestamp')]"},{"column":"Device","path":"$.event[?(@.Key == 'deviceId')]"},{"column":"MessageId","path":"$.event[?(@.Key == 'messageId')]"},{"column":"Temperature","path":"$.event[?(@.Key == 'temperature')]"},{"column":"Humidity","path":"$.event[?(@.Key == 'humidity')]"}]'""" 
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_MAPPING_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. 將資料內嵌到 `Events` 資料表中。

     ```python
    MAPPING = "KeyValueEventMapping"
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/dictionary.json'
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.multijson, mappingReference=MAPPING)u
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

---    

## <a name="next-steps"></a>後續步驟

* [資料擷取概觀](ingest-data-overview.md)
* [撰寫查詢](write-queries.md)