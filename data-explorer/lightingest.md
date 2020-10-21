---
title: 使用 LightIngest 將資料內嵌至 Azure 資料總管。
description: 瞭解 LightIngest，這是一種命令列公用程式，適用于將臨機運算元據內嵌至 Azure 資料總管。
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 06/28/2020
ms.openlocfilehash: 5e15983039209e2e0c62ebd761e416ebb3bd1076
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342615"
---
# <a name="use-lightingest-to-ingest-data-to-azure-data-explorer"></a>使用 LightIngest 將資料內嵌至 Azure 資料總管
 
LightIngest 是命令列公用程式，適用于將臨機運算元據內嵌至 Azure 資料總管中。 公用程式可以從本機資料夾或 Azure blob 儲存體容器提取來源資料。
當您想要內嵌大量資料時，LightIngest 最有用，因為內嵌持續時間沒有時間限制。 當您稍後想要根據建立的時間查詢記錄，而不是內嵌時，它也很有用。

## <a name="prerequisites"></a>先決條件

* LightIngest-將其下載為 Kusto 的一部分 [NuGet 套件](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/)

    :::image type="content" source="media/lightingest/lightingest-download-area.png" alt-text="Lightingest 下載":::

* WinRAR-從[www.win-rar.com/download.html](http://www.win-rar.com/download.html)下載

## <a name="install-lightingest"></a>安裝 LightIngest

1. 流覽至您的電腦上下載 LightIngest 的位置。
1. 使用 WinRAR 將 *工具* 目錄解壓縮至您的電腦。

## <a name="run-lightingest"></a>執行 LightIngest

1. 流覽至您電腦上已解壓縮的 *工具* 目錄。
1. 從位置列刪除現有的位置資訊。

    :::image type="content" source="media/lightingest/lightingest-locationbar.png" alt-text="Lightingest 下載":::


1. 輸入 `cmd` ，然後按 **enter**鍵。
1. 在命令提示字元中，輸入 `LightIngest.exe` 後面接著相關的命令列引數。

    > [!Tip]
    > 如需支援的命令列引數清單，請輸入 `LightIngest.exe /help` 。
    >
    > :::image type="content" source="media/lightingest/lightingest-cmd-line-help.png" alt-text="Lightingest 下載":::

1. 輸入， `ingest-` 後面接著將會管理內嵌的 Azure 資料總管叢集的連接字串。
    以雙引號括住連接字串，並遵循 [Kusto 連接字串規格](kusto/api/connection-strings/kusto.md)。

    例如：

    ```
    ingest-{Cluster name and region}.kusto.windows.net;AAD Federated Security=True -db:{Database} -table:Trips -source:"https://{Account}.blob.core.windows.net/{ROOT_CONTAINER};{StorageAccountKey}" -pattern:"*.csv.gz" -format:csv -limit:2 -ignoreFirst:true -cr:10.0 -dontWait:true
    ```

### <a name="recommendations"></a>建議

* 建議的方法是讓 LightIngest 使用中的內嵌端點 `https://ingest-{yourClusterNameAndRegion}.kusto.windows.net` 。 如此一來，Azure 資料總管服務可以管理內嵌負載，而且您可以輕鬆地從暫時性錯誤中復原。 不過，您也可以將 LightIngest 設定為直接使用引擎端點 (`https://{yourClusterNameAndRegion}.kusto.windows.net`) 。

   > [!NOTE]
   > 如果您直接內嵌引擎端點，則不需要包含 `ingest-` 。 但是，不會有可保護引擎和改善內嵌成功率的 DM 功能。

* 為了達到最佳的內嵌效能，需要原始資料大小，讓 LightIngest 可以估計本機檔案的未壓縮大小。 不過，LightIngest 可能無法正確估計已壓縮 blob 的原始大小，而不需要先下載它們。 因此，在擷取壓縮的 blob 時，請將 `rawSizeBytes` blob 中繼資料上的屬性設定為未壓縮的資料大小（以位元組為單位）。

## <a name="command-line-arguments"></a>命令列引數

|引數名稱            |類型     |說明       |強制/選用
|------------------------------|--------|----------|-----------------------------|
|                               |字串   |[Azure 資料總管連接字串](kusto/api/connection-strings/kusto.md) ，指定將處理內嵌的 Kusto 端點。 應以雙引號括住 | 強制性
|-database、-db          |字串  |目標 Azure 資料總管資料庫名稱 | 選擇性  |
|-資料表                  |字串  |目標 Azure 資料總管資料表名稱 | 強制性 |
|-sourcePath、-source      |字串  |Blob 容器的來源檔案或根 URI 路徑。 如果資料在 blob 中，必須包含儲存體帳戶金鑰或 SAS。 建議用雙引號括住 |強制性 |
|-前置詞                  |字串  |當要內嵌的來源資料位於 blob 儲存體時，所有 blob 都會共用此 URL 前置詞，但不包括容器名稱。 <br>例如，如果資料是在中 `MyContainer/Dir1/Dir2` ，則前置詞應為 `Dir1/Dir2` 。 建議用雙引號括住 | 選擇性  |
|-模式        |字串  |從中挑選原始程式檔/blob 的模式。 支援萬用字元。 例如： `"*.csv"` 。 建議用雙引號括住 | 選擇性  |
|-zipPattern     |字串  |在 ZIP 封存中選取要內嵌的檔案時，所要使用的正則運算式。<br>封存中的所有其他檔案都會被忽略。 例如： `"*.csv"` 。 建議將它括在雙引號中 | 選擇性  |
|-format、-f           |字串  | 源資料格式。 必須是其中一種 [支援的格式](ingestion-supported-formats.md) | 選擇性  |
|-ingestionMappingPath, -mappingPath |字串  |內嵌資料行對應的本機檔案路徑。 Json 和 Avro 格式的必要參數。 查看[資料](kusto/management/mappings.md)對應 | 選擇性  |
|-ingestionMappingRef, -mappingRef  |字串  |先前在資料表上建立之內嵌資料行對應的名稱。 Json 和 Avro 格式的必要參數。 查看[資料](kusto/management/mappings.md)對應 | 選擇性  |
|-creationTimePattern      |字串  |設定時，會用來從檔案或 blob 路徑中解壓縮 CreationTime 屬性。 瞭解[如何使用 `CreationTime` 內嵌資料](#how-to-ingest-data-using-creationtime) |選擇性  |
|-ignoreFirstRow, -ignoreFirst |bool    |如果設定，則會忽略每個檔案/blob 的第一筆記錄 (例如，如果來源資料有標頭)  | 選擇性  |
|-tag            |字串   |要與內嵌資料相關聯的[標記](kusto/management/extents-overview.md#extent-tagging)。 允許多個出現次數 | 選擇性  |
|-dontWait           |bool     |如果設定為 ' true '，則不會等候內嵌完成。 擷取大量檔案/blob 時有用 |選擇性  |
|-壓縮、-cr          |double |壓縮比例提示。 在擷取壓縮檔案/blob 時很實用，可協助 Azure 資料總管評估原始資料大小。 以原始大小除以壓縮大小計算 |選擇性  |
|-limit、-l           |整數   |如果設定，則會將內嵌限制為前 N 個檔案 |選擇性  |
|-listOnly，-list        |bool    |如果設定，則只會顯示已針對內嵌選取的專案| 選擇性  |
|-ingestTimeout   |整數  |所有內嵌作業完成的時間（以分鐘為單位）。 預設為 `60`| 選擇性  |
|-forceSync        |bool  |如果設定，則會強制執行同步內嵌。 預設為 `false` |選擇性  |
|-dataBatchSize        |整數  |設定每個內嵌作業的總大小限制 (MB、未壓縮)  |選擇性  |
|-filesInBatch            |整數 |設定每個內嵌作業的檔案/blob 計數限制 |選擇性  |
|-devTracing、-trace       |字串    |如果設定，則會根據預設，在目前的目錄中將診斷記錄寫入本機目錄 (， `RollingLogs` 或藉由設定參數值來修改它們)  | 選擇性  |

## <a name="azure-blob-specific-capabilities"></a>Azure blob 特定功能

與 Azure blob 搭配使用時，LightIngest 會使用特定的 blob 中繼資料屬性來增強內嵌進程。

|Metadata 屬性                            | 使用方式                                                                           |
|---------------------------------------------|---------------------------------------------------------------------------------|
|`rawSizeBytes`, `kustoUncompressedSizeBytes` | 如果設定，將會被視為未壓縮的資料大小                       |
|`kustoCreationTime`, `kustoCreationTimeUtc`  | 解釋為 UTC 時間戳記。 如果設定，將會用來覆寫 Kusto 中的建立時間。 適用于回填案例 |

## <a name="usage-examples"></a>使用範例

<!-- Waiting for Tzvia or Vladik to rewrite the instructions for this example before publishing it

### Ingesting a specific number of blobs in JSON format

* Ingest two blobs under a specified storage account {Account}, in `JSON` format matching the pattern `.json`
* Destination is the database {Database}, the table `SampleData`
* Indicate that your data is compressed with the approximate ratio of 10.0
* LightIngest won't wait for the ingestion to be completed

To use the LightIngest command below:
1. Create a table command and enter the table name into the LightIngest command, replacing `SampleData`.
1. Create a mapping command and enter the IngestionMappingRef command, replacing `SampleData_mapping`.
1. Copy your cluster name and enter it into the LightIngest command, replacing `{ClusterandRegion}`.
1. Enter the database name into the LightIngest command, replacing `{Database name}`.
1. Replace `{Account}` with your account name and replace `{ROOT_CONTAINER}?{SAS token}` with the appropriate information.

    ```
    LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"  
        -db:{Database name} 
        -table:SampleData 
        -source:"https://{Account}.blob.core.windows.net/{ROOT_CONTAINER}?{SAS token}" 
        -IngestionMappingRef:SampleData_mapping 
        -pattern:"*.json" 
        -format:JSON 
        -limit:2 
        -cr:10.0 
        -dontWait:true
    ```
     
1. In Azure Data Explorer, open query count.

    ![Ingestion result in Azure Data Explorer](media/lightingest/lightingest-show-failure-count.png)
-->

### <a name="how-to-ingest-data-using-creationtime"></a>如何使用 CreationTime 內嵌資料

當您從現有系統將歷程記錄資料載入至 Azure 資料總管時，所有記錄都會收到相同的內嵌日期。 若要依據建立時間來分割資料，而不是內嵌時間，您可以使用 `-creationTimePattern` 引數。 `-creationTimePattern`引數會 `CreationTime` 從檔案或 blob 路徑中解壓縮屬性。 模式不需要反映整個專案路徑，只是封閉您要使用之時間戳記的區段。

引數值必須包含：
* 緊接在時間戳記格式前面的常文字，以單引號括住 (前置詞) 
* 標準[.Net 日期時程表示法](/dotnet/standard/base-types/custom-date-and-time-format-strings)中的時間戳記格式
* 緊接在時間戳記 (後置詞) 的常文字。

**範例** 

* 包含日期時間的 blob 名稱，如下所示： `historicalvalues19840101.parquet` (時間戳記是四位數的年份、月份的兩位數，以及每月日的兩位數) ， 
    
    `-creationTimePattern`引數的值是檔案名： *"' historicalvalues'yyyyMMdd '. parquet '"* 的一部分。

    ```kusto
    ingest-{Cluster name and region}.kusto.windows.net;AAD Federated Security=True -db:{Database} -table:Trips -source:"https://{Account}.blob.core.windows.net/{ROOT_CONTAINER};{StorageAccountKey}" -creationTimePattern:"'historicalvalues'yyyyMMdd'.parquet'"
     -pattern:"*.csv.gz" -format:csv -limit:2 -ignoreFirst:true -cr:10.0 -dontWait:true
    ```

* 對於參考階層式資料夾結構的 blob URI `https://storageaccount/container/folder/2002/12/01/blobname.extension` ，例如 

    `-creationTimePattern`引數的值是資料夾結構的一部分： *"' 資料夾/' YYYY/MM/dd '/blob '"*

   ```kusto
    ingest-{Cluster name and region}.kusto.windows.net;AAD Federated Security=True -db:{Database} -table:Trips -source:"https://{Account}.blob.core.windows.net/{ROOT_CONTAINER};{StorageAccountKey}" -creationTimePattern:"'folder/'yyyy/MM/dd'/blob'"
     -pattern:"*.csv.gz" -format:csv -limit:2 -ignoreFirst:true -cr:10.0 -dontWait:true
    ```

### <a name="ingesting-blobs-using-a-storage-account-key-or-a-sas-token"></a>使用儲存體帳戶金鑰或 SAS 權杖擷取 blob

* 在指定的儲存體帳戶 `ACCOUNT` 、資料夾、容器下內嵌10個 blob `DIR` `CONT` ，並符合模式 `*.csv.gz`
* 目的地是資料庫 `DB` 、資料表 `TABLE` ，且內嵌對應 `MAPPING` 是在目的地上預先建立
* 此工具會等待內嵌作業完成
* 請注意指定目標資料庫和儲存體帳戶金鑰與 SAS 權杖的不同選項

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"https://ACCOUNT.blob.core.windows.net/{ROOT_CONTAINER};{StorageAccountKey}"
  -prefix:"DIR"
  -pattern:*.csv.gz
  -format:csv
  -mappingRef:MAPPING
  -limit:10

LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True;Initial Catalog=DB"
  -table:TABLE
  -source:"https://ACCOUNT.blob.core.windows.net/{ROOT_CONTAINER}?{SAS token}"
  -prefix:"DIR"
  -pattern:*.csv.gz
  -format:csv
  -mappingRef:MAPPING
  -limit:10
```

### <a name="ingesting-all-blobs-in-a-container-not-including-header-rows"></a>擷取容器中的所有 blob，不包括標頭資料列

* 內嵌指定的儲存體帳戶 `ACCOUNT` 、資料夾 `DIR1/DIR2` 、容器下的所有 blob `CONT` ，並符合模式 `*.csv.gz`
* 目的地是資料庫 `DB` 、資料表 `TABLE` ，且內嵌對應 `MAPPING` 是在目的地上預先建立
* 來源 blob 包含標頭行，因此會指示工具卸載每個 blob 的第一筆記錄
* 此工具將會張貼資料進行內嵌，而不會等候內嵌作業完成

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"https://ACCOUNT.blob.core.windows.net/{ROOT_CONTAINER}?{SAS token}"
  -prefix:"DIR1/DIR2"
  -pattern:*.csv.gz
  -format:csv
  -mappingRef:MAPPING
  -ignoreFirstRow:true
```

### <a name="ingesting-all-json-files-from-a-path"></a>從路徑擷取所有 JSON 檔案

* 內嵌路徑下的所有檔案 `PATH` ，並符合模式 `*.json`
* 目的地是資料庫 `DB` 、資料表 `TABLE` ，且內嵌對應是在本機檔案中定義 `MAPPING_FILE_PATH`
* 此工具將會張貼資料進行內嵌，而不會等候內嵌作業完成

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"PATH"
  -pattern:*.json
  -format:json
  -mappingPath:"MAPPING_FILE_PATH"
```

### <a name="ingesting-files-and-writing-diagnostic-trace-files"></a>擷取檔案和寫入診斷追蹤檔案

* 內嵌路徑下的所有檔案 `PATH` ，並符合模式 `*.json`
* 目的地是資料庫 `DB` 、資料表 `TABLE` ，且內嵌對應是在本機檔案中定義 `MAPPING_FILE_PATH`
* 此工具將會張貼資料進行內嵌，而不會等候內嵌作業完成
* 診斷追蹤檔案將會以本機方式寫入資料夾下 `LOGS_PATH`

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"PATH"
  -pattern:*.json
  -format:json
  -mappingPath:"MAPPING_FILE_PATH"
  -trace:"LOGS_PATH"
```