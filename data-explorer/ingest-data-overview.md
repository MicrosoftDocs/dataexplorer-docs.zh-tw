---
title: Azure 資料總管資料擷取
description: 了解您可以在 Azure 資料總管中擷取 (載入) 資料的不同方式
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/18/2019
ms.openlocfilehash: 8f48499e1b9bb1c67f43ae575a68bb38e314f08e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81497885"
---
# <a name="azure-data-explorer-data-ingestion"></a>Azure 資料總管資料擷取

資料擷取是從一個或多個來源載入資料記錄所使用的程序，以建立或更新 Azure 資料總管中的資料表。 一旦擷取之後，資料就會變成可供查詢。 下圖顯示在 Azure 資料總管中工作的端對端流程，包括資料擷取。

![設計師中](media/ingest-data-overview/data-flow.png)

Azure 資料總管資料管理服務負責資料擷取，並提供下列功能：

1. **資料提取**：從外部來源 (事件中樞) 提取資料，或從 Azure 佇列讀取擷取要求。

1. **批次處理**：批次處理流向相同資料庫和資料表的資料，將擷取輸送量最佳化。

1. **驗證**:必要時進行初步驗證和格式轉換。

1. **資料操作**：比對結構描述、組織、編製索引、編碼以及壓縮資料。

1. **擷取流程中的持續點**：管理引擎上的擷取負載，並在暫時性失敗時處理重試。

1. **認可資料擷取**：讓資料可供查詢。

## <a name="ingestion-methods"></a>擷取方法

Azure 資料總管支援數種擷取方法，每種方法都有自己的目標案例、優點和缺點。 Azure 資料總管會針對常用的服務，使用 SDK 的程式設計擷取，以及用於探索之引擎的直接存取，提供管線和連接器。

### <a name="ingestion-using-pipelines-connectors-and-plugins"></a>使用管線、連接器和外掛程式的擷取

Azure 資料總管目前支援：

* 事件格線管線，可以使用 Azure 入口網站中的管理精靈來管理。 如需詳細資訊，請參閱[將 Azure Blob 擷取至 Azure 資料總管](ingest-data-event-grid.md)。

* 事件中樞管線，可以使用 Azure 入口網站中的管理精靈來管理。 如需詳細資訊，請參閱[將資料從事件中樞內嵌至 Azure 資料總管](ingest-data-event-hub.md)。

* Logstash 外掛程式，請參閱[將資料從 Logstash 擷取至 Azure 資料總管](ingest-data-logstash.md)。

* Kafka 連接器，請參閱[將資料從 Kafka 內嵌至 Azure 資料總管](ingest-data-kafka.md)。

### <a name="ingestion-using-integration-services"></a>使用整合服務進行擷取

* Azure 資料工廠 (ADF) 是 Azure 中分析工作負荷的完全託管數據整合服務,用於使用[支援的資料儲存和格式](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)將資料複製到 Azure 資料資源管理員和從 Azure 資料資源管理員中複製。 有關詳細資訊,請參閱[將資料從 Azure 資料工廠複製到 Azure 資料資源管理員](/azure/data-explorer/data-factory-load-data)。

### <a name="programmatic-ingestion"></a>程式設計擷取

Azure 資料總管會提供可用於查詢和資料擷取的 SDK。 程式設計擷取適合用於將擷取程序期間和之後的儲存體交易降至最低，藉此降低擷取成本 (COG)。

**可用的 SDK 及開放原始碼專案**：

Kusto 提供的用戶端 SDK 可用來搭配下列項目內嵌及查詢資料：

* [Python SDK](kusto/api/python/kusto-python-client-library.md)

* [.NET SDK](kusto/api/netfx/about-the-sdk.md)

* [Java SDK](kusto/api/java/kusto-java-client-library.md)

* [Node SDK](kusto/api/node/kusto-node-client-library.md)

* [REST API](kusto/api/netfx/kusto-ingest-client-rest.md)

**程式設計擷取技術**：

* 透過 Azure 資料總管資料管理服務 (高輸送量且可靠的擷取) 擷取資料：

    [**批次擷取**](kusto/api/netfx/kusto-ingest-queued-ingest-sample.md) (由 SDK 提供)：用戶端將資料上傳至 Azure Blob 儲存體 (Azure 資料總管資料管理服務所指定)，並將通知張貼到 Azure 佇列。 批次擷取是針對大量、可靠且不費力的資料擷取所建議的技術。

* 將資料直接擷取到 Azure 資料總管引擎 (最適合探索和原型設計)：

  * **內聯引入**:包含帶內數據的控制命令(.inges 內聯)用於臨時測試目的。

  * **從查詢擷取**：指向查詢結果的控制命令 (.set、.set-or-append、.set-or-replace)，用來產生報表或小型暫存資料表。

  * **從儲存體內嵌**：資料儲存在外部 (例如，Azure Blob 儲存體) 的控制命令 (.ingest into) 允許高效大量擷取資料。

**不同方法的延遲**：

| 方法 | Latency |
| --- | --- |
| **內嵌式內嵌** | 立即 |
| **從查詢內嵌** | 查詢時間 + 處理時間 |
| **從儲存體內嵌** | 下載時間 + 處理時間 |
| **已排入佇列的內嵌** | 批次處理時間 + 處理時間 |
| |

處理時間取決於資料大小 (少於幾秒鐘)。 批次處理時間預設為 5 分鐘。

## <a name="choosing-the-most-appropriate-ingestion-method"></a>選擇最適合的擷取方法

開始擷取資料之前，您應該自問下列問題。

* 我的資料在哪裡？ 
* 什麼是資料格式？可以變更嗎？ 
* 要查詢的必填欄位有哪些？ 
* 預期的資料量和速率為何？ 
* 預期有多少事件類型 (稱為資料表數目)？ 
* 預期變更事件結構描述頻率為何？ 
* 有多少個節點將會產生資料？ 
* 來源作業系統是什麼？ 
* 延遲需求為何？ 
* 可以使用其中一個現有的受控擷取管線嗎？ 

對於具有基於事件中心和IoT中心等消息服務的現有基礎結構的組織,使用連接器可能是最合適的解決方案。 已排入佇列的擷取適合大量資料。

## <a name="supported-data-formats"></a>支援的資料格式

對於從查詢內嵌以外的所有擷取方法，請設定資料格式，Azure 資料總管才能剖析它。 
* 支援的數據格式包括:TXT、CSV、TSV、TSVE、PSV、SCSV、SOH、JSON(線分離、多線)、Avro、Orc 和 Parquet。 
* 支援 ZIP 和 GZIP 壓縮。

> [!NOTE]
> 正在擷取資料時，會根據目標資料表資料行推斷資料類型。 如果記錄不完整或無法將欄位剖析為所需的資料類型，則會將對應的資料表資料行填入 null 值。

## <a name="ingestion-recommendations-and-limitations"></a>擷取的建議和限制

* 已擷取資料的有效保留原則衍生自資料庫的保留原則。 如需詳細資料，請參閱[保留原則](kusto/management/retentionpolicy.md)。 擷取資料需要有**資料表擷取器**或**資料庫擷取器**權限。
* 擷取最多可支援 5GB 的檔案大小。 建議內嵌 100 MB 和 1 GB 之間的檔案。

## <a name="schema-mapping"></a>結構描述對應

結構描述對應可協助將來源資料欄位繫結至目的地資料表資料行。

* [CSV 對應](kusto/management/mappings.md#csv-mapping) (選擇性) 適用於所有序數型格式。 可以使用內嵌命令參數來執行，或是[在資料表上預先建立](kusto/management/create-ingestion-mapping-command.md)，並從擷取命令參數參考。
* 可以使用內嵌命令參數執行 [JSON 對應](kusto/management/mappings.md#json-mapping) (必要) 和 [Avro 對應](kusto/management/mappings.md#avro-mapping) (必要)。 它們也可以[在資料表上預先建立](kusto/management/create-ingestion-mapping-command.md)並從內嵌命令參數參考。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [將資料從事件中樞內嵌至 Azure 資料總管](ingest-data-event-hub.md)

> [!div class="nextstepaction"]
> [使用事件格線訂用帳戶將資料內嵌至 Azure 資料總管](ingest-data-event-grid.md)

> [!div class="nextstepaction"]
> [將資料從 Kafka 擷取至 Azure 資料總管](ingest-data-kafka.md)

> [!div class="nextstepaction"]
> [使用 Azure 資料資源管理員 Python 函式庫引入資料](python-ingest-data.md)

> [!div class="nextstepaction"]
> [使用 Azure 資料資源管理員節點庫引入資料](node-ingest-data.md)

> [!div class="nextstepaction"]
> [使用 Azure 資料總管 .NET Standard SDK 內嵌資料 (預覽)](net-standard-ingest-data.md)

> [!div class="nextstepaction"]
> [將資料從 Logstash 擷取至 Azure 資料總管](ingest-data-logstash.md)
