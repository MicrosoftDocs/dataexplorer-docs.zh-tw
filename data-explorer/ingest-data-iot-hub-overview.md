---
title: 從 IoT 中樞內嵌-Azure 資料總管 |Microsoft Docs
description: 本文說明如何從 Azure 資料總管中的 IoT 中樞內嵌。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 08/13/2020
ms.openlocfilehash: 5437a4ecb77b81e7ffd0e60dfa3bacb76240a094
ms.sourcegitcommit: f2f9cc0477938da87e0c2771c99d983ba8158789
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/07/2020
ms.locfileid: "89502699"
---
# <a name="create-a-connection-to-iot-hub"></a>建立連至 IoT 中樞的連線

[Azure IoT 中樞](https://docs.microsoft.com/azure/iot-hub/about-iot-hub) 是裝載于雲端中的受控服務，可作為 IoT 應用程式與其管理裝置之間雙向通訊的中央訊息中樞。 Azure 資料總管使用其 [與事件中樞相容的內建端點](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c#routing-endpoints)，從客戶管理的 IoT 中樞提供連續的內嵌功能。

IoT 內嵌管線會經歷數個步驟。 首先，您要建立 IoT 中樞，並向其註冊裝置。 然後，您會在 Azure 中建立目標資料表，資料總管將會使用指定的內嵌[屬性](#set-ingestion-properties)內嵌[特定格式的資料](#data-format)。 Iot 中樞連線需要知道要連線至 Azure 資料總管資料表的 [事件路由](#set-events-routing) 。 資料會根據 [事件系統屬性對應](#set-event-system-properties-mapping)，以選取的屬性內嵌。 您可以透過 [Azure 入口網站](ingest-data-iot-hub.md)、使用 [c #](data-connection-iot-hub-csharp.md) 或 [Python](data-connection-iot-hub-python.md)以程式設計方式，或使用 [Azure Resource Manager 範本](data-connection-iot-hub-resource-manager.md)來管理此程式。

如需 Azure 資料總管中資料內嵌的一般資訊，請參閱 [azure 資料總管資料內嵌總覽](ingest-data-overview.md)。

## <a name="data-format"></a>資料格式

* 會以 [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata?view=azure-dotnet) 物件的形式從事件中樞端點讀取資料。
* 請參閱 [支援的格式](ingestion-supported-formats.md)。
    > [!NOTE]
    > IoT 中樞不支援 raw 格式。
* 請參閱 [支援的壓縮](ingestion-supported-formats.md#supported-data-compression-formats)。
  * 原始未壓縮的資料大小應該是 blob 中繼資料的一部分，否則 Azure 資料總管將會加以評估。 每個檔案的內嵌未壓縮大小限制為 4 GB。

## <a name="set-ingestion-properties"></a>設定內嵌屬性

內嵌屬性會指示要路由傳送資料的位置，以及處理資料的方式。 您可以使用[EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties)指定事件的內嵌[屬性](ingestion-properties.md)。 您可以設定下列屬性：

|屬性 |描述|
|---|---|
| Table |  (現有目標資料表的區分大小寫) 名稱。 覆寫 `Table` 窗格上的集合 `Data Connection` 。 |
| 格式 | 資料格式。 覆寫 `Data format` 窗格上的集合 `Data Connection` 。 |
| IngestionMappingReference | 要使用之現有內嵌 [對應](kusto/management/create-ingestion-mapping-command.md) 的名稱。 覆寫 `Column mapping` 窗格上的集合 `Data Connection` 。|
| 編碼 |  資料編碼，預設值為 UTF8。 可以是任何 [.net 支援的編碼](https://docs.microsoft.com/dotnet/api/system.text.encoding?view=netframework-4.8#remarks)方式。 |

## <a name="set-events-routing"></a>設定事件路由

設定 Azure 資料總管叢集的 IoT 中樞連線時，您可以 (資料表名稱、資料格式和對應) 來指定目標資料表屬性。 這項設定是您的資料的預設路由，也稱為靜態路由。
您也可以使用事件屬性，為每個事件指定目標資料表屬性。 連接會動態路由 [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties)中指定的資料，並覆寫這個事件的靜態屬性。

> [!Note]
> 如果 **我的資料包含選取的路由資訊** ，您就必須提供必要的路由資訊作為 events 屬性的一部分。

## <a name="set-event-system-properties-mapping"></a>設定事件系統屬性對應

系統屬性是一種集合，用來儲存 IoT 中樞服務在收到事件時所設定的屬性。 Azure 資料總管 IoT 中樞連線會在資料表的資料登陸中內嵌選取的屬性。

> [!Note]
> 針對 `csv` 對應，會依下表所列的順序，在記錄的開頭加入屬性。 針對對應 `json` ，會根據下表中的屬性名稱來新增屬性。

### <a name="system-properties"></a>系統屬性

IoT 中樞會公開下列系統屬性：

|屬性 |描述|
|---|---|
| message-id | 使用者可設定的訊息識別碼，用於「要求-回覆」模式。 |
| sequence-number | IoT 中樞指派給每則雲端到裝置訊息的數字 (對每個裝置佇列而言都是唯一的)。 |
| 至 | 雲端到裝置 訊息中指定的目的地。 |
| absolute-expiry-time | 訊息到期的日期和時間。 |
| iothub-enqueuedtime | IoT 中樞收到裝置到雲端訊息的日期和時間。 |
| correlation-id| 回應訊息中的字串屬性，通常包含採用「要求-回覆」模式之要求的 MessageId。 |
| user-id| 用來指定訊息來源的識別碼。 |
| iothub-ack| 意見反應訊息產生器。 |
| iothub-connection-device-id| 由 IoT 中樞在裝置到雲端訊息上設定的識別碼。 它包含傳送訊息之裝置的 deviceId 。 |
| iothub-connection-auth-generation-id| 由 IoT 中樞在裝置到雲端訊息上設定的識別碼。 包含傳送訊息之裝置的 connectionDeviceGenerationId (依據裝置身分識別屬性)。 |
| iothub-connection-auth-method| 由 IoT 中樞在裝置到雲端訊息上設定的驗證方法。 這個屬性包含用來驗證傳送訊息之裝置的驗證方法的相關資訊。 |

如果您在資料表的 [**資料來源**] 區段中選取 [**事件系統屬性**]，就必須在資料表架構和對應中包含屬性。

[!INCLUDE [data-explorer-container-system-properties](includes/data-explorer-container-system-properties.md)]

## <a name="create-iot-hub-connection"></a>建立 IoT 中樞連線

> [!Note]
> 為了達到最佳效能，請在與 Azure 資料總管叢集相同的區域中建立所有資源。

### <a name="create-an-iot-hub"></a>建立 IoT 中樞

如果您還沒有帳戶，請 [建立一個 Iot 中樞](ingest-data-iot-hub.md#create-an-iot-hub)。 您可以透過 [Azure 入口網站](ingest-data-iot-hub.md)、使用 [c #](data-connection-iot-hub-csharp.md) 或 [Python](data-connection-iot-hub-python.md)以程式設計方式，或使用 [Azure Resource Manager 範本](data-connection-iot-hub-resource-manager.md)，來管理 IoT 中樞的連線。

> [!Note]
> * `device-to-cloud partitions`計數無法變更，因此您應該在設定資料分割計數時考慮長期的規模。
> * 每個取用者的取用者群組必須是唯一的。 建立 Azure 資料總管連接專屬的取用者群組。 在 Azure 入口網站中尋找您的資源，並移至 `Built-in endpoints` 以新增取用者群組。

## <a name="sending-events"></a>傳送事件

請參閱模擬裝置並產生資料的 [範例專案](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Quickstarts/simulated-device) 。

## <a name="next-steps"></a>後續步驟

有各種方法可將資料內嵌到 IoT 中樞。 請參閱下列連結，以取得每個方法的逐步解說。

* [將資料從 IoT 中樞內嵌至 Azure 資料總管](ingest-data-iot-hub.md)
* [使用 c # (Preview 建立適用于 Azure 資料總管的 IoT 中樞資料連線) ](data-connection-iot-hub-csharp.md)
* [使用 Python (Preview) 建立適用于 Azure 資料總管的 IoT 中樞資料連線 ](data-connection-iot-hub-python.md)
* [使用 Azure Resource Manager 範本建立 Azure 資料總管的 IoT 中樞資料連線](data-connection-iot-hub-resource-manager.md)
