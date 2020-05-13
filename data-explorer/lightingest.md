---
title: LightIngest 是命令列公用程式，可用於內嵌至 Azure 資料總管。
description: 深入瞭解 LightIngest，這是一種命令列公用程式，可用於將臨機運算元據內嵌至 Azure 資料總管。
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 8d4eeb47abb8eac2b042b64e65b55dac7e91d6c9
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83374047"
---
# <a name="install-and-use-lightingest"></a>安裝和使用 LightIngest

LightIngest 是命令列公用程式，可用於將臨機運算元據內嵌至 Azure 資料總管。
公用程式可以從本機資料夾或從 Azure blob 儲存體容器提取來源資料。

## <a name="prerequisites"></a>Prerequisites

* LightIngest-將其下載為 Kusto 的一部分[NuGet 套件](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/)

    ![Lightingest 下載](media/lightingest/lightingest-download-area.png)

* WinRAR-從[www.win-rar.com/download.html](http://www.win-rar.com/download.html)下載

## <a name="install-lightingest"></a>安裝 LightIngest

1. 流覽至您電腦上下載 LightIngest 的位置。 
1. 使用 WinRAR，將*工具*目錄解壓縮至您的電腦。

## <a name="run-lightingest"></a>執行 LightIngest

1. 流覽至您電腦上的已解壓縮*工具*目錄。
1. 從 [位置] 列刪除現有的位置資訊。
    
      ![刪除位置資訊](media/lightingest/lightingest-location-bar.png)

1. 輸入 `cmd` ，然後按**enter**鍵。
1. 在命令提示字元中，輸入， `LightIngest.exe` 後面接著相關的命令列引數。

    > [!Tip]
    > 如需支援的命令列引數清單，請輸入 `LightIngest.exe /help` 。
    >
    >![命令列說明](media/lightingest/lightingest-cmd-line-help.png)

1. 輸入， `ingest-` 後面接著將會管理內嵌的 Azure 資料總管叢集的連接字串。
    以雙引號括住連接字串，並遵循[Kusto 連接字串規格](kusto/api/connection-strings/kusto.md)。

    例如：
    ```
    ingest-{Cluster name and region}.kusto.windows.net;AAD Federated Security=True -db:{Database} -table:Trips -source:"https://{Account}.blob.core.windows.net/{ROOT_CONTAINER};{StorageAccountKey}" -pattern:"*.csv.gz" -format:csv -limit:2 -ignoreFirst:true -cr:10.0 -dontWait:true
    ```

* 建議的方法是讓 LightIngest 在中使用內嵌端點 `https://ingest-{yourClusterNameAndRegion}.kusto.windows.net` 。 如此一來，Azure 資料總管服務就可以管理內嵌負載，而且您可以輕鬆地從暫時性錯誤中復原。 不過，您也可以將 LightIngest 設定為直接與引擎端點（）搭配使用 `https://{yourClusterNameAndRegion}.kusto.windows.net` 。

> [!Note]
> 如果您直接內嵌引擎端點，則不需要包含 `ingest-` ，但也不會有 DM 功能來保護引擎並改善內嵌成功率。

* 為了達到最佳的內嵌效能，LightIngest 必須知道原始資料大小，因此 LightIngest 會估計本機檔案的未壓縮大小。 不過，LightIngest 可能無法正確估計壓縮 blob 的原始大小，而不需要先下載它們。 因此，內嵌壓縮的 blob 時，請將 `rawSizeBytes` blob 中繼資料上的屬性設定為未壓縮的資料大小（以位元組為單位）。

## <a name="general-command-line-arguments"></a>一般命令列引數

|引數名稱         |簡短名稱   |類型    |強制性 |描述                                |
|----------------------|-------------|--------|----------|-------------------------------------------|
|                      |             |字串  |強制性 |[Azure 資料總管連接字串](kusto/api/connection-strings/kusto.md)，指定將處理內嵌的 Kusto 端點。 應以雙引號括住 |
|-資料庫             |-db          |字串  |選用  |目標 Azure 資料總管資料庫名稱 |
|-table                |             |字串  |強制性 |目標 Azure 資料總管資料表名稱 |
|-sourcePath           |-source      |字串  |強制性 |Blob 容器的來源檔案或根 URI 路徑。 如果資料位於 blob 中，必須包含儲存體帳戶金鑰或 SAS。 建議以雙引號括住 |
|-前置詞               |             |字串  |選用  |當要內嵌的來源資料位於 blob 儲存體時，此 URL 首碼會由所有 blob 共用，但不包括容器名稱。 <br>例如，如果資料在中，則 `MyContainer/Dir1/Dir2` 前置詞應該是 `Dir1/Dir2` 。 建議以雙引號括住 |
|-pattern              |             |字串  |選用  |從中挑選原始程式檔/blob 的模式。 支援萬用字元。 例如 `"*.csv"`。 建議以雙引號括住 |
|-zipPattern           |             |字串  |選用  |選取要內嵌之 ZIP 封存中的檔案時，所要使用的正則運算式。<br>封存中的所有其他檔案都會被忽略。例如， `"*.csv"` 。 建議以雙引號括住 |
|-格式               |-f           |字串  |選用  |源資料格式。 必須是其中一種[支援的格式](ingestion-supported-formats.md) |
|-ingestionMappingPath |-mappingPath |字串  |選用  |內嵌資料行對應檔的路徑（Json 和 Avro 格式的必要）。 請參閱[資料](kusto/management/mappings.md)對應 |
|-ingestionMappingRef  |-mappingRef  |字串  |選用  |預先建立的內嵌資料行對應名稱（Json 和 Avro 格式的必要項）。 請參閱[資料](kusto/management/mappings.md)對應 |
|-creationTimePattern  |             |字串  |選用  |設定時，會用來從檔案或 blob 路徑解壓縮 CreationTime 屬性。 請參閱[使用 CreationTimePattern 引數](#using-creationtimepattern-argument) |
|-ignoreFirstRow       |-ignoreFirst |bool    |選用  |如果設定，則會忽略每個檔案/blob 的第一筆記錄（例如，如果來源資料有標頭） |
|-tag                  |             |字串  |選用  |要與內嵌資料產生關聯的[標記](kusto/management/extents-overview.md#extent-tagging)。 允許多次發生 |
|-dontWait             |             |bool    |選用  |如果設定為 ' true '，則不會等候內嵌完成。 內嵌大量檔案/blob 時很有用 |

### <a name="using-creationtimepattern-argument"></a>使用 CreationTimePattern 引數

`-creationTimePattern`引數會從檔案或 blob 路徑中解壓縮 CreationTime 屬性。 此模式不需要反映整個專案路徑，只會包含您要使用的時間戳記區段。

引數值必須包含：
* 緊接在時間戳記前面的常數測試，以單引號括住
* 標準[.Net 日期時程表示法](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)中的時間戳記格式
* 緊接在時間戳記之後的常文字。 例如，如果 blob 名稱的結尾 `historicalvalues19840101.parquet` 是（時間戳記是年份的四位數，月份有兩個數字，月份日期有兩個數字），則引數的對應值 `-creationTimePattern` 會是：

```
ingest-{Cluster name and region}.kusto.windows.net;AAD Federated Security=True -db:{Database} -table:Trips -source:"https://{Account}.blob.core.windows.net/{ROOT_CONTAINER};{StorageAccountKey}" -creationTimePattern:"'historicalvalues'yyyyMMdd'.parquet'"
 -pattern:"*.csv.gz" -format:csv -limit:2 -ignoreFirst:true -cr:10.0 -dontWait:true
```

### <a name="command-line-arguments-for-advanced-scenarios"></a>Advanced 案例的命令列引數

|引數名稱         |簡短名稱   |類型    |強制性 |描述                                |
|----------------------|-------------|--------|----------|-------------------------------------------|
|-壓縮          |-cr          |double  |選用  |壓縮比例提示。 內嵌壓縮檔案/blob 以協助 Azure 資料總管評估原始資料大小時很有用。 以原始大小除以壓縮大小來計算 |
|-限制                |-l           |integer |選用  |若設定，會將內嵌限制為前 N 個檔案 |
|-listOnly             |-list        |bool    |選用  |如果設定，則只會顯示已選取要進行內嵌的專案| 
|-ingestTimeout        |             |integer |選用  |所有內嵌作業完成的超時（以分鐘為單位）。 預設為 `60`|
|-forceSync            |             |bool    |選用  |如果設定，則會強制執行同步內嵌。 預設為 `false` |
|-dataBatchSize        |             |integer |選用  |設定每個內嵌作業的總大小限制（MB、未壓縮） |
|-filesInBatch         |             |integer |選用  |設定每個內嵌作業的檔案/blob 計數限制 |
|-devTracing           |-trace       |字串  |選用  |如果設定，診斷記錄會寫入本機目錄（根據預設， `RollingLogs` 在目前的目錄中，或者可以藉由設定參數值來修改） |

## <a name="blob-metadata-properties"></a>Blob 中繼資料屬性
與 Azure blob 搭配使用時，LightIngest 會使用某些 blob 中繼資料屬性來增加內嵌進程。

|Metadata 屬性                            | 使用方式                                                                           |
|---------------------------------------------|---------------------------------------------------------------------------------|
|`rawSizeBytes`, `kustoUncompressedSizeBytes` | 如果設定，將會被視為未壓縮的資料大小                       |
|`kustoCreationTime`, `kustoCreationTimeUtc`  | 以 UTC 時間戳記的形式轉譯。 如果設定，將會用來覆寫 Kusto 中的建立時間。 適用于回填案例 |

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

    ![Injestion result in Azure Data Explorer](media/lightingest/lightingest-show-failure-count.png)
-->

### <a name="ingesting-blobs-using-a-storage-account-key-or-a-sas-token"></a>使用儲存體帳戶金鑰或 SAS 權杖來內嵌 blob

* 在 [容器] 底下的 [指定的儲存體帳戶 `ACCOUNT` 、資料夾] `DIR` `CONT` 和 [符合] 模式中內嵌10個 blob`*.csv.gz`
* 目的地是資料庫 `DB` 、資料表 `TABLE` ，以及 `MAPPING` 預先建立在目的地上的內嵌對應
* 此工具會等到內嵌作業完成
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

### <a name="ingesting-all-blobs-in-a-container-not-including-header-rows"></a>內嵌容器中的所有 blob，不包含標頭資料列

* 將所有 blob 內嵌在指定的儲存體帳戶 `ACCOUNT` 、資料夾 `DIR1/DIR2` 、容器底下， `CONT` 並符合模式`*.csv.gz`
* 目的地是資料庫 `DB` 、資料表 `TABLE` ，以及 `MAPPING` 預先建立在目的地上的內嵌對應
* 來源 blob 包含標頭行，因此會指示工具卸載每個 blob 的第一筆記錄
* 此工具會公佈資料進行內嵌，而不會等待內嵌作業完成

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

### <a name="ingesting-all-json-files-from-a-path"></a>從路徑內嵌所有 JSON 檔案

* 內嵌 path 底下的所有檔案 `PATH` ，符合模式`*.json`
* 目的地是資料庫 `DB` 、資料表 `TABLE` 和內嵌對應定義于本機檔案中`MAPPING_FILE_PATH`
* 此工具會公佈資料進行內嵌，而不會等待內嵌作業完成

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"PATH"
  -pattern:*.json
  -format:json
  -mappingPath:"MAPPING_FILE_PATH"
```

### <a name="ingesting-files-and-writing-diagnostic-trace-files"></a>內嵌檔案和寫入診斷追蹤檔案

* 內嵌 path 底下的所有檔案 `PATH` ，符合模式`*.json`
* 目的地是資料庫 `DB` 、資料表 `TABLE` 和內嵌對應定義于本機檔案中`MAPPING_FILE_PATH`
* 此工具會公佈資料進行內嵌，而不會等待內嵌作業完成
* 診斷追蹤檔案會以本機方式寫入資料夾`LOGS_PATH`

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
## <a name="changelog"></a>變更記錄
|版本        |變更                                                                             |
|---------------|------------------------------------------------------------------------------------|
|4.0.9.0        |<ul><li>已新增 `-zipPattern` 引數 </li><li>已新增 `-listOnly` 引數 </li><li>引數摘要會在執行生效之前顯示</li><li>根據引數，從 blob 中繼資料屬性或從 blob 或檔案名讀取 CreationTime `-creationTimePattern`</li></ul>|
