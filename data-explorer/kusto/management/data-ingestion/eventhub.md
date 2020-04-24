---
title: 從事件中心引入 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中事件中心的引入。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: ddb218e707152f529e5b547ffe06c4d3c7614811
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521500"
---
# <a name="ingest-from-event-hub"></a>從事件中心引入

[Azure 事件中心](https://docs.microsoft.com/azure/event-hubs/event-hubs-about)是一個大數據流式處理平臺和事件引入服務。 Azure 資料資源管理員提供來自客戶託管的事件中心的持續引入。 

## <a name="data-format"></a>資料格式

* 以[事件數據](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata?view=azure-dotnet)物件的形式從事件中心讀取數據。
* 事件有效負載可以包含一個或多個要引入的記錄,其格式之一[為 Azure 資料資源管理員](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats)支援。
* 可以使用`GZip`壓縮演演演算法壓縮數據。 必須指定為`Compression`[引入屬性](#ingestion-properties)。

> [!Note]
> * 壓縮格式(Avro、Parquet、ORC)不支援數據壓縮。
> * 壓縮資料不支援自訂編碼與嵌入[的系統屬性](#event-system-properties-mapping)。

## <a name="ingestion-properties"></a>內嵌屬性

攝入屬性指示攝取過程。 在何處路由數據以及如何處理數據。 您可以使用[EventData.屬性](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties)指定事件引入[屬性](https://docs.microsoft.com/azure/data-explorer/ingestion-properties)。 您可以設定下列屬性：

|屬性 |描述|
|---|---|
| Table | 現有目標表的名稱(區分大小寫)。 覆蓋邊欄`Table`選項卡`Data Connection`上的 集。 |
| [格式] | 資料格式。 覆蓋邊欄`Data format`選項卡`Data Connection`上的 集。 |
| 引入對應 | 要使用的現有[引入映射](../create-ingestion-mapping-command.md)的名稱。 覆蓋邊欄`Column mapping`選項卡`Data Connection`上的 集。|
| 壓縮 | 資料壓縮(`None`預設)`GZip`或壓縮。|
| 編碼 |  數據編碼,預設值為 UTF8。 可以是[.NET 支援的任何編碼](https://docs.microsoft.com/dotnet/api/system.text.encoding?view=netframework-4.8#remarks)。 |

<!--| Database | Name of the existing target database.|-->
<!--| Tags | String representing [tags](https://docs.microsoft.com/azure/kusto/management/extents-overview#extent-tagging) that will be attached to resulting extent. |-->

## <a name="events-routing"></a>事件路由

將事件中心連接到 Azure 資料資源管理器群集時,可以指定目標表屬性(表名稱、資料格式、壓縮和映射)。 這是資料的預設路由,也稱為`static routing`。
您還可以使用事件屬性為每個事件指定目標表屬性。 連接將動態路由[事件數據.屬性](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties)中指定的數據,覆蓋此事件的靜態屬性。

在下面的範例中,設定事件中心詳細資訊並將天氣指標資料傳送到`WeatherMetrics`表 。
數據採用`json`格式。 `mapping1`在表`WeatherMetrics`格上預定義:

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

系統屬性是一個集合,用於在事件排隊時存儲事件中心服務設置的屬性。 Azure 資料資源管理器事件中心連接將嵌入到表中的數據著陸中。

> [!Note]
> * 單記錄事件支援系統屬性。
> * 壓縮數據不支援系統屬性。
> * 對於`csv`映射,屬性按下表中列出的順序在記錄的開頭添加。 對於`json`映射,根據下表中的屬性名稱添加屬性。

### <a name="event-hub-expose-the-following-system-properties"></a>事件中心公開以下系統屬性

|屬性 |資料類型 |描述|
|---|---|---|
| x-opt-enqueued-time |Datetime | 事件排隊時的 UTC 時間。 |
| x-opt-sequence-number |long | 事件中心分區流中事件的邏輯序列號。
| x-opt-offset |字串 | 事件相對於事件中心分區流的偏移量。 偏移標識碼在事件中心流的分區中是唯一的。 |
| X 選擇宣告者 |字串 | 如果消息已發送到發佈者終結點,則發佈者名稱。 |
| x-opt-partition-key |字串 |存儲事件的相應分區的分區鍵。 |

如果在表的 **「資料源」** 部分中選擇了**事件系統屬性**,則必須在表架構和映射中包括這些屬性。

**表架構範例**

如果資料包含三`Timespan`欄`Metric`(、`Value`與 ) 以及`x-opt-enqueued-time``x-opt-offset`包含的屬性是與, 請使用此指令建立或變更表架構:

```kusto
    .create-merge table TestTable (TimeStamp: datetime, Metric: string, Value: int, EventHubEnqueuedTime:datetime, EventHubOffset:long)
```

**CSV 對應範例**

運行以下命令將數據添加到記錄的開頭。 注意序號值:屬性按上表中列出的順序在記錄的開頭添加。 這對 CSV 映射非常重要,其中列序列將根據映射的系統屬性進行更改。

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

數據是透過使用系統屬性名稱添加的,因為它們顯示在 **「資料連接**邊欄選項卡**事件」系統屬性**清單中。 執行以下命令：

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
> 為獲得最佳性能,請創建與 Azure 資料資源管理器群集相同的區域中的所有資源。

### <a name="create-an-event-hub"></a>建立事件中心

如果還沒有,[請建立一個事件中心](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)。 可以在「如何[創建事件中心](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub#create-an-event-hub)指南」中找到範本。

> [!Note]
> * 資料分割計數不可變更，您在設定資料分割計數時，應該考慮長期的規模。
> * 消費者群體*必須是*每個消費者的單一。 創建專用於 Azure 資料資源管理器連接的消費群體。

### <a name="data-ingestion-connection-to-azure-data-explorer"></a>到 Azure 資料資源管理員的資料連線

* 透過 Azure 門戶:[連線到事件中心](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub#connect-to-the-event-hub)。
* 使用 Azure 資料資源管理者管理 .NET SDK:[新增事件中心資料連線](https://docs.microsoft.com/azure/data-explorer/data-connection-event-hub-csharp#add-an-event-hub-data-connection)
* 使用 Azure 資料資源管理 Python SDK:[新增事件中心資料連線](https://docs.microsoft.com/azure/data-explorer/data-connection-event-hub-python#add-an-event-hub-data-connection)
* 使用 ARM 樣本:[用於新增事件中心資料連線的 Azure 資源管理員樣本](https://docs.microsoft.com/azure/data-explorer/data-connection-event-hub-resource-manager#azure-resource-manager-template-for-adding-an-event-hub-data-connection)

> [!Note]
> 如果 **"我的數據"包含所選的路由資訊**,*則必須*作為事件屬性的一部分提供必要的[路由](#events-routing)資訊。

> [!Note]
> 設置連接后,它會從創建時間後排隊的事件開始引入數據。

#### <a name="generating-data"></a>組建資料

* 檢視產生資料並將傳送到事件中心[的範例應用程式](https://github.com/Azure-Samples/event-hubs-dotnet-ingest)。

事件可以包含一個或多個記錄,最多其大小限制。 在下面的範例中,我們發送兩個事件,每個事件都附加了 5 條記錄:

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