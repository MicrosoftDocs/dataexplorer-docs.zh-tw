---
title: 從事件中樞內嵌-Azure 資料總管 |Microsoft Docs
description: 本文說明如何從 Azure 中的事件中樞內嵌資料總管。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: fac9fd9f218948928e4f91d0d1aa056affcebd11
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617658"
---
# <a name="ingest-from-event-hub"></a>從事件中樞內嵌

[Azure 事件中樞](https://docs.microsoft.com/azure/event-hubs/event-hubs-about)是一種龐大的資料串流平臺和事件內嵌服務。 Azure 資料總管可從客戶管理的事件中樞提供持續的內嵌。 

## <a name="data-format"></a>資料格式

* 資料會以[EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata?view=azure-dotnet)物件的形式從事件中樞讀取。
* 事件裝載可以包含一或多個要內嵌的記錄，其格式為[Azure 資料總管支援](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats)的其中一種格式。
* 您可以使用`GZip`壓縮演算法來壓縮資料。 必須指定為`Compression`內嵌[屬性](#ingestion-properties)。

> [!Note]
> * 壓縮格式不支援資料壓縮（Avro、Parquet、ORC）。
> * 壓縮資料不支援自訂編碼和內嵌的[系統屬性](#event-system-properties-mapping)。

## <a name="ingestion-properties"></a>內嵌屬性

內嵌屬性會指示內嵌進程。 資料的路由和處理方式。 您可以使用[EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties)指定內嵌事件的內嵌[屬性](https://docs.microsoft.com/azure/data-explorer/ingestion-properties)。 您可以設定下列屬性：

|屬性 |描述|
|---|---|
| Table | 現有目標資料表的名稱（區分大小寫）。 覆寫`Table`分頁上`Data Connection`的集合。 |
| [格式] | 資料格式。 覆寫`Data format`分頁上`Data Connection`的集合。 |
| IngestionMappingReference | 要使用之現有內嵌[對應](../create-ingestion-mapping-command.md)的名稱。 覆寫`Column mapping`分頁上`Data Connection`的集合。|
| 壓縮 | 資料壓縮、 `None` （預設）或`GZip`壓縮。|
| 編碼 |  資料編碼，預設值為 UTF8。 可以是任何[.net 支援的編碼](https://docs.microsoft.com/dotnet/api/system.text.encoding?view=netframework-4.8#remarks)。 |

<!--| Database | Name of the existing target database.|-->
<!--| Tags | String representing [tags](https://docs.microsoft.com/azure/kusto/management/extents-overview#extent-tagging) that will be attached to resulting extent. |-->

## <a name="events-routing"></a>事件路由

設定 Azure 資料總管叢集的事件中樞連線時，您會指定目標資料表屬性（資料表名稱、資料格式、壓縮和對應）。 這是您的資料的預設路由，也稱為`static routing`。
您也可以使用事件屬性來指定每個事件的目標資料表屬性。 連接會以動態方式路由傳送[EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties)中指定的資料，並覆寫此事件的靜態屬性。

在下列範例中，設定事件中樞詳細資料，並將氣象計量資料`WeatherMetrics`傳送至資料表。
資料的`json`格式為。 `mapping1`已在資料表`WeatherMetrics`上預先定義：

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

// Send events
var eventHubClient = EventHubClient.CreateFromConnectionString(eventHubNamespaceConnectionString, eventHubName);
eventHubClient.Send(eventData);
eventHubClient.Close();
```

## <a name="event-system-properties-mapping"></a>事件系統屬性對應

系統屬性是在事件排入佇列時，用來儲存事件中樞服務所設定之屬性的集合。 Azure 資料總管事件中樞連接會將選取的屬性內嵌到資料表中的資料登陸。

> [!Note]
> * 單一記錄事件支援系統屬性。
> * 壓縮資料不支援系統屬性。
> * 針對`csv`對應，會依照下表所列的順序，在記錄的開頭加入屬性。 針對`json`對應，會根據下表中的屬性名稱來加入屬性。

### <a name="event-hub-expose-the-following-system-properties"></a>事件中樞會公開下列系統屬性

|屬性 |資料類型 |描述|
|---|---|---|
| x-opt-enqueued-time |Datetime | 將事件排入佇列的 UTC 時間。 |
| x-opt-sequence-number |long | 事件中樞之分割資料流程內事件的邏輯序號。
| x-opt-offset |字串 | 相對於事件中樞分割區資料流程的事件位移。 位移識別碼在事件中樞資料流程的資料分割內是唯一的。 |
| x-opt-發行者 |字串 | 如果訊息已傳送至發行者端點，則為發行者名稱。 |
| x-opt-partition-key |字串 |儲存事件之對應資料分割的資料分割索引鍵。 |

如果您在資料表的 [**資料來源**] 區段中選取 [**事件系統屬性**]，則必須在資料表架構和對應中包含屬性。

**資料表架構範例**

如果您的資料包含三`Timespan`個`Metric`資料行`Value`（、和），而您`x-opt-enqueued-time`包含`x-opt-offset`的屬性為和，請使用下列命令來建立或修改資料表架構：

```kusto
    .create-merge table TestTable (TimeStamp: datetime, Metric: string, Value: int, EventHubEnqueuedTime:datetime, EventHubOffset:long)
```

**CSV 對應範例**

執行下列命令，將資料新增至記錄的開頭。 注意序數值：屬性會依照上表所列的順序加入記錄的開頭。 這對於 CSV 對應很重要，其中資料行序數會根據所對應的系統屬性而變更。

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
 
**JSON 對應範例**

使用 [**資料連線**] 分頁**事件系統屬性**清單中所顯示的系統屬性名稱來加入資料。 執行下列命令：

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

### <a name="create-an-event-hub"></a>建立事件中心

如果您還沒有帳戶，請[建立一個事件中樞](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)。 您可以在如何[建立事件中樞](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub#create-an-event-hub)指南中找到範本。

> [!Note]
> * 資料分割計數不可變更，您在設定資料分割計數時，應該考慮長期的規模。
> * 取用者群組*必須*uniqe 每個取用者。 建立 Azure 資料總管連接專用的取用者群組。

### <a name="data-ingestion-connection-to-azure-data-explorer"></a>Azure 資料總管的資料內嵌連接

* 透過 Azure 入口網站：[連接到事件中樞](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub#connect-to-the-event-hub)。
* 使用 Azure 資料總管 management .NET SDK：[新增事件中樞資料](https://docs.microsoft.com/azure/data-explorer/data-connection-event-hub-csharp#add-an-event-hub-data-connection)連線
* 使用 Azure 資料總管管理 Python SDK：[新增事件中樞資料](https://docs.microsoft.com/azure/data-explorer/data-connection-event-hub-python#add-an-event-hub-data-connection)連線
* 使用 ARM 範本：[用於新增事件中樞資料連線的 Azure Resource Manager 範本](https://docs.microsoft.com/azure/data-explorer/data-connection-event-hub-resource-manager#azure-resource-manager-template-for-adding-an-event-hub-data-connection)

> [!Note]
> 如果 [我的資料] 包含選取的 [**路由資訊**]，您*必須*提供必要的[路由](#events-routing)資訊做為事件屬性的一部分。

> [!Note]
> 設定連線之後，它會從在建立時間排入佇列的事件開始內嵌資料。

#### <a name="generating-data"></a>產生資料

* 請參閱產生資料的[範例應用程式](https://github.com/Azure-Samples/event-hubs-dotnet-ingest)，並將其傳送至事件中樞。

事件可包含一筆或多筆記錄（最大為其大小限制）。 在下列範例中，我們會傳送兩個事件，每個都附加5筆記錄：

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
