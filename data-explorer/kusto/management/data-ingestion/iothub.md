---
title: 從 IoT 中心引入 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的 IoT 中心中的引入。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: 33b6f4f737657ee0a6c2712f3b7cadce0c9c0a8f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521347"
---
# <a name="ingest-from-iot-hub"></a>從 IoT 中心引入

[Azure IoT 中心](https://docs.microsoft.com/azure/iot-hub/about-iot-hub)是託管服務,託管在雲中,充當 IoT 應用程式與它管理的設備之間的雙向通信的中央消息中心。 Azure 資料資源管理器使用其[相容內置的終結點](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c#routing-endpoints)提供來自客戶託管 IoT 中心的連續引入。

## <a name="data-format"></a>資料格式

* 數據以[事件數據](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata?view=azure-dotnet)物件的形式從事件中心終結點讀取。
* 事件有效負載可以採用 Azure[資料資源管理員支援的格式](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats)之一。

## <a name="ingestion-properties"></a>內嵌屬性

攝入屬性指示攝取過程。 在何處路由數據以及如何處理數據。 您可以使用[EventData.屬性](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties)指定事件引入[屬性](https://docs.microsoft.com/azure/data-explorer/ingestion-properties)。 您可以設定下列屬性：

|屬性 |描述|
|---|---|
| Table | 現有目標表的名稱(區分大小寫)。 覆蓋邊欄`Table`選項卡`Data Connection`上的 集。 |
| [格式] | 資料格式。 覆蓋邊欄`Data format`選項卡`Data Connection`上的 集。 |
| 引入對應 | 要使用的現有[引入映射](../create-ingestion-mapping-command.md)的名稱。 覆蓋邊欄`Column mapping`選項卡`Data Connection`上的 集。|
| 編碼 |  數據編碼,預設值為 UTF8。 可以是[.NET 支援的任何編碼](https://docs.microsoft.com/dotnet/api/system.text.encoding?view=netframework-4.8#remarks)。 |

## <a name="events-routing"></a>事件路由

設置到 Azure 資料資源管理器群集的 IoT 中心連接時,可以指定目標表屬性(表名稱、資料格式和映射)。 這是資料的預設路由,也稱為`static routing`。
您還可以使用事件屬性為每個事件指定目標表屬性。 連接將動態路由[事件數據.屬性](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties)中指定的數據,覆蓋此事件的靜態屬性。

## <a name="event-system-properties-mapping"></a>事件系統屬性對應

系統屬性是用於存儲IoT中心服務在接收事件時設置的屬性的集合。 Azure 資料資源管理員 IoT 中心連接將所選屬性嵌入到表中的數據著陸中。

> [!Note]
> * 對於`csv`映射,屬性按下表中列出的順序在記錄的開頭添加。 對於`json`映射,根據下表中的屬性名稱添加屬性。

### <a name="iot-hub-exposes-the-following-system-properties"></a>IoT 中心公開以下系統屬性:

|屬性 |描述|
|---|---|
| message-id | 使用者可設定的訊息識別碼，用於「要求-回覆」模式。 |
| sequence-number | IoT 中樞指派給每則雲端到裝置訊息的數字 (對每個裝置佇列而言都是唯一的)。 |
| to | 雲端到裝置 訊息中指定的目的地。 |
| absolute-expiry-time | 訊息到期的日期和時間。 |
| iothub-enqueuedtime | IoT 中心接收設備到雲消息的日期和時間。 |
| correlation-id| 回應訊息中的字串屬性，通常包含採用「要求-回覆」模式之要求的 MessageId。 |
| user-id| 用來指定訊息來源的識別碼。 |
| iothub-ack| 意見反應訊息產生器。 |
| iothub-connection-device-id| 由 IoT 中樞在裝置到雲端訊息上設定的識別碼。 它包含傳送訊息之裝置的 deviceId 。 |
| iothub-connection-auth-generation-id| 由 IoT 中樞在裝置到雲端訊息上設定的識別碼。 它包含發送消息的設備的連接DeviceGenerationId(根據設備識別屬性)。 |
| iothub-connection-auth-method| 由 IoT 中樞在裝置到雲端訊息上設定的驗證方法。 這個屬性包含用來驗證傳送訊息之裝置的驗證方法的相關資訊。 |

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

## <a name="create-iot-hub-connection"></a>建立 IoT 中心連線

> [!Note]
> 為獲得最佳性能,請創建與 Azure 資料資源管理器群集相同的區域中的所有資源。

### <a name="create-an-iot-hub"></a>建立 IoT 中樞

如果還沒有,[請建立 Iot 中 。](https://docs.microsoft.com/azure/data-explorer/ingest-data-iot-hub#create-an-iot-hub)

> [!Note]
> * 計數`device-to-cloud partitions`不可更改,因此在設置分區計數時應考慮長期比例。
> * 消費者群體*必須是*每個消費者的單一。 創建專用於 Kusto 連接的消費者組。 在 Azure 門戶中查找資源`Built-in endpoints`,然後 轉到添加新消費者組。

### <a name="data-ingestion-connection-to-azure-data-explorer"></a>到 Azure 資料資源管理員的資料連線

* 透過 Azure 閘戶:[將 Azure 資料資源管理員表連接到 IoT 中心](https://docs.microsoft.com/azure/data-explorer/ingest-data-iot-hub#connect-azure-data-explorer-table-to-iot-hub)。
* 使用 Azure 資料資源管理者管理 .NET SDK:[新增 IoT 中心資料連線](https://docs.microsoft.com/azure/data-explorer/data-connection-iot-hub-csharp#add-an-iot-hub-data-connection)
* 使用 Azure 資料資源管理 Python SDK:[新增 IoT 中心資料連線](https://docs.microsoft.com/azure/data-explorer/data-connection-iot-hub-python#add-an-iot-hub-data-connection)
* 使用 ARM 樣本:[用於新增 Iot 中心資料連線的 Azure 資源管理員樣本](https://docs.microsoft.com/azure/data-explorer/data-connection-iot-hub-resource-manager#azure-resource-manager-template-for-adding-an-iot-hub-data-connection)

> [!Note]
> 如果 **"我的數據"包含所選的路由資訊**,*則必須*作為事件屬性的一部分提供必要的[路由](#events-routing)資訊。

> [!Note]
> 設置連接后,它會從創建時間後排隊的事件開始引入數據。

### <a name="generating-data"></a>組建資料

* 檢視模擬裝置與產生資料[的範例項目](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Quickstarts/simulated-device)。