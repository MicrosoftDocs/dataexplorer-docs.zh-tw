---
title: Azure 資料總管資料擷取概觀
description: 了解您可以在 Azure 資料總管中擷取 (載入) 資料的不同方式
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/18/2020
ms.localizationpriority: high
ms.openlocfilehash: 147fe77b25229a4d2854fee5b7c15e05ec8a2f9f
ms.sourcegitcommit: 40f86b7f085152c21b6a1ee877f3ab324b59b88b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/04/2021
ms.locfileid: "101838332"
---
# <a name="azure-data-explorer-data-ingestion-overview"></a>Azure 資料總管資料擷取概觀 

資料擷取是一個程序，用於從一或多個來源載入資料記錄，以將資料匯入 Azure 資料總管中的資料表。 一旦擷取之後，資料就會變成可供查詢。

下圖顯示在 Azure 資料總管中工作的端對端流程，以及顯示不同的擷取方法。

:::image type="content" source="media/data-ingestion-overview/data-management-and-ingestion-overview.png" alt-text="資料擷取和管理的概觀配置":::

Azure 資料總管中負責資料擷取的資料管理服務可實作下列程序：

Azure 資料總管會從外部來源提取資料，並從擱置的 Azure 佇列中讀取要求。 資料會進行批次處理或串流處理到資料管理員。 流向相同資料庫和資料表的批次資料，已針對擷取輸送量最佳化。 Azure 資料總管會驗證初始資料，並在必要時轉換資料格式。 進一步資料操作包括比對結構描述、組織、編製索引、編碼以及壓縮資料。 資料會根據設定保留原則保存在儲存體中。 然後，資料管理員會將資料內嵌認可至引擎，以供查詢。 

## <a name="supported-data-formats-properties-and-permissions"></a>支援的資料格式、屬性和權限

* **[支援的資料格式](ingestion-supported-formats.md)** 

* **[擷取屬性](ingestion-properties.md)** ：會影響資料內嵌方式的屬性 (例如，標記、對應、建立時間)。

* **權限**：若要內嵌資料，程序需要 [資料庫擷取器層級權限](kusto/management/access-control/role-based-authorization.md)。 其他動作 (例如查詢) 可能需要資料庫管理員、資料庫使用者或資料表管理員權限。

## <a name="batching-vs-streaming-ingestion"></a>批次處理與串流擷取

* 批次處理擷取會進行資料批次處理，並已針對高擷取輸送量進行最佳化。 這個方法是擷取慣用的最高效能類型。 資料會根據擷取屬性進行批次處理。 小型批次的資料會接著合併，並針對快速查詢結果進行最佳化。 您可以在資料庫或資料表上設定[擷取批次處理](kusto/management/batchingpolicy.md)原則。 根據預設，最大批次處理值為 5 分鐘、1000 個項目，或總大小為 1 GB。

* [串流擷取](ingest-data-streaming.md)是從串流來源進行的資料擷取。 串流擷取可讓您以近乎即時的延遲來處理每個資料表的小型資料集。 資料一開始會內嵌到資料列存放區，然後移至資料行存放區範圍。 您可使用 Azure 資料總管用戶端程式庫或其中一個支援的資料管線來完成串流擷取。 

## <a name="ingestion-methods-and-tools"></a>擷取方法和工具

Azure 資料總管支援數種擷取方法，而每種方法都有自己的目標案例。 這些方法包括擷取工具、各種服務的連接器和外掛程式、受控管線、使用 SDK 的程式設計擷取，以及擷取的直接存取權。

### <a name="ingestion-using-managed-pipelines"></a>使用受控管線進行擷取

對於想要由外部服務進行管理 (節流、重試、監視、警示等) 的組織而言，使用連接器可能是最適當的解決方案。 已排入佇列的擷取適合大量資料。 Azure 資料總管支援下列 Azure Pipelines：

* **[事件方格](https://azure.microsoft.com/services/event-grid/)** ：接聽 Azure 儲存體的管線，而在訂閱的事件發生時，會更新 Azure 資料總管以提取資訊。 如需詳細資訊，請參閱[將 Azure Blob 擷取至 Azure 資料總管](ingest-data-event-grid.md)。

* **[事件中樞](https://azure.microsoft.com/services/event-hubs/)** ：可將事件從服務轉送至 Azure 資料總管的管線。 如需詳細資訊，請參閱[將資料從事件中樞內嵌至 Azure 資料總管](ingest-data-event-hub.md)。

* **[IoT 中樞](https://azure.microsoft.com/services/iot-hub/)** ：用於將資料從支援的 IoT 裝置轉送至 Azure 資料總管的管線。 如需詳細資訊，請參閱[從 IoT 中樞內嵌](ingest-data-iot-hub.md)。

* **Azure Data Factory (ADF)** ：適用於 Azure 中分析工作負載的完全受控資料整合服務。 Azure Data Factory 會與超過 90 個支援的來源連線，以提供有效率且可復原的資料轉送。 ADF 會準備、轉換及擴充資料，以提供可透過不同方式監視的見解。 此服務可作為一次性解決方案、用於定期的時間軸上，或由特定事件所觸發。 
  * [整合 Azure 資料總管與 Azure Data Factory](data-factory-integration.md)。
  * [使用 Azure Data Factory 將資料從支援的來源複製到 Azure 資料總管](./data-factory-load-data.md)。
  * [使用 Azure Data Factory 範本，從資料庫大量複製到 Azure 資料總管](data-factory-template.md)。
  * [使用 Azure Data Factory 命令活動來執行 Azure 資料總管控制命令](data-factory-command-activity.md)。

### <a name="ingestion-using-connectors-and-plugins"></a>使用連接器和外掛程式的擷取

* **Logstash 外掛程式**，請參閱 [將資料從 Logstash 內嵌至 Azure 資料總管](ingest-data-logstash.md)。

* **Kafka 連接器**，請參閱 [將資料從 Kafka 內嵌至 Azure 資料總管](ingest-data-kafka.md)。

* **[Power Automate](https://flow.microsoft.com/)** ：Azure 資料總管的自動化工作流程管線。 Power Automate 可用於執行查詢，並使用查詢結果作為觸發程序來執行預設動作。 請參閱[適用於 Power Automate 的 Azure 資料總管連接器 (預覽)](flow.md)。

* **Apache Spark 連接器**：可在任何 Spark 叢集上執行的開放原始碼專案。 其會實作資料來源和資料接收，以便在 Azure 資料總管和 Spark 叢集間移動資料。 您可以建立以資料導向案例為目標的快速且可擴充的應用程式。 請參閱[適用於 Apache Spark 的 Azure 資料總管連接器](spark-connector.md)。

### <a name="programmatic-ingestion-using-sdks"></a>使用 SDK 進行程式設計擷取

Azure 資料總管會提供可用於查詢和資料擷取的 SDK。 程式設計擷取適合用於將擷取程序期間和之後的儲存體交易降至最低，藉此降低擷取成本 (COG)。

**可用的 SDK 及開放原始碼專案**

* [Python SDK](kusto/api/python/kusto-python-client-library.md)

* [.NET SDK](kusto/api/netfx/about-the-sdk.md)

* [Java SDK](kusto/api/java/kusto-java-client-library.md)

* [Node SDK](kusto/api/node/kusto-node-client-library.md)

* [REST API](kusto/api/netfx/kusto-ingest-client-rest.md)

* [GO SDK](kusto/api/golang/kusto-golang-client-library.md)

### <a name="tools"></a>工具

* **[單鍵擷取](ingest-data-one-click.md)** ：可讓您從各種來源類型建立和調整資料表，以便快速內嵌資料。 單鍵擷取會根據 Azure 資料總管中的資料來源，自動建議資料表和對應結構。 單鍵擷取可用於一次性擷取，或在資料內嵌至的容器上透過事件方格定義連續擷取。

* **[LightIngest](lightingest.md)** ：用於將特殊資料擷取至 Azure 資料總管的命令列公用程式。 此公用程式可以從本機資料夾或從 Azure blob 儲存體容器提取來源資料。

### <a name="kusto-query-language-ingest-control-commands"></a>Kusto 查詢語言內嵌控制命令

有數種方法可經由 Kusto 查詢語言 (KQL) 命令，將資料直接內嵌至引擎。 因為此方法會略過資料管理服務，所以只適用於探索和原型設計。 請勿在生產或高容量的案例中使用此方法。

  * **內嵌擷取**：系統會將控制命令 ([.ingest inline](kusto/management/data-ingestion/ingest-inline.md)) 傳送至引擎，並將資料內嵌為命令文字本身的一部分。 此方法適用於即興測試用途。

  * **從查詢內嵌**：系統會將控制命令 ([.set、.append、.set-or-append 或 .set-or-replace](kusto/management/data-ingestion/ingest-from-query.md)) 傳送至引擎，並將資料間接指定為查詢或命令的結果。

  * **從儲存體內嵌 (提取)** ：系統會將控制命令 ([.ingest into](kusto/management/data-ingestion/ingest-from-storage.md)) 傳送至引擎，並且讓儲存在某些外部儲存體 (例如 Azure Blob 儲存體) 中的資料可由引擎存取，並由命令指向。

## <a name="comparing-ingestion-methods-and-tools"></a>比較擷取方法和工具

| 擷取名稱 | 資料類型 | 檔案大小上限 | 串流、批次處理、直接 | 最常見的案例 | 考量 |
| --- | --- | --- | --- | --- | --- |
| [**單鍵擷取**](ingest-data-one-click.md) | *sv、JSON | 1 GB 未壓縮 (請參閱注意)| 在直接擷取中批次處理至容器、本機檔案和 Blob | 一次性、建立資料表結構描述、使用事件方格進行連續擷取定義、使用容器進行大量擷取 (最多 10,000 個 Blob) | 從容器隨機選取 10,000 個 Blob|
| [**LightIngest**](lightingest.md) | 所有支援的格式 | 1 GB 未壓縮 (請參閱注意) | 透過 DM 或直接擷取批次處理至引擎 |  資料移轉、使用已調整擷取時間戳記的歷史資料、大量擷取 (無大小限制)| 區分大小寫、空格敏感性 |
| [**ADX Kafka**](ingest-data-kafka.md) | | | | |
| [**ADX 至 Apache Spark**](spark-connector.md) | | | | |
| [**LogStash**](ingest-data-logstash.md) | | | | |
| [**Azure Data Factory**](./data-factory-integration.md) | [支援的資料格式](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) | 無限制 *(依據 ADF 限制) | 批次處理或經由 ADF 觸發程序 | 支援通常不受支援的格式、大型檔案、可從超過 90 個來源複製 (從內部部署環境複製到雲端) | 擷取時間 |
|[ **Azure 資料流程**](./flow.md) | | | | 成為流程一部分的擷取命令| 必須具有高效能的回應時間 |
| [**IoT 中樞**](ingest-data-iot-hub-overview.md) | [支援的資料格式](ingest-data-iot-hub-overview.md#data-format)  | N/A | 批次處理、串流 | IoT 訊息、IoT 事件、IoT 屬性 | |
| [**事件中樞**](ingest-data-event-hub-overview.md) | [支援的資料格式](ingest-data-event-hub-overview.md#data-format) | N/A | 批次處理、串流 | 訊息、事件 | |
| [**事件方格**](ingest-data-event-grid-overview.md) | [支援的資料格式](ingest-data-event-grid-overview.md#data-format) | 1 GB 未壓縮 | 批次處理 | 從 Azure 儲存體、Azure 儲存體中的外部資料進行連續擷取 | 100 KB 是最佳檔案大小，用於 Blob 重新命名和 Blob 建立 |
| [**.NET SDK**](./net-sdk-ingest-data.md) | 所有支援的格式 | 1 GB 未壓縮 (請參閱注意) | 批次處理、串流、直接 | 根據組織需求撰寫自己的程式碼 |
| [**Python**](python-ingest-data.md) | 所有支援的格式 | 1 GB 未壓縮 (請參閱注意) | 批次處理、串流、直接 | 根據組織需求撰寫自己的程式碼 |
| [**Node.js**](node-ingest-data.md) | 所有支援的格式 | 1 GB 未壓縮 (請參閱注意) | 批次處理、串流、直接 | 根據組織需求撰寫自己的程式碼 |
| [**Java**](kusto/api/java/kusto-java-client-library.md) | 所有支援的格式 | 1 GB 未壓縮 (請參閱注意) | 批次處理、串流、直接 | 根據組織需求撰寫自己的程式碼 |
| [**REST**](kusto/api/netfx/kusto-ingest-client-rest.md) | 所有支援的格式 | 1 GB 未壓縮 (請參閱注意) | 批次處理、串流、直接| 根據組織需求撰寫自己的程式碼 |
| [**Go**](kusto/api/golang/kusto-golang-client-library.md) | 所有支援的格式 | 1 GB 未壓縮 (請參閱注意) | 批次處理、串流、直接 | 根據組織需求撰寫自己的程式碼 |

> [!Note] 
> 若在上表中參考，擷取支援的檔案大小上限為 4 GB。 建議內嵌 100 MB 和 1 GB 之間的檔案。

## <a name="ingestion-process"></a>擷取程序

一旦選擇了最符合您需求的擷取方法，請執行下列步驟：

1. **設定保留原則**

    在 Azure 資料總管中內嵌至資料表的資料會受制於資料表的有效保留原則。 除非明確設定於資料表上，否則有效保留原則會衍生自資料庫的保留原則。 熱保留是叢集大小和保留原則的函式。 內嵌的資料超過您的可用空間，將會強制第一筆資料冷保留。
    
    請確定資料庫的保留原則適合您的需求。 如果不是，請在資料表層級明確覆寫。 如需詳細資訊，請參閱[保留原則](kusto/management/retentionpolicy.md)。 
    
1. **建立資料表**

    為了內嵌資料，必須事先建立資料表。 使用下列其中一個選項：
   * [使用命令](kusto/management/create-table-command.md)建立資料表。 
   * 使用[單鍵擷取](one-click-ingestion-new-table.md)建立資料表。

    > [!Note]
    > 如果記錄不完整或無法將欄位剖析為所需的資料類型，則會將對應的資料表資料行填入 null 值。

1. **建立結構描述對應**

    [結構描述對應](kusto/management/mappings.md)可協助將來源資料欄位繫結至目的地資料表資料行。 對應可讓您根據定義的屬性，將不同來源的資料放入相同的資料表。 支援不同類型的對應，包括資料列導向 (CSV、JSON 和 AVRO) 和資料行導向 (Parquet)。 在大多數的方法中，對應也可以[在資料表上預先建立](kusto/management/create-ingestion-mapping-command.md)並從內嵌命令參數進行參考。

1. **設定更新原則** (選擇性)

   某些資料格式對應 (Parquet、JSON 和 Avro) 支援簡單且實用的內嵌時間轉換。 如果在內嵌時需要更複雜的處理，請使用更新原則，這可讓您使用 Kusto 查詢語言命令進行輕量型處理。 更新原則會自動對原始資料表上內嵌的資料執行擷取和轉換，並將結果產生的資料內嵌到一或多個目的地資料表。 將您的[更新原則](kusto/management/update-policy.md)。



## <a name="next-steps"></a>後續步驟

* [支援的資料格式](ingestion-supported-formats.md)
* [支援的擷取屬性](ingestion-properties.md)
