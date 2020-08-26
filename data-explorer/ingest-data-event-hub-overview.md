---
title: 從事件中樞內嵌-Azure 資料總管
description: 本文說明如何在 Azure 資料總管中從事件中樞內嵌。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 08/13/2020
ms.openlocfilehash: 8b24d6f771eaa4004b36a1b3171eb593e2fc0f6c
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88874880"
---
# <a name="connect-to-event-hub"></a>連線至事件中樞


[Azure 事件中樞](https://docs.microsoft.com/azure/event-hubs/event-hubs-about) 是大型的資料串流平臺和事件內嵌服務。 Azure 資料總管可從客戶管理的事件中樞提供連續的內嵌。

事件中樞內嵌管線會使用數個步驟將事件傳輸到 Azure 資料總管。 您會先在 Azure 入口網站中建立事件中樞。 然後，您會使用指定的內嵌[屬性](#set-ingestion-properties)，建立將內嵌[特定格式資料](#data-format)的目標資料表 Azure 資料總管。 事件中樞連接需要知道 [事件路由](#set-events-routing)。 資料會根據 [事件系統屬性對應](#set-event-system-properties-mapping)，以選取的屬性內嵌。 您可以透過 [Azure 入口網站](ingest-data-event-hub.md)、以 [c #](data-connection-event-hub-csharp.md) 或 [Python](data-connection-event-hub-python.md)程式設計方式，或使用 [Azure Resource Manager 範本](data-connection-event-hub-resource-manager.md)來管理此進程。

## <a name="data-format"></a>資料格式

* 會以 [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata?view=azure-dotnet) 物件的形式從事件中樞讀取資料。
* 您可以使用 [Azure 資料總管支援](ingestion-supported-formats.md)的其中一種格式來內嵌事件裝載。
* 您可以使用壓縮演算法來壓縮資料 `GZip` 。 必須指定為內嵌 `Compression` [屬性](#set-ingestion-properties)。

    > [!Note]
    > * 壓縮格式不支援資料壓縮 (Avro、Parquet、ORC) 。
    > * 在壓縮的資料上不支援自訂編碼和內嵌的 [系統屬性](#set-event-system-properties-mapping) 。
    
## <a name="set-ingestion-properties"></a>設定內嵌屬性

內嵌屬性會指示內嵌進程、路由資料的位置，以及如何處理它。 您可以使用[EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties)來指定內嵌事件的內嵌[屬性](ingestion-properties.md)。 您可以設定下列屬性：

|屬性 |描述|
|---|---|
| 資料表 |  (現有目標資料表的區分大小寫) 名稱。 覆寫分頁 `Table` 上的集合 `Data Connection` 。 |
| 格式 | 資料格式。 覆寫分頁 `Data format` 上的集合 `Data Connection` 。 |
| IngestionMappingReference | 要使用之現有內嵌 [對應](kusto/management/create-ingestion-mapping-command.md) 的名稱。 覆寫分頁 `Column mapping` 上的集合 `Data Connection` 。|
| 壓縮 | 資料壓縮、 `None` (預設) 或 `GZip` 壓縮。|
| 編碼 | 資料編碼，預設值為 UTF8。 可以是任何 [.net 支援的編碼](https://docs.microsoft.com/dotnet/api/system.text.encoding?view=netframework-4.8#remarks)方式。 |
| 標記 (預覽)  | 要與內嵌資料產生關聯的 [標記](kusto/management/extents-overview.md#extent-tagging) 清單，格式為 JSON 陣列字串。 使用標記時，會 [影響效能](kusto/management/extents-overview.md#performance-notes-1) 。 |

<!--| Database | Name of the existing target database.|-->
<!--| Tags | String representing [tags](https://docs.microsoft.com/azure/kusto/management/extents-overview#extent-tagging) that will be attached to resulting extent. |-->

## <a name="set-events-routing"></a>設定事件路由

當您設定 Azure 資料總管叢集的事件中樞連線時，您可以 (資料表名稱、資料格式、壓縮和對應) 來指定目標資料表屬性。 您資料的預設路由也稱為 `static routing` 。
您也可以使用事件屬性，為每個事件指定目標資料表屬性。 連接會動態路由 [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties)中指定的資料，並覆寫這個事件的靜態屬性。

在下列範例中，請設定事件中樞詳細資料，並將天氣計量資料傳送給資料表 `WeatherMetrics` 。
資料的 `json` 格式為。 `mapping1` 在資料表上是預先定義的 `WeatherMetrics` 。

```csharp
var eventHubNamespaceConnectionString=<connection_string>;
var eventHubName=<event_hub>;

// Create the data
var metric = new Metric { Timestamp = DateTime.UtcNow, MetricName = "Temperature", Value = 32 }; 
var data = JsonConvert.SerializeObject(metric);

// Create the event and add optional "dynamic routing" properties
var eventData = new EventData(Encoding.UTF8.GetBytes(data));
eventData.Properties.Add("Table", "WeatherMetrics");
eventData.Properties.Add("Format", "json");
eventData.Properties.Add("IngestionMappingReference", "mapping1");
eventData.Properties.Add("Tags", "['mydatatag']");

// Send events
var eventHubClient = EventHubClient.CreateFromConnectionString(eventHubNamespaceConnectionString, eventHubName);
eventHubClient.Send(eventData);
eventHubClient.Close();
```

## <a name="set-event-system-properties-mapping"></a>設定事件系統屬性對應

系統屬性會儲存事件排入佇列時，由事件中樞服務所設定的屬性。 Azure 資料總管事件中樞連接會將選取的屬性內嵌至資料表中的資料登陸。

> [!Note]
> * 單一記錄事件支援系統屬性。
> * 壓縮資料不支援系統屬性。
> * 針對 `csv` 對應，會依下表所列的順序，在記錄的開頭加入屬性。 針對對應 `json` ，會根據下表中的屬性名稱來新增屬性。

### <a name="event-hub-exposes-the-following-system-properties"></a>事件中樞會公開下列系統屬性

|屬性 |資料類型 |描述|
|---|---|---|
| x-opt-enqueued-time |Datetime | 事件排入佇列時的 UTC 時間 |
| x-opt-sequence-number |long | 事件中樞之分割資料流程內事件的邏輯序號
| x-opt-offset |字串 | 事件中樞分割區資料流程中事件的位移。 位移識別碼在事件中樞資料流程的資料分割內是唯一的 |
| x-選擇發行者 |字串 | 發行者名稱（如果訊息已傳送至發行者端點） |
| x-opt-partition-key |字串 |儲存事件之對應分割區的分割區索引鍵 |

如果您在資料表的 [**資料來源**] 區段中選取 [**事件系統屬性**]，就必須在資料表架構和對應中包含屬性。

### <a name="examples"></a>範例

#### <a name="table-schema-example"></a>資料表架構範例

如果您的資料包含下列資料，請使用資料表架構命令來建立或改變數據表架構：
* `Timespan`、和等資料行 `Metric``Value`  
* 屬性 `x-opt-enqueued-time` 和 `x-opt-offset`

```kusto
    .create-merge table TestTable (TimeStamp: datetime, Metric: string, Value: int, EventHubEnqueuedTime:datetime, EventHubOffset:long)
```

#### <a name="csv-mapping-example"></a>CSV 對應範例

執行下列命令，以將資料新增至記錄的開頭。
屬性會依上表所列的順序加入至記錄的開頭。
序數值對於 CSV 對應很重要，其中資料行序數會根據對應的系統屬性而變更。

```kusto
    .create table TestTable ingestion csv mapping "CsvMapping1"
    '['
    '   { "column" : "Timespan", "Properties":{"Ordinal":"2"}},'
    '   { "column" : "Metric", "Properties":{"Ordinal":"3"}},'
    '   { "column" : "Value", "Properties":{"Ordinal":"4"}},'
    '   { "column" : "EventHubEnqueuedTime", "Properties":{"Ordinal":"0"}},'
    '   { "column" : "EventHubOffset", "Properties":{"Ordinal":"1"}}'
    ']'
```
 
#### <a name="json-mapping-example"></a>JSON 對應範例

使用出現在 *資料連線* 分頁器 *事件系統屬性* 清單中的系統屬性名稱來加入資料。 執行：

```kusto
    .create table TestTable ingestion json mapping "JsonMapping1"
    '['
    '    { "column" : "Timespan", "Properties":{"Path":"$.timestamp"}},'
    '    { "column" : "Metric", "Properties":{"Path":"$.metric"}},'
    '    { "column" : "Value", "Properties":{"Path":"$.metric_value"}},'
    '    { "column" : "EventHubEnqueuedTime", "Properties":{"Path":"$.x-opt-enqueued-time"}},'
    '    { "column" : "EventHubOffset", "Properties":{"Path":"$.x-opt-offset"}}'
    ']'
```

## <a name="create-event-hub-connection"></a>建立事件中樞連線

> [!Note]
> 為了達到最佳效能，請在與 Azure 資料總管叢集相同的區域中建立所有資源。

### <a name="create-an-event-hub"></a>建立事件中樞

如果您還沒有帳戶，請 [建立一個事件中樞](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)。 您可以在如何 [建立事件中樞](ingest-data-event-hub.md#create-an-event-hub) 指南中找到範本。

> [!Note]
> * 資料分割計數不可變更，您在設定資料分割計數時，應該考慮長期的規模。
> * 每個取用者的取用者群組 *必須* 是唯一的。 建立 Azure 資料總管連接專屬的取用者群組。

#### <a name="generate-data"></a>產生資料

* 請參閱產生資料的 [範例應用程式](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) ，並將其傳送至事件中樞。

事件可以包含一或多筆記錄，最多可達其大小限制。 在下列範例中，我們會傳送兩個事件，每個事件都會附加五筆記錄：

```csharp
var events = new List<EventData>();
var data = string.Empty;
var recordsPerEvent = 5;
var rand = new Random();
var counter = 0;

for (var i = 0; i < 10; i++)
{
    // Create the data
    var metric = new Metric { Timestamp = DateTime.UtcNow, MetricName = "Temperature", Value = rand.Next(-30, 50) }; 
    var data += JsonConvert.SerializeObject(metric) + Environment.NewLine;
    counter++;

    // Create the event
    if (counter == recordsPerEvent)
    {
        var eventData = new EventData(Encoding.UTF8.GetBytes(data));
        events.Add(eventData);

        counter = 0;
        data = string.Empty;
    }
}

// Send events
eventHubClient.SendAsync(events).Wait();
```

## <a name="next-steps"></a>後續步驟

* [將資料從事件中樞內嵌至 Azure 資料總管](ingest-data-event-hub.md)
* [使用 C 建立 Azure 資料總管的事件中樞資料連線#](data-connection-event-hub-csharp.md)
* [使用 Python 建立 Azure 資料總管的事件中樞資料連線](data-connection-event-hub-python.md)
* [使用 Azure Resource Manager 範本，建立 Azure 資料總管的事件中樞資料連線](data-connection-event-hub-resource-manager.md)
