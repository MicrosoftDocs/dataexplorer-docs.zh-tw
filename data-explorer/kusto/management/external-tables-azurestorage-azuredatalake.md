---
title: 在 Azure 儲存體或 Azure Data Lake 中建立和變更外部資料表-Azure 資料總管
description: 本文描述如何在 Azure 儲存體或 Azure Data Lake 中建立和變更外部資料表
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 0532219b8efc1cab7508d1838882b6fa48f5048f
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92343261"
---
# <a name="create-and-alter-external-tables-in-azure-storage-or-azure-data-lake"></a>建立和改變 Azure 儲存體或 Azure Data Lake 中的外部資料表

下列命令說明如何建立位於 Azure Blob 儲存體、Azure Data Lake Store Gen1 或 Azure Data Lake Store Gen2 的外部資料表。 

如需外部 Azure 儲存體資料表功能的簡介，請參閱 [使用 Azure 資料總管查詢 Azure Data Lake 中的資料](../../data-lake-query-data.md)。

## <a name="create-or-alter-external-table"></a>. create 或. alter external table

**語法**

 (`.create`  |  `.alter`  |  `.create-or-alter`) `external` `table` *[TableName](#table-name)* `(` *[架構](#schema)*`)`  
`kind` `=` (`blob` | `adl`)  
[分割區 `partition` `by` `(` *[Partitions](#partitions)* `)` [ `pathformat` `=` `(` *[PathFormat](#path-format)* `)` ]]  
`dataformat``=` *[格式](#format)*  
`(`*[StorageConnectionString](#connection-string)* [ `,` ...]`)`   
[ `with` `(` *[PropertyName](#properties)* `=` *[值](#properties)* `,` ... `)` ]  

在執行命令的資料庫中建立或改變新的外部資料表。

> [!NOTE]
> * 如果資料表存在， `.create` 命令將會失敗並出現錯誤。 使用 `.create-or-alter` 或 `.alter` 來修改現有的資料表。
> * 不支援改變外部 blob 資料表的架構、格式或資料分割定義。 
> * 作業需要的 [資料庫使用者](../management/access-control/role-based-authorization.md) 權力 `.create` ，以及的 [資料表管理許可權](../management/access-control/role-based-authorization.md) `.alter` 。 

**參數**

<a name="table-name"></a>
*名*

符合 [機構名稱](../query/schema-entities/entity-names.md) 規則的外部資料表名稱。
外部資料表的名稱不能與相同資料庫中的一般資料表相同。

<a name="schema"></a>
*模式*

外部資料結構描述的使用格式如下：

&nbsp;&nbsp;*ColumnName* `:`*ColumnType* [ `,` *ColumnName* `:` *ColumnType* ...]

當 *ColumnName* 符合 [實體命名](../query/schema-entities/entity-names.md) 規則時， *ColumnType* 是其中一種 [支援的資料類型](../query/scalar-data-types/index.md)。

> [!TIP]
> 如果外部資料結構描述不明，請使用 [推斷 \_ 儲存 \_ 架構](../query/inferstorageschemaplugin.md) 外掛程式，以協助根據外部檔案內容推斷架構。

<a name="partitions"></a>
*分區*

用來分割外部資料表的資料行清單（以逗號分隔）。 資料分割資料行可以存在於資料檔案本身，或是檔案路徑的 sa 部分 (在) 的 [虛擬資料行](#virtual-columns) 上進一步瞭解。

分割區清單是資料分割資料行的任意組合，使用下列其中一種形式來指定：

* 代表 [虛擬資料行](#virtual-columns)的資料分割。

  *PartitionName* `:` (`datetime`  |  `string`) 

* 以字串資料行值為基礎的資料分割。

  *PartitionName* `:``string` `=` *ColumnName*

* 資料分割，以字串資料行值 [雜湊](../query/hashfunction.md)，模數 *數位*為基礎。

  *PartitionName* `:``long` `=` `hash``(` *ColumnName* `,` *數目*`)`

* 分割區，以日期時間資料行的截斷值為基礎。 請參閱 [startofyear](../query/startofyearfunction.md)、 [startofmonth](../query/startofmonthfunction.md)、 [startofweek](../query/startofweekfunction.md)、 [startofday](../query/startofdayfunction.md) 或 [bin](../query/binfunction.md) 函式的檔。

  *PartitionName* `:``datetime` `=` (`startofyear` \| `startofmonth` \| `startofweek` \| `startofday`) `(` *ColumnName*`)`  
  *PartitionName* `:``datetime` `=` `bin``(` *ColumnName* `,` *TimeSpan*`)`

若要檢查資料分割定義正確性，請 `sampleUris` 在建立外部資料表時使用屬性。

<a name="path-format"></a>
*PathFormat*

外部資料 URI 檔案路徑格式，除了分割區之外，還可以指定此格式。 路徑格式是一系列的資料分割元素和文字分隔符號：

&nbsp;&nbsp;[*StringSeparator*] *Partition* [*StringSeparator*] [*partition* [*StringSeparator*] ...]  

其中 *partition* 指的是在子句中宣告的資料分割 `partition` `by` ，而 *StringSeparator* 是以引號括住的任何文字。 連續的資料分割元素必須使用 *StringSeparator*來分開設定。

您可以使用轉譯為字串的資料分割元素，並以對應的文字分隔符號來建立原始檔案路徑前置詞。 若要指定用於轉譯 datetime 資料分割值的格式，可以使用下列宏：

&nbsp;&nbsp;`datetime_pattern``(` *DateTimeFormat* `,` *PartitionName*`)`  

其中 *DateTimeFormat* 符合 .net 格式規格，其副檔名允許將格式規範括在大括弧內。 例如，下列兩種格式相等：

&nbsp;&nbsp;`'year='yyyy'/month='MM` 和 `year={yyyy}/month={MM}`

根據預設，日期時間值會使用下列格式轉譯：

| 分割區函數    | 預設格式 |
|-----------------------|----------------|
| `startofyear`         | `yyyy`         |
| `startofmonth`        | `yyyy/MM`      |
| `startofweek`         | `yyyy/MM/dd`   |
| `startofday`          | `yyyy/MM/dd`   |
| `bin(`*列*`, 1d)` | `yyyy/MM/dd`   |
| `bin(`*列*`, 1h)` | `yyyy/MM/dd/HH` |
| `bin(`*列*`, 1m)` | `yyyy/MM/dd/HH/mm` |

如果外部資料表定義中省略了 *PathFormat* ，則會假設所有資料分割的順序與定義的順序完全相同，並使用分隔符號來分隔 `/` 。 使用其預設字串呈現方式來轉譯資料分割。

若要檢查路徑格式定義正確性，請 `sampleUris` 在建立外部資料表時使用屬性。

<a name="format"></a>
*格式*

資料格式，任何內嵌 [格式](../../ingestion-supported-formats.md)。

> [!NOTE]
> 使用 [匯出案例](data-export/export-data-to-an-external-table.md) 的外部資料表的格式限制如下： `CSV` 、 `TSV` `JSON` 和 `Parquet` 。

<a name="connection-string"></a>
*StorageConnectionString*

一或多個路徑，可 Azure Blob 儲存體 Blob 容器或 Azure Data Lake 儲存檔案系統 (虛擬目錄或資料夾) ，包括認證。
如需詳細資訊，請參閱 [儲存體連接字串](../api/connection-strings/storage.md) 。

> [!TIP]
> 提供一個以上的儲存體帳戶，以避免在將大量資料 [匯出](data-export/export-data-to-an-external-table.md) 至外部資料表時，進行儲存體節流。 匯出會在所有提供的帳戶之間散發寫入。 

<a name="properties"></a>
*選用屬性*

| 屬性         | 類型     | 說明       |
|------------------|----------|-------------------------------------------------------------------------------------|
| `folder`         | `string` | 資料表的資料夾                                                                     |
| `docString`      | `string` | 記錄資料表的字串                                                       |
| `compressed`     | `bool`   | 如果設定，則會指出檔案是否會壓縮為 `.gz` 僅 (用於 [匯出案例](data-export/export-data-to-an-external-table.md) 的檔案)  |
| `includeHeaders` | `string` | 若為分隔的文字格式 (CSV、TSV、... ) ，表示檔案是否包含標頭。 可能的值為： `All` (所有檔案包含標頭) 、 `FirstFile` 資料夾中 (第一個檔案包含標頭) ， `None` (沒有任何檔案包含標頭) 。 |
| `namePrefix`     | `string` | 如果設定，則表示檔案的前置詞。 在寫入作業中，會使用此前置詞來寫入所有檔案。 在讀取作業中，只會讀取具有這個前置詞的檔案。 |
| `fileExtension`  | `string` | 如果設定，表示檔案的副檔名。 寫入時，檔案名會以這個尾碼結尾。 讀取時，只會讀取具有此副檔名的檔案。           |
| `encoding`       | `string` | 指出文字的編碼方式： `UTF8NoBOM` (預設) 或 `UTF8BOM` 。             |
| `sampleUris`     | `bool`   | 如果設定，命令結果會提供外部資料表定義所預期的一些外部資料檔 URI 範例 (範例會在第二個結果資料表) 中傳回。 這個選項可協助驗證是否已正確定義分割 *[區和](#partitions)* *[PathFormat](#path-format)* 參數。 |
| `validateNotEmpty` | `bool`   | 如果設定，則會驗證連接字串中是否有內容。 如果指定的 URI 位置不存在，或者沒有足夠的許可權可以存取它，此命令將會失敗。 |

> [!TIP]
> 若要深入瞭解 `namePrefix` `fileExtension` 在查詢期間于資料檔案篩選中扮演的角色和屬性，請參閱檔案 [篩選邏輯](#file-filtering) 一節。
 
<a name="examples"></a>
**例子** 

非資料分割的外部資料表。 資料檔案應該會直接放在) 定義的容器 (：

```kusto
.create external table ExternalTable (x:long, s:string)  
kind=blob 
dataformat=csv 
( 
   h@'https://storageaccount.blob.core.windows.net/container1;secretKey' 
) 
```

依日期分割的外部資料表。 日期檔案應放置在預設日期時間格式的目錄中 `yyyy/MM/dd` ：

```kusto
.create external table ExternalTable (Timestamp:datetime, x:long, s:string) 
kind=adl
partition by (Date:datetime = bin(Timestamp, 1d)) 
dataformat=csv 
( 
   h@'abfss://filesystem@storageaccount.dfs.core.windows.net/path;secretKey'
)
```

依月份分割的外部資料表，目錄格式為 `year=yyyy/month=MM` ：

```kusto
.create external table ExternalTable (Timestamp:datetime, x:long, s:string) 
kind=blob 
partition by (Month:datetime = startofmonth(Timestamp)) 
pathformat = (datetime_pattern("'year='yyyy'/month='MM", Month)) 
dataformat=csv 
( 
   h@'https://storageaccount.blob.core.windows.net/container1;secretKey' 
) 
```

先依客戶名稱分割的外部資料表，再依日期分割。 例如，預期的目錄結構 `customer_name=Softworks/2019/02/01` 如下：

```kusto
.create external table ExternalTable (Timestamp:datetime, CustomerName:string) 
kind=blob 
partition by (CustomerNamePart:string = CustomerName, Date:datetime = startofday(Timestamp)) 
pathformat = ("customer_name=" CustomerNamePart "/" Date)
dataformat=csv 
(  
   h@'https://storageaccount.blob.core.windows.net/container1;secretKey' 
)
```

先依客戶名稱雜湊分割的外部資料表 (模數 10) ，然後依日期進行分割。 例如，預期的目錄結構為 `customer_id=5/dt=20190201` 。 資料檔案名稱結尾為 `.txt` 副檔名：

```kusto
.create external table ExternalTable (Timestamp:datetime, CustomerName:string) 
kind=blob 
partition by (CustomerId:long = hash(CustomerName, 10), Date:datetime = startofday(Timestamp)) 
pathformat = ("customer_id=" CustomerId "/dt=" datetime_pattern("yyyyMMdd", Date)) 
dataformat=csv 
( 
   h@'https://storageaccount.blob.core.windows.net/container1;secretKey'
)
with (fileExtension = ".txt")
```

**範例輸出**

|TableName|TableType|資料夾|DocString|屬性|ConnectionStrings|資料分割|PathFormat|
|---------|---------|------|---------|----------|-----------------|----------|----------|
|ExternalTable|Blob|ExternalTables|Docs|{"Format"： "Csv"，"壓縮"： false，"CompressionType"： null，"FileExtension"： null，"IncludeHeaders"： "None"，"Encoding"： null，"NamePrefix"： null}|["https://storageaccount.blob.core.windows.net/container1;\*\*\*\*\*\*\*"]|[{"Mod"：10，"Name"： "CustomerId"，"ColumnName"： "CustomerName"，"序數"： 0}，{"Function"： "StartOfDay"，"Name"： "Date"，"ColumnName"： "Timestamp"，"序數"： 1}]|"customer \_ id =" CustomerId "/dt =" datetime \_ 模式 ( "yyyyMMdd"，日期) |

<a name="virtual-columns"></a>
**虛擬資料行**

從 Spark 匯出資料時，資料框架寫入器的方法中所指定的資料分割資料行 (`partitionBy`) 不會寫入資料檔案中。 此程式可避免重復資料，因為資料已出現在「資料夾」名稱中。 例如， `column1=<value>/column2=<value>/` 和 Spark 可以在讀取時辨識。

外部資料表支援下列語法來指定虛擬資料行：

```kusto
.create external table ExternalTable (EventName:string, Revenue:double)  
kind=blob  
partition by (CustomerName:string, Date:datetime)  
pathformat = ("customer=" CustomerName "/date=" datetime_pattern("yyyyMMdd", Date))  
dataformat=parquet
( 
   h@'https://storageaccount.blob.core.windows.net/container1;secretKey'
)
```

<a name="file-filtering"></a>
**檔案篩選邏輯**

查詢外部資料表時，查詢引擎會篩選出不相關的外部儲存體檔案，藉以改善效能。 逐一查看檔案並決定是否應該處理檔案的程式如下所述。

1. 建立 URI 模式，表示找到檔案的位置。 一開始，URI 模式等於外部資料表定義中提供的連接字串。 如果有定義任何分割區，則會使用 *[PathFormat](#path-format)* 轉譯，然後附加至 URI 模式。

2. 針對在) 建立的 URI 模式 (下找到的所有檔案，請檢查：

   * 資料分割值符合查詢中所使用的述詞。
   * `NamePrefix`如果定義了這類屬性，Blob 名稱就會以開頭。
   * `FileExtension`如果定義了這類屬性，Blob 名稱會以結尾。

一旦符合所有條件之後，查詢引擎就會提取並處理檔案。

> [!NOTE]
> 初始 URI 模式是使用查詢述詞值所建立。 這最適合一組有限的字串值，也適用于封閉的時間範圍。

## <a name="show-external-table-artifacts"></a>。顯示外部資料表構件

傳回在查詢給定的外部資料表時，將會處理的所有檔案清單。

> [!NOTE]
> 作業需要 [資料庫使用者](../management/access-control/role-based-authorization.md)權力。

**語法：** 

`.show``external` `table` *TableName* `artifacts` [ `limit` *MaxResults*]

其中 *MaxResults* 是選擇性參數，可設定以限制結果數目。

**輸出**

| 輸出參數 | 類型   | 說明                       |
|------------------|--------|-----------------------------------|
| Uri              | 字串 | 外部儲存體資料檔案的 URI |
| 大小             | long   | 檔案長度（以位元組為單位）              |
| 資料分割        | 動態 | 描述分割區外部資料表之檔案分割區的動態物件 |

> [!TIP]
> 根據檔案數目，反覆運算外部資料表所參考的所有檔案可能相當昂貴。 `limit`如果您只想要查看一些 URI 範例，請務必使用參數。

**範例：**

```kusto
.show external table T artifacts
```

**輸出：**

| Uri                                                                     | 大小 | 資料分割 |
|-------------------------------------------------------------------------| ---- | --------- |
| `https://storageaccount.blob.core.windows.net/container1/folder/file.csv` | 10743 | `{}`   |


如果是資料分割資料表， `Partition` 資料行將包含已解壓縮的資料分割值：

**輸出：**

| Uri                                                                     | 大小 | 資料分割 |
|-------------------------------------------------------------------------| ---- | --------- |
| `https://storageaccount.blob.core.windows.net/container1/customer=john.doe/dt=20200101/file.csv` | 10743 | `{"Customer": "john.doe", "Date": "2020-01-01T00:00:00.0000000Z"}` |


## <a name="create-external-table-mapping"></a>. 建立外部資料表對應

`.create``external` `table` *ExternalTableName* `json` `mapping` *MappingName* *MappingInJsonFormat*

建立新的對應。 如需詳細資訊，請參閱 [資料](./mappings.md#json-mapping)對應。

**範例** 
 
```kusto
.create external table MyExternalTable json mapping "Mapping1" '[{"Column": "rownumber", "Properties": {"Path": "$.rownumber"}}, {"Column": "rowguid", "Properties": {"Path": "$.rowguid"}}]'
```

**範例輸出**

| Name     | 種類 | 對應                                                           |
|----------|------|-------------------------------------------------------------------|
| mapping1 | JSON | [{"ColumnName"： "rownumber"，"Properties"： {"Path"： "$. rownumber"}}，{"ColumnName"： "rowguid"，"Properties"： {"Path"： "$ rowguid"}}] |

## <a name="alter-external-table-mapping"></a>。 alter external table 對應

`.alter``external` `table` *ExternalTableName* `json` `mapping` *MappingName* *MappingInJsonFormat*

改變現有的對應。 
 
**範例** 
 
```kusto
.alter external table MyExternalTable json mapping "Mapping1" '[{"Column": "rownumber", "Properties": {"Path": "$.rownumber"}}, {"Column": "rowguid", "Properties": {"Path": "$.rowguid"}}]'
```

**範例輸出**

| Name     | 種類 | 對應                                                                |
|----------|------|------------------------------------------------------------------------|
| mapping1 | JSON | [{"ColumnName"： "rownumber"，"Properties"： {"Path"： "$. rownumber"}}，{"ColumnName"： "rowguid"，"Properties"： {"Path"： "$ rowguid"}}] |

## <a name="show-external-table-mappings"></a>。顯示外部資料表對應

`.show``external` `table` *ExternalTableName* `json` `mapping` *MappingName* 

`.show``external` `table` *ExternalTableName* ExternalTableName `json``mappings`

顯示 (全部或依名稱) 指定的對應。
 
**範例** 
 
```kusto
.show external table MyExternalTable json mapping "Mapping1" 

.show external table MyExternalTable json mappings 
```

**範例輸出**

| Name     | 種類 | 對應                                                                         |
|----------|------|---------------------------------------------------------------------------------|
| mapping1 | JSON | [{"ColumnName"： "rownumber"，"Properties"： {"Path"： "$. rownumber"}}，{"ColumnName"： "rowguid"，"Properties"： {"Path"： "$ rowguid"}}] |

## <a name="drop-external-table-mapping"></a>. drop external table mapping

`.drop``external` `table` *ExternalTableName* `json` `mapping` *MappingName* 

卸載資料庫的對應。
 
**範例** 
 
```kusto
.drop external table MyExternalTable json mapping "Mapping1" 
```
## <a name="next-steps"></a>後續步驟

* [外部資料表一般控制項命令](./external-table-commands.md)
* [建立和改變外部 SQL 資料表](external-sql-tables.md)