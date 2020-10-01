---
title: Azure 資料總管資料內嵌總覽
description: 了解您可以在 Azure 資料總管中擷取 (載入) 資料的不同方式
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: e6630067cb02f3bf4685e274d2d5d2a6d00a97c5
ms.sourcegitcommit: 1618cbad18f92cf0cda85cb79a5cc1aa789a2db7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/01/2020
ms.locfileid: "91615031"
---
# <a name="azure-data-explorer-data-ingestion-overview"></a>Azure 資料總管資料內嵌總覽 

資料內嵌是用來從一或多個來源載入資料記錄，以將資料匯入 Azure 資料總管中資料表的程式。 一旦擷取之後，資料就會變成可供查詢。

下圖顯示在 Azure 資料總管中工作的端對端流程，並顯示不同的內嵌方法。

:::image type="content" source="media/data-ingestion-overview/data-management-and-ingestion-overview.png" alt-text="資料內嵌和管理的總覽配置":::

負責資料內嵌的 Azure 資料總管資料管理服務會執行下列程式：

Azure 資料總管會從外部來源提取資料，並從暫止的 Azure 佇列讀取要求。 資料會批次處理或串流至資料管理員。 流向相同資料庫和資料表的批次資料已針對內嵌輸送量進行優化。 Azure 資料總管會驗證初始資料，並視需要轉換資料格式。 進一步的資料操作包含比對架構、組織、編制索引、編碼和壓縮資料。 資料會根據設定的保留原則保存在儲存體中。 然後，資料管理員會將資料內嵌認可到引擎（可供查詢）。 

## <a name="supported-data-formats-properties-and-permissions"></a>支援的資料格式、屬性和許可權

* **[支援的資料格式](ingestion-supported-formats.md)** 

* 內嵌**[屬性](ingestion-properties.md)**：會影響資料內嵌方式的屬性 (例如，標記、對應、建立時間) 。

* **許可權**：若要內嵌資料，處理常式需要 [資料庫擷取器層級許可權](kusto/management/access-control/role-based-authorization.md)。 其他動作（例如 query）可能需要資料庫管理員、資料庫使用者或資料表管理員許可權。

## <a name="batching-vs-streaming-ingestion"></a>批次處理與串流內嵌

* 批次處理內嵌會進行資料批次處理，並針對高內嵌輸送量進行優化。 這個方法是最慣用且最具效能的內嵌類型。 資料會根據內嵌屬性進行批次處理。 然後會合並少量的資料，並針對快速查詢結果進行優化。 您可以在資料庫或資料表上設定內嵌 [批次處理](kusto/management/batchingpolicy.md) 原則。 根據預設，最大批次處理值為5分鐘、1000個專案，或總大小為 1 GB。

* [串流](ingest-data-streaming.md) 內嵌正在進行來自串流來源的資料內嵌。 串流內嵌可針對每個資料表的小型資料集，提供近乎即時的延遲時間。 資料一開始會內嵌至資料列存放區，然後移至資料行存放區的範圍。 您可以使用 Azure 資料總管用戶端程式庫或其中一個支援的資料管線來完成串流內嵌。 

## <a name="ingestion-methods-and-tools"></a>內嵌方法和工具

Azure 資料總管支援數個內嵌方法，每個方法都有自己的目標案例。 這些方法包括將工具、連接器和外掛程式加入至各式各樣的服務、managed 管線、使用 Sdk 以程式設計方式內嵌，以及直接存取內嵌。

### <a name="ingestion-using-managed-pipelines"></a>使用受控管線進行內嵌

如果組織想要擁有管理 (節流、重試、監視、警示，以及由外部服務完成的其他) ，使用連接器可能是最適當的解決方案。 已排入佇列的擷取適合大量資料。 Azure 資料總管支援下列 Azure Pipelines：

* **[事件方格](https://azure.microsoft.com/services/event-grid/)**：可接聽 azure 儲存體的管線，並會更新 azure 資料總管，以便在訂閱的事件發生時提取資訊。 如需詳細資訊，請參閱[將 Azure Blob 擷取至 Azure 資料總管](ingest-data-event-grid.md)。

* **[事件中樞](https://azure.microsoft.com/services/event-hubs/)**：將事件從服務傳輸至 Azure 資料總管的管線。 如需詳細資訊，請參閱[將資料從事件中樞內嵌至 Azure 資料總管](ingest-data-event-hub.md)。

* **[IoT 中樞](https://azure.microsoft.com/services/iot-hub/)**：用來將資料從支援的 IoT 裝置傳輸到 Azure 資料總管的管線。 如需詳細資訊，請參閱 [從 IoT 中樞](ingest-data-iot-hub.md)內嵌。

* **Azure Data Factory (ADF) **：適用于 Azure 中分析工作負載的完全受控資料整合服務。 Azure Data Factory 會連接到超過90的支援來源，以提供有效率且可復原的資料傳輸。 ADF 會準備、轉換和擴充資料，以提供可透過不同方式監視的見解。 這項服務可以用來做為一次性解決方案、定期時間軸，或由特定事件所觸發。 
  * [整合 Azure 資料總管與 Azure Data Factory](data-factory-integration.md)。
  * [使用 Azure Data Factory 將資料從支援的來源複製到 Azure 資料總管](/azure/data-explorer/data-factory-load-data)。
  * [使用 Azure Data Factory 範本，從資料庫大量複製到 Azure 資料總管](data-factory-template.md)。
  * [使用 Azure Data Factory 命令活動來執行 Azure 資料總管控制命令](data-factory-command-activity.md)。

### <a name="ingestion-using-connectors-and-plugins"></a>使用連接器和外掛程式的擷取

* **Logstash 外掛程式**，請參閱 [將資料從 Logstash 內嵌到 Azure 資料總管](ingest-data-logstash.md)。

* **Kafka 連接器**，請參閱將 [資料從 Kafka 內嵌到 Azure 資料總管](ingest-data-kafka.md)。

* **[Power Automate](https://flow.microsoft.com/)**： Azure 資料總管的自動化工作流程管線。 Power Automate 可以用來執行查詢，並使用查詢結果做為觸發程式來執行預設動作。 請參閱 [Azure 資料總管連接器，以 Power Automate (Preview) ](flow.md)。

* **Apache Spark 連接器**：可在任何 Spark 叢集上執行的開放原始碼專案。 它會執行資料來源和資料接收，以在 Azure 資料總管和 Spark 叢集之間移動資料。 您可以建立以資料驅動案例為目標的快速且可擴充的應用程式。 請參閱 [適用于 Apache Spark 的 Azure 資料總管連接器](spark-connector.md)。

### <a name="programmatic-ingestion-using-sdks"></a>使用 Sdk 以程式設計方式內嵌

Azure 資料總管會提供可用於查詢和資料擷取的 SDK。 程式設計擷取適合用於將擷取程序期間和之後的儲存體交易降至最低，藉此降低擷取成本 (COG)。

**可用的 Sdk 和開放原始碼專案**

* [Python SDK](kusto/api/python/kusto-python-client-library.md)

* [.NET SDK](kusto/api/netfx/about-the-sdk.md)

* [Java SDK](kusto/api/java/kusto-java-client-library.md)

* [Node SDK](kusto/api/node/kusto-node-client-library.md)

* [REST API](kusto/api/netfx/kusto-ingest-client-rest.md)

* [GO API](kusto/api/golang/kusto-golang-client-library.md)

### <a name="tools"></a>工具

* **[按一下](ingest-data-one-click.md)**[內嵌]：可讓您從各種來源類型建立及調整資料表，以快速內嵌資料。 按一下 [內嵌] 會根據 Azure 資料總管中的資料來源，自動建議資料表和對應結構。 按一下 [內嵌] 可用於單次內嵌，或透過事件方格定義資料內嵌所在容器上的連續內嵌。

* **[LightIngest](lightingest.md)**：適用于特定資料內嵌至 Azure 資料總管的命令列公用程式。 公用程式可以從本機資料夾或 Azure blob 儲存體容器提取來源資料。

### <a name="kusto-query-language-ingest-control-commands"></a>Kusto 查詢語言內嵌控制項命令

有幾種方法可透過 Kusto 查詢語言 (KQL) 命令，將資料直接內嵌到引擎中。 因為此方法會略過資料管理服務，所以它只適用于探索和原型設計。 請勿在生產或高容量的案例中使用這個方法。

  * **內嵌內嵌**： control 命令 [。內嵌內嵌](kusto/management/data-ingestion/ingest-inline.md) 會傳送到引擎，並將資料內嵌成為命令文字本身的一部分。 此方法適用于拼湊測試用途。

  * **從查詢**內嵌：控制項命令 [。 set、. append、. set-或-append 或。 set-或-replace](kusto/management/data-ingestion/ingest-from-query.md) 會傳送給引擎，並將資料間接指定為查詢或命令的結果。

  * **從儲存體內嵌 (提取) **：控制命令 [。內嵌至](kusto/management/data-ingestion/ingest-from-storage.md) 引擎會將資料儲存在某些外部儲存體 (例如，Azure Blob 儲存體) 由引擎存取，並由命令指向。

## <a name="comparing-ingestion-methods-and-tools"></a>比較內嵌方法和工具

| 內嵌名稱 | 資料類型 | 檔案大小上限 | 串流、批次處理、直接 | 最常見的案例 | 考量 |
| --- | --- | --- | --- | --- | --- |
| [**單次按一下內嵌**](ingest-data-one-click.md) | * sv，JSON | 1 GB 未壓縮 (請參閱附注) | 在直接內嵌中批次處理至容器、本機檔案和 blob | 一次性、建立資料表架構、使用事件方格進行連續內嵌的定義、使用容器進行大量內嵌， (最多10000個 blob)  | 10000 blob 是從容器隨機選取|
| [**LightIngest**](lightingest.md) | 支援的所有格式 | 1 GB 未壓縮 (請參閱附注)  | 透過 DM 進行批次處理或直接內嵌到引擎 |  資料移轉，已調整內嵌時間戳記的歷程記錄資料，大量內嵌 (沒有大小限制) | 區分大小寫，區分大小寫 |
| [**ADX Kafka**](ingest-data-kafka.md) | | | | |
| [**ADX 至 Apache Spark**](spark-connector.md) | | | | |
| [**LogStash**](ingest-data-logstash.md) | | | | |
| [**Azure Data Factory**](kusto/tools/azure-data-factory.md) | [支援的資料格式](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) | 無限制 * (每個 ADF 限制)  | 批次處理或每個 ADF 觸發程式 | 支援通常不受支援之大型檔案的格式，可從為永久到雲端複製到超過90的來源 | 內嵌時間 |
|[**Azure 資料流程**](kusto/tools/flow.md) | | | | 將命令內嵌為流程的一部分| 必須有高效能的回應時間 |
| [**IoT 中樞**](ingest-data-iot-hub-overview.md) | [支援的資料格式](ingest-data-iot-hub-overview.md#data-format)  | N/A | 批次處理，串流 | IoT 訊息、IoT 事件、IoT 屬性 | |
| [**事件中樞**](ingest-data-event-hub-overview.md) | [支援的資料格式](ingest-data-event-hub-overview.md#data-format) | N/A | 批次處理，串流 | 訊息、事件 | |
| [**事件方格**](ingest-data-event-grid-overview.md) | [支援的資料格式](ingest-data-event-grid-overview.md#data-format) | 1 GB 未壓縮 | 批次處理 | 從 Azure 儲存體持續內嵌，Azure 儲存體中的外部資料 | 100 KB 是最佳的檔案大小，用於 blob 重新命名和建立 blob |
| [**Net Std**](net-standard-ingest-data.md) | 支援的所有格式 | 1 GB 未壓縮 (請參閱附注)  | 批次處理、串流、直接 | 根據組織需求撰寫您自己的程式碼 |
| [**Python**](python-ingest-data.md) | 支援的所有格式 | 1 GB 未壓縮 (請參閱附注)  | 批次處理、串流、直接 | 根據組織需求撰寫您自己的程式碼 |
| [**Node.js**](node-ingest-data.md) | 支援的所有格式 | 1 GB 未壓縮 (請參閱附注 | 批次處理、串流、直接 | 根據組織需求撰寫您自己的程式碼 |
| [**Java**](kusto/api/java/kusto-java-client-library.md) | 支援的所有格式 | 1 GB 未壓縮 (請參閱附注)  | 批次處理、串流、直接 | 根據組織需求撰寫您自己的程式碼 |
| [**REST**](kusto/api/netfx/kusto-ingest-client-rest.md) | 支援的所有格式 | 1 GB 未壓縮 (請參閱附注)  | 批次處理、串流、直接| 根據組織需求撰寫您自己的程式碼 |
| [**Go**](kusto/api/golang/kusto-golang-client-library.md) | 支援的所有格式 | 1 GB 未壓縮 (請參閱附注)  | 批次處理、串流、直接 | 根據組織需求撰寫您自己的程式碼 |

> [!Note] 
> 在上表中參考時，內嵌支援的檔案大小上限為 5 GB。 建議內嵌 100 MB 和 1 GB 之間的檔案。

## <a name="ingestion-process"></a>內嵌進程

一旦您選擇了最適合您需求的內嵌方法，請執行下列步驟：

1. **設定保留原則**

    將資料內嵌到 Azure 資料總管中的資料表會受限於資料表的有效保留原則。 除非明確設定於資料表上，否則有效保留原則會衍生自資料庫的保留原則。 熱保留是叢集大小和保留原則的功能。 擷取超過您擁有可用空間的資料將會強制第一個資料的冷保留。
    
    請確定資料庫的保留原則符合您的需求。 如果不是，請在資料表層級明確覆寫。 如需詳細資訊，請參閱 [保留原則](kusto/management/retentionpolicy.md)。 
    
1. **建立資料表**

    若要內嵌資料，必須事先建立資料表。 使用下列其中一個選項：
   * [使用命令](kusto/management/create-table-command.md)建立資料表。 
   * 使用 [按一下](one-click-ingestion-new-table.md)內嵌來建立資料表。

    > [!Note]
    > 如果記錄不完整或無法將欄位剖析為所需的資料類型，則會將對應的資料表資料行填入 null 值。

1. **建立架構對應**

    [架構對應](kusto/management/mappings.md) 有助於將源資料欄位系結至目的地資料表資料行。 對應可讓您根據定義的屬性，將不同來源的資料放入相同的資料表中。 支援不同類型的對應，也就是資料列導向 (CSV、JSON 和 AVRO) ，以及資料行導向 (Parquet) 。 在大部分的方法中，也可以 [在資料表上預先建立](kusto/management/create-ingestion-mapping-command.md) 對應，並從內嵌命令參數參考。

1. **將更新原則設定** (選用) 

   某些資料格式對應 (Parquet、JSON 和 Avro) 支援簡單且有用的內嵌階段轉換。 在內嵌階段需要更複雜的處理案例時，請使用更新原則，以允許使用 Kusto 查詢語言命令進行輕量處理。 更新原則會自動對原始資料表上的內嵌資料執行提取和轉換，然後將產生的資料內嵌到一個或多個目的地資料表。 設定您的 [更新原則](kusto/management/update-policy.md)。



## <a name="next-steps"></a>後續步驟

* [支援的資料格式](ingestion-supported-formats.md)
* [支援的內嵌屬性](ingestion-properties.md)
