---
title: Azure 資料資源管理員與 Azure 資料工廠整合
description: 在本主題中,將 Azure 資料資源管理員與 Azure 資料工廠整合,以使用複製、搜尋和指令活動
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tomersh26
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: 5b8e9844894df9c49c1abd703ebc5a14b4c7050c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81497963"
---
# <a name="integrate-azure-data-explorer-with-azure-data-factory"></a>將 Azure 資料資源管理員與 Azure 資料工廠整合

[Azure 資料工廠](/azure/data-factory/)(ADF) 是一種基於雲端資料整合服務,允許您整合不同的資料儲存並執行對資料的活動。 ADF 允許您創建資料驅動的工作流,以便協調和自動化數據移動和數據轉換。 Azure 資料資源管理器是 Azure 數據工廠[中受支援的數據存儲](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)之一。 

## <a name="azure-data-factory-activities-for-azure-data-explorer"></a>Azure 資料工廠活動,用於 Azure 資料資源管理員

Azure 資料資源管理員使用者可以使用與 Azure 資料工廠的各種整合:

### <a name="copy-activity"></a>複製活動
 
Azure 資料工廠複製活動用於在數據存儲之間傳輸數據。 Azure 資料資源管理員支援為源,其中數據從 Azure 資料資源管理員複製到任何受支援的數據存儲,以及從任何受支援的數據儲存複製到 Azure 資料資源管理員的接收器。 有關詳細資訊,請參閱使用[Azure 資料工廠將資料複製到 Azure 資料資源管理器或從 Azure 資料資源管理器](/azure/data-factory/connector-azure-data-explorer)中複製數據。 有關詳細演練,請參閱[將 Azure 資料工廠載入資料到 Azure 資料資源管理員](data-factory-load-data.md)。
Azure 資料資源管理員受 Azure IR(整合式執行時)的支援,在 Azure 複製資料時使用,在自託管 IR 中使用,用於將資料從/複製到本地的資料儲存或具有存取控制的網路中(如 Azure 虛擬網路)。 有關詳細資訊,請參閱要使用的[IR](/azure/data-factory/concepts-integration-runtime#determining-which-ir-to-use)
 
> [!TIP]
> 使用複製活動並建立**連結服務**或**資料集**時,請選擇資料儲存**Azure 資料資源管理員 (Kusto),** 而不是舊資料儲存**Kusto**。  

### <a name="lookup-activity"></a>尋找活動
 
查找活動用於在 Azure 資料資源管理員上執行查詢。 查詢的結果將作為查找活動的輸出返回,並且可用於管道中的下一個活動,如[ADF 查找文檔中](/azure/data-factory/control-flow-lookup-activity#use-the-lookup-activity-result-in-a-subsequent-activity)所述。  
除了 5,000 行和 2 MB 的回應大小限制外,該活動還有 1 小時的查詢超時限制。

### <a name="command-activity"></a>命令活動

命令活動允許執行 Azure 資料資源管理員[控制項 。](kusto/concepts/index.md#control-commands) 與查詢不同,控件命令可能會修改數據或元數據。 某些控制命令旨在將資料引入 Azure 資料資源管理員,`.ingest``.set-or-append`使用或 ) 等命令或`.export`使用 命令(如 ) 將資料複製到外部資料儲存。
有關命令活動的詳細演練,請參閱[使用 Azure 資料工廠指令活動執行 Azure 資料資源管理員控制項指令](data-factory-command-activity.md)。  有時,使用控制命令複製數據可以比複製活動更快、更便宜。 要確定何時使用指令活動與複製活動,請參閱[複製資料時在「複製」 與「命令」 的動作之間進行選擇](#select-between-copy-and-azure-data-explorer-command-activities-when-copy-data)。

### <a name="copy-in-bulk-from-a-database-template"></a>從資料庫樣本批次複製

[通過使用 Azure 資料工廠樣本批量從資料庫複製到 Azure 資料資源管理器](data-factory-template.md)是預定義的 Azure 數據工廠管道。 該範本用於為每個資料庫或每個表創建多個管道,以加快資料複製。 

### <a name="mapping-data-flows"></a>對應資料流程 

[Azure 資料工廠映射資料流](/azure/data-factory/concepts-data-flow-overview)是可視化設計的數據轉換,允許數據工程師無需編寫代碼即可開發圖形數據轉換邏輯。 要建立資料串流並將資料引入 Azure 資料資源管理員,請使用以下方法:

1. 建立[映射資料串流](/azure/data-factory/data-flow-create)。
1. [匯出資料到 Azure Blob](/azure/data-factory/data-flow-sink)。 
1. 定義[事件網格](/azure/data-explorer/ingest-data-event-grid)或[ADF 複製活動](/azure/data-explorer/data-factory-load-data)以將資料引入 Azure 資料資源管理器。

## <a name="select-between-copy-and-azure-data-explorer-command-activities-when-copy-data"></a>複製資料時在「複製」和「Azure 資料資源管理員」命令活動之間進行選擇 

本節將説明您選擇適合數據複製需求的正確活動。

將資料從 Azure 資料資源管理員複製到 Azure 資料資源管理員時,Azure 資料工廠中有兩個可用選項:
* 複製活動。
* Azure 資料資源管理器命令活動,它執行在 Azure 資料資源管理器中傳輸數據的控制項命令之一。 

### <a name="copy-data-from-azure-data-explorer"></a>從 Azure 資料資源管理員複製資料
  
可以使用複製活動或[`.export`](kusto/management/data-export/index.md)命令從 Azure 資料資源管理員複製數據。 該`.export`命令執行查詢,然後匯出查詢的結果。 

有關複製活動和`.export`命令的比較,請參閱下表,以便從 Azure 資料資源管理器複製數據。

| | 複製活動 | .匯出命令 |
|---|---|---|
| **流描述** | ADF 在 Kusto 上執行查詢,處理結果,並將其發送到目標數據儲存。 <br>(**ADX > ADF >接收器数据存储**) | ADF`.export`向 Azure 資料資源管理員發送控制命令,該資源管理員執行該命令,並將數據直接發送到目標數據存儲。 <br>**(ADX >接收器数据存储**) |
| **支援的目標資料儲存** | 各種[支援的資料儲存](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) | ADLSv2,Azure Blob,SQL 資料庫 |
| **效能** | 集中 | <ul><li>分散式(預設),同時從多個節點匯出資料</li><li>更快,COGS 效率高。</li></ul> |
| **伺服器限制** | 可以延伸/關閉[查詢限制](kusto/concepts/querylimits.md)。 預設情況下,ADF 查詢包含: <ul><li>大小限制為 500,000 條記錄或 64 MB。</li><li>時間限制為 10 分鐘。</li><li>`noTruncation`設置為 false。</li></ul> | 預設情況下,擴展或禁用查詢限制: <ul><li>大小限制已禁用。</li><li>伺服器超時延長到 1 小時。</li><li>`MaxMemoryConsumptionPerIterator`並`MaxMemoryConsumptionPerQueryPerNode`擴展到最大值(5 GB,總物理記憶體/2)。</li></ul>

> [!TIP]
> 如果複製目標是`.export`命令支持的數據存儲之一,並且"複製活動"功能對您的需求都至關重要,請選擇該`.export`命令。

### <a name="copying-data-to-azure-data-explorer"></a>複製資料到 Azure 資料資源管理員

可以使用複製活動或引入命令(如`.set``.replace)``.ingest``.set-or-append``.set-or-replace`[從查詢(、、、](kusto/management/data-ingestion/ingest-from-query.md)和[從存儲()引入](kusto/management/data-ingestion/ingest-from-storage.md)來將數據複製到 Azure 資料資源管理器。 

有關複製活動的比較以及將資料複製到 Azure 資料資源管理器的引入命令的比較,請參閱下表。

| | 複製活動 | 從查詢內嵌<br> `.set-or-append` / `.set-or-replace` / `.set` / `.replace` | 從儲存體內嵌 <br> `.ingest` |
|---|---|---|---|
| **流描述** | ADF 從來源資料儲存取得資料,將其轉換為表格格式,並執行所需的架構映射更改。 然後,ADF 將數據上載到 Azure Blob,將其拆分為塊,然後將 blob 下載到 ADX 表中。 <br> (**來源資料儲存> ADF > AX > Azure blob**) | 這些命令可以執行查詢或`.show`命令,並將查詢的結果引入到表中 **(ADX > ADX**)。 | 此命令通過從一個或多個雲端儲存專案「提取」數據來將數據引入表中。 |
| **支援的來源資料儲存** |  [多種選項](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) | ADLS 第 2 代、Azure Blob、SQL(使用sql_request外掛程式)、Cosmos(使用cosmosdb_sql_request外掛程式)和任何其他提供 HTTP 或 Python API 的數據儲存。 | 檔案系統、Azure Blob 儲存、ADLS 第 1 代、ADLS 第 2 代 |
| **效能** | 引入是排隊和管理的,通過提供負載平衡、重試和錯誤處理,確保小尺寸引入並確保高可用性。 | <ul><li>這些命令不是為輸入大容量數據而設計的。</li><li>工作如預期,更便宜。 但對於生產方案以及流量速率和數據大小較大時,請使用 Copy 活動。</li></ul> |
| **伺服器限制** | <ul><li>無大小限制。</li><li>最大超時限制:每個引入的 blob 1 小時。 |<ul><li>查詢元件上只有大小限制,可以通過指定 來跳過`noTruncation=true`該限制 。</li><li>最大超時限制:1 小時。</li></ul> | <ul><li>無大小限制。</li><li>最大超時限制:1 小時。</li></ul>|

> [!TIP]
> * 將資料從 ADF 複製到 Azure 資料`ingest from query`資源管理員時,請使用這些命令。  
> * 對於大型數據集(>1GB),請使用複製活動。  

## <a name="required-permissions"></a>所需的權限

下表列出了與 Azure 數據工廠集成中各個步驟所需的許可權。

| 步驟 | 作業 | 最低權限等級 | 注意 |
|---|---|---|---|
| **建立連結服務** | 資料庫導航 | *資料庫檢視器* <br>應授權使用 ADF 登錄的使用者讀取資料庫元數據。 | 用戶可以手動提供資料庫名稱。 |
| | [測試連接] | *資料庫監控器*或*表引入器* <br>應授權服務主體執行資料庫級`.show`命令或表級引入。 | <ul><li>TestConnect 驗證與群集的連接,而不是與資料庫的連接。 即使資料庫不存在,它也能成功。</li><li>表管理員許可權不足。</li></ul>|
| **建立資料集** | 表導航 | *資料庫監控器* <br>必須授權使用 ADF 登錄的使用者執行`.show`資料庫級 命令。 | 用戶可以手動提供表名稱。|
| **建立資料集**或**複製活動** | 預覽資料 | *資料庫檢視器* <br>必須授權服務主體讀取資料庫元數據。 | | 
|   | 匯入架構 | *資料庫檢視器* <br>必須授權服務主體讀取資料庫元數據。 | 當 ADX 是表格到表格副本的來源時,ADF 將自動導入架構,即使使用者未顯式導入架構也是如此。 |
| **ADX 為接收器** | 建立此名稱欄射 | *資料庫監控器* <br>必須授權服務主體執行資料庫級`.show`命令。 | <ul><li>所有必要操作都使用*表格機 。*</li><li> 某些可選操作可能會失敗。</li></ul> |
|   | <ul><li>建立 CSV 映射</li><li>刪除對應</li></ul>| *表引入器*或*資料庫管理員* <br>必須授權服務主體對表進行更改。 | |
|   | 擷取資料 | *表引入器*或*資料庫管理員* <br>必須授權服務主體對表進行更改。 | | 
| **ADX 為來源** | 執行查詢 | *資料庫檢視器* <br>必須授權服務主體讀取資料庫元數據。 | |
| **庫托命令** | | 根據每個命令的許可權級別。 |

## <a name="performance"></a>效能 

如果 Azure 資料資源管理員是來源,並且使用包含查詢的尋找、複製或指令活動,請參閱[查詢](kusto/query/best-practices.md)效能資訊和[ADF 文件以瞭解複製活動](/azure/data-factory/copy-activity-performance)。
  
本節介紹 Azure 數據資源管理器是接收器的複製活動的使用。 Azure 資料資源管理器接收器的估計輸送量為11-13 MBps。 下表詳細介紹了影響 Azure 數據資源管理器接收器性能的參數。

| 參數 | 注意 |
|---|---|
| **元件地理接近** | 將所有元件放在同一區域中:<ul><li>源和接收器數據存儲。</li><li>ADF 整合式執行時。</li><li>您的 ADX 群集。</li></ul>確保至少整合式執行時與 ADX 群集位於同一區域。 |
| **DIA 數** | ADF 每 4 個 DIA 1 個 VM。 <br>僅當源是具有多個檔的基於檔的儲存時,增加 DIA 才會有所説明。 然後,每個 VM 將並行處理不同的檔。 因此,複製單個大型檔的延遲將比複製多個較小的檔更高。|
|**ADX 叢集的數量和 SKU** | 大量 ADX 節點將增強引入處理時間。|
| **並行** |    要從資料庫複製大量資料,請對資料進行分割區,然後使用 ForEach 循環並行複製每個分區,或使用[從資料庫到 Azure 資料資源管理員樣本的批次複製](data-factory-template.md)。 注意:**Settings** > 複製活動中**的並行性設置程度**與 ADX 無關。 |
| **資料處理複雜性** | 延遲因源檔格式、列映射和壓縮而異。|
| **執行整合的 VM** | <ul><li>對於 Azure 副本,無法更改 ADF VM 和電腦 SKU。</li><li> 對於 Azure 複製的「打開」,確定託管自託管 IR 的 VM 足夠強大。</li></ul>|

## <a name="tips-and-common-pitfalls"></a>提示和常見陷阱

### <a name="monitor-activity-progress"></a>監控活動進度

* 監視活動進度時,*資料寫入*屬性可能比*資料讀取*屬性大得多,因為*資料讀取*是根據二進位檔案大小計算的,而*寫入的資料*是在資料解序列化並解壓縮後根據記憶體中大小計算的。

* 監視活動進度時,可以看到數據已寫入 Azure 資料資源管理器接收器。 查詢 Azure 數據資源管理器表時,可以看到數據尚未到達。 這是因為複製到 Azure 資料資源管理器時有兩個階段。 
    * 第一階段讀取源數據,將其拆分為 900 MB 塊,並將每個區塊上載到 Azure Blob。 ADF 活動進度視圖可以看到第一階段。 
    * 第二階段在所有數據上載到 Azure Blob 後開始。 Azure 資料資源管理器引擎節點下載 blob 並將數據引入接收器表。 然後,數據在 Azure 資料資源管理器表中查看。

### <a name="failure-to-ingest-csv-files-due-to-improper-escaping"></a>由於無法引入 CSV 檔,導致無法進行洩漏

Azure 資料資源管理員希望 CSV 檔與[RFC 4180](https://www.ietf.org/rfc/rfc4180.txt)對齊。
它期望:
* 包含需要轉義的字元(如"和新的行)的欄位應以**字元開**頭和結尾,而不帶空格。 所有**欄**位中*的所有*字元都使用雙**字元****("")** 轉義。 例如,"_你好,""世界"是_一個有效的CSV檔,其中一個記錄具有一個單一的列或字段,內容為_Hello,"世界"。_
* 檔案中的所有記錄必須具有相同的欄位數。

Azure 資料工廠允許反斜杠(轉義)字元。 如果使用 Azure 資料工廠產生具有反斜杠字元的 CSV 檔,則將檔案引入 Azure 資料資源管理器將失敗。

#### <a name="example"></a>範例

以下文本值:你好,"世界"<br/>
ABC DEF<br/>
"ABC_D"EF<br/>
"ABC DEF<br/>

應出現在適當的 CSV 檔中,如下所示:"你好,"世界"<br/>
"ABC DEF"<br/>
""ABC DEF"<br/>
""ABC\D""EF"<br/>

通過使用預設轉義字元(反斜杠),以下 CSV 將無法使用 Azure 數據資源管理\"器:"\"你好, 世界"<br/>
"ABC DEF"<br/>
"ABC\"DEF"<br/>
\""ABC+D\"EF"<br/>

### <a name="nested-json-objects"></a>嵌套 JSON 物件

將 JSON 檔案複製到 Azure 資料資源管理員時,請注意:
* 不支援陣列。
* 如果 JSON 結構包含物件數據類型,Azure 數據工廠將拼平物件的子項,並嘗試將每個子項映射到 Azure 數據資源管理器表中的不同列。 如果希望將整個物件項目映射到 Azure 資料資源管理員中的單欄:
    * 將整個 JSON 行引入 Azure 數據資源管理器中的單個動態列。
    * 使用 Azure 數據工廠的 JSON 編輯器手動編輯管道定義。 在**對應對應**
       * 刪除為每個子項創建的多個映射,並添加將物件類型映射到表列的單個映射。
       * 在關閉的方括弧後,添加逗號,後跟:<br/>
       `"mapComplexValuesToString": true`.

### <a name="specify-additionalproperties-when-copying-to-azure-data-explorer"></a>複製到 Azure 資料資源管理員時指定其他屬性

> [!NOTE]
> 此功能當前可通過手動編輯 JSON 有效負載提供。 

在複製活動的「接收器」部分下添加一行,如下所示:

```json
"sink": {
    "type": "AzureDataExplorerSink",
    "additionalProperties": "{\"tags\":\"[\\\"drop-by:account_FiscalYearID_2020\\\"]\"}"
},
```

值的溢出可能比較棘手。 使用以下代碼段作為參考:

```csharp
static void Main(string[] args)
{
       Dictionary<string, string> additionalProperties = new Dictionary<string, string>();
       additionalProperties.Add("ignoreFirstRecord", "false");
       additionalProperties.Add("csvMappingReference", "Table1_mapping_1");
       IEnumerable<string> ingestIfNotExists = new List<string> { "Part0001" };
       additionalProperties.Add("ingestIfNotExists", JsonConvert.SerializeObject(ingestIfNotExists));
       IEnumerable<string> tags = new List<string> { "ingest-by:Part0001", "ingest-by:IngestedByTest" };
       additionalProperties.Add("tags", JsonConvert.SerializeObject(tags));
       var additionalPropertiesForPayload = JsonConvert.SerializeObject(additionalProperties);
       Console.WriteLine(additionalPropertiesForPayload);
       Console.ReadLine();
}
```

列印值:

```json
{"ignoreFirstRecord":"false","csvMappingReference":"Table1_mapping_1","ingestIfNotExists":"[\"Part0001\"]","tags":"[\"ingest-by:Part0001\",\"ingest-by:IngestedByTest\"]"}
```

## <a name="next-steps"></a>後續步驟

* 讓您如何使用[Azure 資料工廠將資料複製到 Azure 資料資源管理員](data-factory-load-data.md)。
* 瞭解如何使用[Azure 資料工廠樣本將批次複製到 Azure 資料資源管理員](data-factory-template.md)。
* 瞭解如何使用[Azure 資料工廠指令活動來執行 Azure 資料資源管理員控制項指令](data-factory-command-activity.md)。
* 瞭解[資料查詢的 Azure 資料資源管理員查詢](/azure/data-explorer/web-query-data)。



