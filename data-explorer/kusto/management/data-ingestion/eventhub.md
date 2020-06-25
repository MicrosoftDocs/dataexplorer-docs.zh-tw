---
title: 從事件中樞內嵌-Azure 資料總管
description: 本文說明如何從 Azure 中的事件中樞內嵌資料總管。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: dbb74d8b718b5a03fa0e9e7f4679f48c837d0842
ms.sourcegitcommit: c3bbb9a6bfd7c5506f05afb4968fdc2043a9fbbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/24/2020
ms.locfileid: "85332552"
---
# <a name="ingest-from-event-hub"></a>從事件中樞內嵌

[Azure 事件中樞](https://docs.microsoft.com/azure/event-hubs/event-hubs-about)是一種龐大的資料串流平臺和事件內嵌服務。 Azure 資料總管可從客戶管理的事件中樞提供持續的內嵌。

## <a name="data-format"></a>資料格式

* 資料會以[EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata?view=azure-dotnet)物件的形式從事件中樞讀取。
* 事件裝載可以包含一或多筆記錄，其中會以[Azure 資料總管支援](../../../ingestion-supported-formats.md)的其中一種格式進行內嵌。
* 您可以使用壓縮演算法來壓縮資料 `GZip` 。 必須指定為內嵌 `Compression` [屬性](#ingestion-properties)。

> [!Note]
> * 壓縮格式不支援資料壓縮（Avro、Parquet、ORC）。
> * 壓縮資料不支援自訂編碼和內嵌的[系統屬性](#event-system-properties-mapping)。

## <a name="ingestion-properties"></a>內嵌屬性

內嵌屬性會指示內嵌程式、路由傳送資料的位置，以及處理該程式的方式。 您可以使用[EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties)指定內嵌事件的內嵌[屬性](../../../ingestion-properties.md)。 您可以設定下列屬性：

|屬性 |說明|
|---|---|
| Table | 現有目標資料表的名稱（區分大小寫）。 覆寫分頁 `Table` 上的集合 `Data Connection` 。 |
| 格式 | 資料格式。 覆寫分頁 `Data format` 上的集合 `Data Connection` 。 |
| IngestionMappingReference | 要使用之現有內嵌[對應](../create-ingestion-mapping-command.md)的名稱。 覆寫分頁 `Column mapping` 上的集合 `Data Connection` 。|
| 壓縮 | 資料壓縮、 `None` （預設值）或 `GZip` 壓縮。|
| 編碼 | 資料編碼，預設值為 UTF8。 可以是任何[.net 支援的編碼](https://docs.microsoft.com/dotnet/api/system.text.encoding?view=netframework-4.8#remarks)。 |
| 標記（預覽） | 要與內嵌資料產生關聯的[標記](../extents-overview.md#extent-tagging)清單，格式為 JSON 陣列字串。 使用標記時，會造成效能上的[影響](../extents-overview.md#performance-notes-1)。 |

<!--| Database | Name of the existing target database.|-->
<!--| Tags | String representing [tags](https://docs.microsoft.com/azure/kusto/management/extents-overview#extent-tagging) that will be attached to resulting extent. |-->

## <a name="events-routing"></a>事件路由

當您設定 Azure 資料總管叢集的事件中樞連線時，您會指定目標資料表屬性（資料表名稱、資料格式、壓縮和對應）。 您資料的預設路由也稱為 `static routing` 。
您也可以使用事件屬性來指定每個事件的目標資料表屬性。 連接會以動態方式路由傳送[EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties)中指定的資料，並覆寫此事件的靜態屬性。

在下列範例中，請設定事件中樞詳細資料，並將氣象計量資料傳送至資料表 `WeatherMetrics` 。
資料的 `json` 格式為。 `mapping1`是在資料表上預先定義的 `WeatherMetrics` 。

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

## <a name="event-system-properties-mapping"></a>事件系統屬性對應

當事件排入佇列時，系統屬性會儲存由事件中樞服務所設定的屬性。 Azure 資料總管事件中樞連接會將選取的屬性內嵌到資料表中的資料登陸。

> [!Note]
> * 單一記錄事件支援系統屬性。
> * 壓縮資料不支援系統屬性。
> * 針對 `csv` 對應，會依照下表所列的順序，在記錄的開頭加入屬性。 針對 `json` 對應，會根據下表中的屬性名稱來加入屬性。

### <a name="event-hub-exposes-the-following-system-properties"></a>事件中樞會公開下列系統屬性

|屬性 |資料類型 |描述|
|---|---|---|
| x-opt-enqueued-time |Datetime | 將事件排入佇列的 UTC 時間 |
| x-opt-sequence-number |long | 事件中樞之分割資料流程內事件的邏輯序號
| x-opt-offset |字串 | 事件中樞分割資料流程的事件位移。 位移識別碼在事件中樞資料流程的資料分割內是唯一的 |
| x-opt-發行者 |字串 | 發行者名稱（如果訊息已傳送至發行者端點） |
| x-opt-partition-key |字串 |儲存事件之對應資料分割的分割區索引鍵 |

如果您在資料表的 [**資料來源**] 區段中選取 [**事件系統屬性**]，則必須在資料表架構和對應中包含屬性。

**資料表架構範例**

如果您的資料包含下列內容，請使用 [資料表架構] 命令來建立或更改資料表架構：
* `Timespan`、和資料 `Metric` 行`Value`  
* 屬性 `x-opt-enqueued-time` 和`x-opt-offset`

```kusto
    .create-merge table TestTable (TimeStamp: datetime, Metric: string, Value: int, EventHubEnqueuedTime:datetime, EventHubOffset:long)
```

**CSV 對應範例**

執行下列命令，將資料新增至記錄的開頭。
屬性會依照上表所列的順序，在記錄的開頭加入。
序數值對於 CSV 對應很重要，其中資料行序數會根據所對應的系統屬性而變更。

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
 
**JSON-對應範例**

使用 [*資料連線*] 分頁*事件系統屬性*清單中所顯示的系統屬性名稱來加入資料。 請執行：

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
> 為了達到最佳效能，請在 Azure 資料總管叢集所在的相同區域中建立所有資源。

### <a name="create-an-event-hub"></a>建立事件中樞

如果您還沒有帳戶，請[建立一個事件中樞](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)。 您可以在如何[建立事件中樞](../../../ingest-data-event-hub.md#create-an-event-hub)指南中找到範本。

> [!Note]
> * 資料分割計數不可變更，您在設定資料分割計數時，應該考慮長期的規模。
> * 取用者群組在每個取用者中*必須*是唯一的。 建立 Azure 資料總管連接專用的取用者群組。

### <a name="data-ingestion-connection-to-azure-data-explorer"></a>Azure 資料總管的資料內嵌連接

* 透過 Azure 入口網站：[連接到事件中樞](../../../ingest-data-event-hub.md#connect-to-the-event-hub)。
* 使用 Azure 資料總管 management .NET SDK：[新增事件中樞資料](../../../data-connection-event-hub-csharp.md#add-an-event-hub-data-connection)連線
* 使用 Azure 資料總管管理 Python SDK：[新增事件中樞資料](../../../data-connection-event-hub-python.md#add-an-event-hub-data-connection)連線
* 使用 ARM 範本：使用[Azure Resource Manager 範本來新增事件中樞資料](../../../data-connection-event-hub-resource-manager.md#azure-resource-manager-template-for-adding-an-event-hub-data-connection)連線

> [!Note]
> 如果 [我的資料] 包含選取的 [**路由資訊**]，您*必須*提供必要的[路由](#events-routing)資訊做為事件屬性的一部分。

> [!Note]
> 設定連線之後，它會從在建立時間排入佇列的事件開始內嵌資料。

#### <a name="generating-data"></a>產生資料

* 請參閱產生資料的[範例應用程式](https://github.com/Azure-Samples/event-hubs-dotnet-ingest)，並將其傳送至事件中樞。

事件可包含一筆或多筆記錄（最大為其大小限制）。 在下列範例中，我們會傳送兩個事件，每個都有附加五筆記錄：

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
