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
ms.openlocfilehash: b8ba6199d5353ffd34081483c2ffbbd73e88a60c
ms.sourcegitcommit: 3af95ea6a6746441ac71b1a217bbb02ee23d5f28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2020
ms.locfileid: "95473517"
---
# <a name="event-hub-data-connection"></a>事件中樞資料連線

[Azure 事件中樞](/azure/event-hubs/event-hubs-about) 是大型的資料串流平臺和事件內嵌服務。 Azure 資料總管可從客戶管理的事件中樞提供連續的內嵌。

事件中樞內嵌管線會以數個步驟將事件傳輸至 Azure 資料總管。 您會先在 Azure 入口網站中建立事件中樞。 然後，您會在 Azure 中建立目標資料表，資料總管將會使用指定的內嵌[屬性](#ingestion-properties)內嵌[特定格式的資料](#data-format)。 事件中樞連接需要知道 [事件路由](#events-routing)。 資料會根據 [事件系統屬性對應](#event-system-properties-mapping)，以選取的屬性內嵌。 [建立](#event-hub-connection) 事件中樞的連線，以 [建立事件中樞](#create-an-event-hub) 並 [傳送事件](#send-events)。 您可以透過 [Azure 入口網站](ingest-data-event-hub.md)、使用 [c #](data-connection-event-hub-csharp.md) 或 [Python](data-connection-event-hub-python.md)以程式設計方式，或使用 [Azure Resource Manager 範本](data-connection-event-hub-resource-manager.md)來管理此程式。

如需 Azure 資料總管中資料內嵌的一般資訊，請參閱 [azure 資料總管資料內嵌總覽](ingest-data-overview.md)。

## <a name="data-format"></a>資料格式

* 會以 [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata?view=azure-dotnet) 物件的形式從事件中樞讀取資料。
* 請參閱 [支援的格式](ingestion-supported-formats.md)。
    > [!NOTE]
    > 事件中樞不支援 raw 格式。

* 您可以使用壓縮演算法來壓縮資料 `GZip` 。 `Compression`在內嵌[屬性](#ingestion-properties)中指定。
   * 壓縮格式不支援資料壓縮 (Avro、Parquet、ORC) 。
   * 壓縮資料不支援自訂編碼和內嵌的 [系統屬性](#event-system-properties-mapping) 。
  
## <a name="ingestion-properties"></a>內嵌屬性

內嵌屬性會指示內嵌進程、路由資料的位置，以及如何處理它。 您可以使用[EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties)來指定內嵌事件的內嵌[屬性](ingestion-properties.md)。 您可以設定下列屬性：

|屬性 |描述|
|---|---|
| Table |  (現有目標資料表的區分大小寫) 名稱。 覆寫 `Table` 窗格上的集合 `Data Connection` 。 |
| 格式 | 資料格式。 覆寫 `Data format` 窗格上的集合 `Data Connection` 。 |
| IngestionMappingReference | 要使用之現有內嵌 [對應](kusto/management/create-ingestion-mapping-command.md) 的名稱。 覆寫 `Column mapping` 窗格上的集合 `Data Connection` 。|
| 壓縮 | 資料壓縮、 `None` (預設) 或 `GZip` 壓縮。|
| 編碼 | 資料編碼，預設值為 UTF8。 可以是任何 [.net 支援的編碼](/dotnet/api/system.text.encoding?view=netframework-4.8#remarks)方式。 |
| 標籤 | 要與內嵌資料產生關聯的 [標記](kusto/management/extents-overview.md#extent-tagging) 清單，格式為 JSON 陣列字串。 使用標記時，會 [影響效能](kusto/management/extents-overview.md#performance-notes-1) 。 |

> [!NOTE]
> 只有在建立資料連線之後排入佇列的事件才會內嵌。

## <a name="events-routing"></a>事件路由

當您設定 Azure 資料總管叢集的事件中樞連線時，您可以 (資料表名稱、資料格式、壓縮和對應) 來指定目標資料表屬性。 您資料的預設路由也稱為 `static routing` 。
您也可以使用事件屬性，為每個事件指定目標資料表屬性。 連接會動態路由 [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties)中指定的資料，並覆寫這個事件的靜態屬性。

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

## <a name="event-system-properties-mapping"></a>事件系統屬性對應

系統屬性會儲存事件排入佇列時，由事件中樞服務所設定的屬性。 Azure 資料總管事件中樞連接會將選取的屬性內嵌至資料表中的資料登陸。

> [!Note]
> * 單一記錄事件支援系統屬性。
> * 壓縮資料不支援系統屬性。
> * 針對 `csv` 對應，會依下表所列的順序，在記錄的開頭加入屬性。 針對對應 `json` ，會根據下表中的屬性名稱來新增屬性。

### <a name="system-properties"></a>系統屬性

事件中樞會公開下列系統屬性：

|屬性 |資料類型 |描述|
|---|---|---|
| x-opt-enqueued-time |Datetime | 事件排入佇列時的 UTC 時間 |
| x-opt-sequence-number |long | 事件中樞之分割資料流程內事件的邏輯序號
| x-opt-offset |字串 | 事件中樞分割區資料流程中事件的位移。 位移識別碼在事件中樞資料流程的資料分割內是唯一的 |
| x-選擇發行者 |字串 | 發行者名稱（如果訊息已傳送至發行者端點） |
| x-opt-partition-key |字串 |儲存事件之對應分割區的分割區索引鍵 |

如果您在資料表的 [**資料來源**] 區段中選取 [**事件系統屬性**]，就必須在資料表架構和對應中包含屬性。

[!INCLUDE [data-explorer-container-system-properties](includes/data-explorer-container-system-properties.md)]

## <a name="event-hub-connection"></a>事件中樞連線

> [!Note]
> 為了達到最佳效能，請在與 Azure 資料總管叢集相同的區域中建立所有資源。

### <a name="create-an-event-hub"></a>建立事件中樞

如果您還沒有帳戶，請 [建立一個事件中樞](/azure/event-hubs/event-hubs-create)。 您可以透過 [Azure 入口網站](ingest-data-event-hub.md)、使用 [c #](data-connection-event-hub-csharp.md) 或 [Python](data-connection-event-hub-python.md)以程式設計方式，或使用 [Azure Resource Manager 範本](data-connection-event-hub-resource-manager.md)來管理連線到事件中樞。


> [!Note]
> * 資料分割計數不可變更，您在設定資料分割計數時，應該考慮長期的規模。
> * 每個取用者的取用者群組 *必須* 是唯一的。 建立 Azure 資料總管連接專屬的取用者群組。

## <a name="send-events"></a>傳送事件

請參閱產生資料的 [範例應用程式](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) ，並將其傳送至事件中樞。

如需如何產生範例資料的範例，請參閱將 [資料從事件中樞內嵌至 Azure 資料總管](ingest-data-event-hub.md#generate-sample-data)

## <a name="set-up-geo-disaster-recovery-solution"></a>設定異地災難復原解決方案

事件中樞提供 [異地災難](/azure/event-hubs/event-hubs-geo-dr) 復原解決方案。 Azure 資料總管不支援 `Alias` 事件中樞命名空間。 若要在您的解決方案中執行異地災難復原，請建立兩個事件中樞資料連線：一個用於主要命名空間，另一個用於次要命名空間。 Azure 資料總管會接聽這兩個事件中樞連線。

> [!NOTE]
> 使用者必須負責執行從主要命名空間到次要命名空間的容錯移轉。

## <a name="next-steps"></a>後續步驟

* [將資料從事件中樞內嵌至 Azure 資料總管](ingest-data-event-hub.md)
* [使用 C 建立 Azure 資料總管的事件中樞資料連線#](data-connection-event-hub-csharp.md)
* [使用 Python 建立 Azure 資料總管的事件中樞資料連線](data-connection-event-hub-python.md)
* [使用 Azure Resource Manager 範本，建立 Azure 資料總管的事件中樞資料連線](data-connection-event-hub-resource-manager.md)
