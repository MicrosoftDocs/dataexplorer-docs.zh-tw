---
title: Azure 資料總管與 Azure Data Factory 整合
description: 在本主題中，將 Azure 資料總管與 Azure Data Factory 整合，以使用複製、查閱和命令活動
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tomersh26
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: b3ff40f04ba9152fa1b12b7211bf7a7cf07c69bb
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373943"
---
# <a name="integrate-azure-data-explorer-with-azure-data-factory"></a>將 Azure 資料總管與 Azure Data Factory 整合

[Azure Data Factory](/azure/data-factory/) （ADF）是以雲端為基礎的資料整合服務，可讓您整合不同的資料存放區，並對資料執行活動。 ADF 可讓您建立資料導向的工作流程，以協調和自動化資料移動和資料轉換。 Azure 資料總管是 Azure Data Factory 中支援的其中一個[資料存放區](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)。 

## <a name="azure-data-factory-activities-for-azure-data-explorer"></a>適用于 Azure 資料總管的 Azure Data Factory 活動

Azure 資料總管用戶可以使用各種與 Azure Data Factory 的整合：

### <a name="copy-activity"></a>複製活動
 
Azure Data Factory 複製活動是用來在資料存放區之間傳送資料。 Azure 資料總管支援作為來源，其中資料會從 Azure 資料總管複製到任何支援的資料存放區，以及從任何支援的資料存放區將資料複製到 Azure 資料總管的接收。 如需詳細資訊，請參閱[使用 Azure Data Factory 在 Azure 資料總管複製資料](/azure/data-factory/connector-azure-data-explorer)。 如需詳細的逐步解說，請參閱[從 Azure Data Factory 將資料載入 Azure 資料總管](data-factory-load-data.md)。
Azure 資料總管受到 Azure IR （Integration Runtime）的支援，當資料在 Azure 內複製，以及從內部部署或具有存取控制的網路（例如 Azure 虛擬網路）複製資料時使用的自我裝載 IR。 如需詳細資訊，請參閱[要使用的 IR](/azure/data-factory/concepts-integration-runtime#determining-which-ir-to-use)
 
> [!TIP]
> 使用複製活動並建立**連結服務**或**資料集**時，請選取資料存放區**Azure 資料總管（Kusto）** ，而不是舊的資料存放區**Kusto**。  

### <a name="lookup-activity"></a>查閱活動
 
查閱活動可用來在 Azure 資料總管上執行查詢。 查詢的結果會當做查閱活動的輸出傳回，並可用於管線中的下一個活動，如[ADF 查閱檔](/azure/data-factory/control-flow-lookup-activity#use-the-lookup-activity-result-in-a-subsequent-activity)中所述。  
除了5000資料列的回應大小限制和 2 MB，活動也具有1小時的查詢超時限制。

### <a name="command-activity"></a>命令活動

命令活動允許執行 Azure 資料總管[控制命令](kusto/concepts/index.md#control-commands)。 與查詢不同的是，control 命令可能會修改資料或中繼資料。 某些控制命令的目標是要使用或之類的命令，將資料內嵌至 Azure 資料總管，或使用之類的 `.ingest` `.set-or-append` 命令，將資料從 azure 資料總管複製到外部資料存放區 `.export` 。
如需命令活動的詳細逐步解說，請參閱[使用 Azure Data Factory 命令活動來執行 Azure 資料總管控制命令](data-factory-command-activity.md)。  有時候，使用 control 命令來複製資料可能會比複製活動更快速且更便宜。 若要判斷何時要使用命令活動與複製活動的比較，請參閱[複製資料時，複製和命令活動之間的選擇](#select-between-copy-and-azure-data-explorer-command-activities-when-copy-data)。

### <a name="copy-in-bulk-from-a-database-template"></a>從資料庫範本大量複製

[使用 Azure Data Factory 範本，從資料庫大量複製到 Azure 資料總管](data-factory-template.md)是預先定義的 Azure Data Factory 管線。 此範本可用來為每個資料庫或每個資料表建立許多管線，以加快資料複製速度。 

### <a name="mapping-data-flows"></a>對應資料流程 

[Azure Data Factory 對應](/azure/data-factory/concepts-data-flow-overview)的資料流程是以視覺化方式設計的資料轉換，可讓資料工程師開發圖形化資料轉換邏輯，而不需要撰寫程式碼。 若要建立資料流程，並將資料內嵌至 Azure 資料總管，請使用下列方法：

1. 建立[對應資料流程](/azure/data-factory/data-flow-create)。
1. [將資料匯出至 Azure Blob](/azure/data-factory/data-flow-sink)。 
1. 定義[事件方格](ingest-data-event-grid.md)或[ADF 複製活動](data-factory-load-data.md)，以將資料內嵌至 Azure 資料總管。

## <a name="select-between-copy-and-azure-data-explorer-command-activities-when-copy-data"></a>複製資料時，請選取 [複製] 和 [Azure 資料總管命令] 活動 

本節將協助您為數據複製需求選取正確的活動。

將資料從或複製到 Azure 資料總管時，Azure Data Factory 有兩個可用的選項：
* 複製活動。
* Azure 資料總管命令活動，其會執行其中一個在 Azure 資料總管中傳輸資料的控制命令。 

### <a name="copy-data-from-azure-data-explorer"></a>從 Azure 資料總管複製資料
  
您可以使用複製活動或命令，從 Azure 資料總管複製資料 [`.export`](kusto/management/data-export/index.md) 。 `.export`命令會執行查詢，然後匯出查詢的結果。 

請參閱下表，以取得複製活動的比較，以及 `.export` 從 Azure 資料總管複製資料的命令。

| | 複製活動 | 。 export 命令 |
|---|---|---|
| **流程描述** | ADF 會在 Kusto 上執行查詢、處理結果，並將其傳送至目標資料存放區。 <br>（**ADX > ADF > 接收資料存放區**） | ADF 會將 `.export` 控制命令傳送至 Azure 資料總管，這會執行命令，並將資料直接傳送至目標資料存放區。 <br>（**ADX > 接收資料存放區**） |
| **支援的目標資料存放區** | 各種[支援的資料存放區](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) | ADLSv2、Azure Blob SQL Database |
| **效能** | 統一 | <ul><li>分散式（預設值），同時從多個節點匯出資料</li><li>更快和 COGS 的效率。</li></ul> |
| **伺服器限制** | [查詢限制](kusto/concepts/querylimits.md)可以是擴充/停用。 根據預設，ADF 查詢包含： <ul><li>500000記錄或 64 MB 的大小限制。</li><li>10分鐘的時間限制。</li><li>`noTruncation`設定為 false。</li></ul> | 根據預設，會延伸或停用查詢限制： <ul><li>已停用大小限制。</li><li>伺服器超時時間延長為1小時。</li><li>`MaxMemoryConsumptionPerIterator`和 `MaxMemoryConsumptionPerQueryPerNode` 會延伸到最大值（5 GB、TotalPhysicalMemory/2）。</li></ul>

> [!TIP]
> 如果您的「複製目的地」是命令所支援的其中一個資料存放區 `.export` ，而且如果沒有任何「複製活動」功能對您的需求很重要，請選取 `.export` 命令。

### <a name="copying-data-to-azure-data-explorer"></a>將資料複製到 Azure 資料總管

您可以使用複製活動或內嵌命令將資料複製到 Azure 資料總管，例如從[查詢](kusto/management/data-ingestion/ingest-from-query.md)內嵌（ `.set-or-append` 、 `.set-or-replace` 、 `.set` 、 `.replace)` 和[從儲存體](kusto/management/data-ingestion/ingest-from-storage.md)內嵌（ `.ingest` ）。 

如需複製活動的比較，以及將資料複製到 Azure 資料總管的內嵌命令，請參閱下表。

| | 複製活動 | 從查詢內嵌<br> `.set-or-append` / `.set-or-replace` / `.set` / `.replace` | 從儲存體內嵌 <br> `.ingest` |
|---|---|---|---|
| **流程描述** | ADF 會從來源資料存放區取得資料，並將它轉換成表格式格式，並執行必要的架構對應變更。 然後 ADF 會將資料上傳至 Azure blob，將其分割成區塊，然後下載 blob 以將其內嵌到 ADX 資料表中。 <br> （**來源資料存放區 > ADF > Azure blob > ADX**） | 這些命令可以執行查詢或 `.show` 命令，並將查詢的結果內嵌到資料表中（**ADX > ADX**）。 | 此命令會藉由「提取」一或多個雲端儲存體成品中的資料，將資料內嵌到資料表中。 |
| **支援的來源資料存放區** |  [各種選項](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) | ADLS Gen 2、Azure Blob、SQL （使用 sql_request 外掛程式）、Cosmos （使用 cosmosdb_sql_request 外掛程式）和任何其他提供 HTTP 或 Python Api 的資料存放區。 | Filesystem、Azure Blob 儲存體、ADLS Gen 1、ADLS Gen 2 |
| **效能** | 擷取已排入佇列並受到管理，可確保小規模擷取，並藉由提供負載平衡、重試和錯誤處理來確保高可用性。 | <ul><li>這些命令並不是為了匯入大量資料而設計。</li><li>如預期般運作，並降低成本。 但針對生產案例，以及當流量速率和資料大小很大時，請使用複製活動。</li></ul> |
| **伺服器限制** | <ul><li>沒有大小限制。</li><li>最大超時限制：每個內嵌 blob 1 小時。 |<ul><li>查詢元件只有大小限制，可以藉由指定來略過 `noTruncation=true` 。</li><li>最大超時限制：1小時。</li></ul> | <ul><li>沒有大小限制。</li><li>最大超時限制：1小時。</li></ul>|

> [!TIP]
> * 將資料從 ADF 複製到 Azure 時資料總管使用 `ingest from query` 命令。  
> * 若為大型資料集（>1GB），請使用複製活動。  

## <a name="required-permissions"></a>所需的權限

下表列出與 Azure Data Factory 整合的各種步驟所需的許可權。

| 步驟 | 作業 | 最低許可權層級 | 附註 |
|---|---|---|---|
| **建立連結服務** | 資料庫導覽 | *資料庫檢視器* <br>使用 ADF 的登入使用者應該獲得授權，才能讀取資料庫中繼資料。 | 使用者可以手動提供資料庫名稱。 |
| | [測試連接] | *資料庫監視器*或*資料表擷取器* <br>服務主體應該獲得授權，以執行資料庫層級 `.show` 命令或資料表層級內嵌。 | <ul><li>TestConnection 會確認與叢集的連線，而不會驗證資料庫的連接。 即使資料庫不存在，也會成功。</li><li>資料表管理員許可權不足。</li></ul>|
| **建立資料集** | 資料表流覽 | *資料庫監視器* <br>使用 ADF 登入的使用者必須獲得授權，才能執行資料庫層級 `.show` 命令。 | 使用者可以手動提供資料表名稱。|
| **建立資料集**或**複製活動** | 預覽資料 | *資料庫檢視器* <br>服務主體必須獲得授權，才能讀取資料庫中繼資料。 | | 
|   | 匯入架構 | *資料庫檢視器* <br>服務主體必須獲得授權，才能讀取資料庫中繼資料。 | 當 ADX 是表格式對表格式複製的來源時，ADF 會自動匯入架構，即使使用者未明確匯入架構也一樣。 |
| **ADX 作為接收** | 建立依名稱的資料行對應 | *資料庫監視器* <br>服務主體必須獲得授權，才能執行資料庫層級 `.show` 命令。 | <ul><li>所有必要的作業都適用*資料表擷取器*。</li><li> 某些選擇性的作業可能會失敗。</li></ul> |
|   | <ul><li>在資料表上建立 CSV 對應</li><li>卸載對應</li></ul>| *資料表擷取器*或*資料庫管理員* <br>服務主體必須獲得授權，才能對資料表進行變更。 | |
|   | 擷取資料 | *資料表擷取器*或*資料庫管理員* <br>服務主體必須獲得授權，才能對資料表進行變更。 | | 
| **ADX 作為來源** | 執行查詢 | *資料庫檢視器* <br>服務主體必須獲得授權，才能讀取資料庫中繼資料。 | |
| **Kusto 命令** | | 根據每個命令的許可權層級。 |

## <a name="performance"></a>效能 

如果 Azure 資料總管是來源，而且您使用包含查詢的查閱、複製或命令活動，請參閱效能資訊的[查詢最佳做法](kusto/query/best-practices.md)和[複製活動的 ADF 檔](/azure/data-factory/copy-activity-performance)。
  
本節說明 Azure 資料總管為接收的「複製活動」的使用。 Azure 資料總管接收的預估輸送量為 11-13 MBps。 下表詳細說明影響 Azure 資料總管接收效能的參數。

| 參數 | 附註 |
|---|---|
| **元件地理鄰近** | 將所有元件放在相同的區域中：<ul><li>來源和接收資料存放區。</li><li>ADF 整合執行時間。</li><li>您的 ADX 叢集。</li></ul>請確定您的整合執行時間至少與您的 ADX 叢集位於相同的區域。 |
| **Diu 數目** | 1個 VM，適用于 ADF 所使用的每4個 Diu。 <br>只有當您的來源是包含多個檔案的檔案型存放區時，才會增加 Diu 的協助。 然後，每個 VM 會以平行方式處理不同的檔案。 因此，複製單一大型檔案的延遲會比複製多個較小的檔案還高。|
|**ADX 叢集的數量和 SKU** | 大量的 ADX 節點會大幅提升內嵌處理時間。|
| **平行處理原則** |    若要從資料庫複製非常大量的資料，請分割您的資料，然後使用 ForEach 迴圈，以平行方式複製每個分割區，或使用[從資料庫到 Azure 的大量複製資料總管範本](data-factory-template.md)。 注意： **Settings**  >  複製活動中平行處理原則**的設定程度**與 ADX 無關。 |
| **資料處理複雜度** | 延遲會根據來源檔案格式、資料行對應和壓縮而有所不同。|
| **執行您整合執行時間的 VM** | <ul><li>針對 Azure 複製，ADF Vm 和機器 Sku 無法變更。</li><li> 若要從內部內部部署至 Azure 複製，請判斷主控自我裝載 IR 的 VM 已夠強。</li></ul>|

## <a name="tips-and-common-pitfalls"></a>秘訣和常見的陷阱

### <a name="monitor-activity-progress"></a>監視活動進度

* 當監視活動進度時，[*資料寫入*] 屬性可能會大於 [*資料讀取*] 屬性，因為*資料讀取*是根據二進位檔案大小來計算，而*寫入的資料*則是根據記憶體中的大小來計算，並在資料解除序列化和解壓縮之後。

* 監視活動進度時，您會看到資料已寫入 Azure 資料總管接收。 查詢 Azure 資料總管資料表時，您會看到資料尚未抵達。 這是因為複製到 Azure 資料總管時有兩個階段。 
    * 第一個階段會讀取來源資料、將其分割為 900 MB 的區塊，並將每個區塊上傳至 Azure Blob。 [ADF 活動進度] 視圖會看到第一個階段。 
    * 當所有資料都上傳至 Azure Blob 時，第二個階段就會開始。 Azure 資料總管引擎節點會下載 blob，並將資料內嵌至接收資料表。 然後，資料會顯示在您的 Azure 資料總管資料表中。

### <a name="failure-to-ingest-csv-files-due-to-improper-escaping"></a>因為不正確的轉義而無法內嵌 CSV 檔案

Azure 資料總管預期 CSV 檔案會與[RFC 4180](https://www.ietf.org/rfc/rfc4180.txt)一致。
它預期：
* 包含需要進行轉義之字元的欄位（例如「和新行」）開頭和結尾都應該是一個不**含空白字元的**字元。 欄位*內*的所有「**字元都會**使用**雙字元（** **"**」）進行轉義。 例如，" _Hello，" world ""_ "是有效的 CSV 檔案，具有單一記錄，其中包含具有內容_Hello，" World "_ 的單一資料行或欄位。
* 檔案中的所有記錄都必須具有相同數目的資料行和欄位。

Azure Data Factory 允許反斜線（escape）字元。 如果您使用 Azure Data Factory 產生含有反斜線字元的 CSV 檔案，將檔案內嵌至 Azure 資料總管將會失敗。

#### <a name="example"></a>範例

下列文字值： Hello，"World"<br/>
ABC 定義<br/>
"ABC\D" EF<br/>
「ABC 定義<br/>

應該會出現在適當的 CSV 檔案中，如下所示： "Hello，" World "" "<br/>
"ABC DEF"<br/>
"" ABC DEF "<br/>
"" "ABC\D" "EF"<br/>

藉由使用預設的 escape 字元（反斜線），下列 CSV 將無法搭配 Azure 資料總管： "Hello， \" World \" "<br/>
"ABC DEF"<br/>
" \" ABC DEF"<br/>
「 \" ABC\D \" EF」<br/>

### <a name="nested-json-objects"></a>嵌套的 JSON 物件

將 JSON 檔案複製到 Azure 資料總管時，請注意：
* 不支援陣列。
* 如果您的 JSON 結構包含物件資料類型，Azure Data Factory 將會壓平合併物件的子專案，並嘗試將每個子專案對應到 Azure 資料總管資料表中的不同資料行。 如果您想要將整個物件專案對應至 Azure 中的單一資料行資料總管：
    * 將整個 JSON 資料列內嵌到 Azure 資料總管中的單一動態資料行。
    * 使用 Azure Data Factory 的 JSON 編輯器，手動編輯管線定義。 在**對應**中
       * 移除針對每個子專案所建立的多個對應，然後新增單一對應，將您的物件類型對應至您的資料表資料行。
       * 在右方括弧後面加上逗號，後面接著：<br/>
       `"mapComplexValuesToString": true`.

### <a name="specify-additionalproperties-when-copying-to-azure-data-explorer"></a>複製到 Azure 資料總管時指定 AdditionalProperties

> [!NOTE]
> 這項功能目前可透過手動編輯 JSON 承載來取得。 

在複製活動的 [接收] 區段底下新增單一資料列，如下所示：

```json
"sink": {
    "type": "AzureDataExplorerSink",
    "additionalProperties": "{\"tags\":\"[\\\"drop-by:account_FiscalYearID_2020\\\"]\"}"
},
```

值的轉義可能會很棘手。 使用下列程式碼片段做為參考：

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

列印的值：

```json
{"ignoreFirstRecord":"false","csvMappingReference":"Table1_mapping_1","ingestIfNotExists":"[\"Part0001\"]","tags":"[\"ingest-by:Part0001\",\"ingest-by:IngestedByTest\"]"}
```

## <a name="next-steps"></a>後續步驟

* 瞭解如何[使用 Azure Data Factory 將資料複製到 Azure 資料總管](data-factory-load-data.md)。
* 瞭解如何使用[Azure Data Factory 範本，從資料庫大量複製到 Azure 資料總管](data-factory-template.md)。
* 瞭解如何使用[Azure Data Factory 命令活動來執行 Azure 資料總管控制命令](data-factory-command-activity.md)。
* 瞭解用於資料查詢的[Azure 資料總管查詢](web-query-data.md)。



