---
title: 在 Azure 儲存體或 Azure Data Lake 中建立和改變外部資料表-Azure 資料總管
description: 本文說明如何在 Azure 儲存體或 Azure Data Lake 中建立和改變外部資料表。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 2ef238d863f2f3fe181814ac14e3605de21a5aff
ms.sourcegitcommit: b4d6c615252e7c7d20fafd99c5501cb0e9e2085b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/26/2020
ms.locfileid: "83863365"
---
# <a name="create-and-alter-external-tables-in-azure-storage-or-azure-data-lake"></a>在 Azure 儲存體或 Azure Data Lake 中建立和改變外部資料表

下列命令描述如何建立外部資料表。 資料表可以位於 Azure Blob 儲存體、Azure Data Lake 存放區 Gen1 或 Azure Data Lake 存放區 Gen2 中。 
[儲存體連接字串](../api/connection-strings/storage.md)說明如何為每個選項建立連接字串。 

## <a name="create-or-alter-external-table"></a>。 create 或. alter external table

**語法**

（ `.create`  |  `.alter` ） `external` `table` *TableName* （*架構*）  
`kind` `=` (`blob` | `adl`)  
[ `partition` `by` *Partition* [ `,` ....]]  
`dataformat` `=` *格式*  
`(`  
*StorageConnectionString* [ `,` ...]  
`)`  
[ `with` `(` [ `docstring` `=` *檔*] [ `,` `folder` `=` *資料夾資料夾*]， *property_name* `=` *值* `,` ... `)` ]

在執行命令的資料庫中，建立或改變新的外部資料表。

**參數**

* *TableName* -外部資料表名稱。 必須遵循[機構名稱](../query/schema-entities/entity-names.md)的規則。 外部資料表不能與相同資料庫中的一般資料表具有相同的名稱。
* *架構*-格式為的外部資料架構： `ColumnName:ColumnType[, ColumnName:ColumnType ...]` 。 如果外部資料結構描述不明，請使用[infer_storage_schema](../query/inferstorageschemaplugin.md)外掛程式，它可以根據外部檔案內容推斷架構。
* *Partition* -一或多個資料分割定義（選擇性）。 請參閱下面的分割語法。
* *Format* -資料格式。 任何內嵌[格式](../../ingestion-supported-formats.md)都支援查詢。 使用[匯出案例](data-export/export-data-to-an-external-table.md)的外部資料表僅限於下列格式： `CSV` 、 `TSV` 、 `JSON` 、 `Parquet` 。
* *StorageConnectionString* -一或多個 Azure Blob 儲存體 Blob 容器或 Azure Data Lake 存放區檔案系統（虛擬目錄或資料夾）（包括認證）的路徑。 如需詳細資訊，請參閱[儲存體連接字串](../api/connection-strings/storage.md)。 提供多個單一儲存體帳戶，以避免在將大量資料[匯出](data-export/export-data-to-an-external-table.md)至外部資料表時進行儲存體節流。 匯出會在提供的所有帳戶之間散發寫入。 

**資料分割語法**

[ `format_datetime =` *DateTimePartitionFormat*] `bin(`*TimestampColumnName*、 *PartitionByTimeSpan*`)`  
|   
[*StringFormatPrefix*]*StringColumnName* [*StringFormatSuffix*]）

**資料分割參數**

* *DateTimePartitionFormat* -輸出路徑中所需目錄結構的格式（選擇性）。 如果已定義資料分割，且未指定格式，則預設值為 "yyyy/MM/dd/HH/MM"。 此格式是以 PartitionByTimeSpan 為基礎。 例如，如果您依1d 分割，結構將會是 "yyyy/MM/dd"。 如果您以1個 h 分割，結構將會是 "yyyy/MM/dd/HH"。
* *TimestampColumnName* -分割資料表的日期時間資料行。 匯出至外部資料表時，時間戳記資料行必須存在於外部資料表架構定義和匯出查詢的輸出中。
* *PartitionByTimeSpan* -用來分割的 Timespan 常值。
* *StringFormatPrefix* -將會在資料表值之前串連之成品路徑一部分的常數值字串常值（選擇性）。
* *StringFormatSuffix* -將成為成品路徑一部分的常數位串常值，會在資料表值之後串連（選擇性）。
* *StringColumnName* -分割資料表的字串資料行。 字串資料行必須存在於外部資料表架構定義中。

**選擇性屬性**：

| 屬性         | 類型     | 描述       |
|------------------|----------|-------------------------------------------------------------------------------------|
| `folder`         | `string` | 資料表的資料夾                                                                     |
| `docString`      | `string` | 記錄資料表的字串                                                       |
| `compressed`     | `bool`   | 如果設定，則表示 blob 是否 `.gz` 壓縮為檔案                  |
| `includeHeaders` | `string` | 針對 CSV 或 TSV blob，表示 blob 是否包含標頭                     |
| `namePrefix`     | `string` | 如果設定，則表示 blob 的前置詞。 在寫入作業中，所有 blob 都會以這個前置詞寫入。 讀取作業時，只會讀取具有此前置詞的 blob。 |
| `fileExtension`  | `string` | 如果設定，則表示 blob 的副檔名。 在寫入時，blob 名稱的結尾會是這個尾碼。 讀取時，只會讀取具有此副檔名的 blob。           |
| `encoding`       | `string` | 表示文字的編碼方式： `UTF8NoBOM` （預設）或 `UTF8BOM` 。             |

如需有關查詢中外部資料表參數的詳細資訊，請參閱成品[篩選邏輯](#artifact-filtering-logic)。

> [!NOTE]
> * 如果資料表存在， `.create` 命令將會失敗並產生錯誤。 使用 `.alter` 修改現有的資料表。 
> * 不支援改變外部 blob 資料表的架構、格式或資料分割定義。 

需要的[資料庫使用者許可權](../management/access-control/role-based-authorization.md) `.create` ，以及的[資料表管理員許可權](../management/access-control/role-based-authorization.md) `.alter` 。 
 
**範例** 

非資料分割的外部資料表。 所有成品都應該直接在定義的容器底下：

```kusto
.create external table ExternalBlob (x:long, s:string) 
kind=blob
dataformat=csv
( 
   h@'https://storageaccount.blob.core.windows.net/container1;secretKey'
)
with 
(
   docstring = "Docs",
   folder = "ExternalTables"
)  
```

依日期時間分割的外部資料表。 成品是在定義的路徑底下的 "yyyy/MM/dd" 格式目錄中：

```kusto
.create external table ExternalAdlGen2 (Timestamp:datetime, x:long, s:string) 
kind=adl
partition by bin(Timestamp, 1d)
dataformat=csv
( 
   h@'abfss://filesystem@storageaccount.dfs.core.windows.net/path;secretKey'
)
with 
(
   docstring = "Docs",
   folder = "ExternalTables"
)  
```

以 dateTime 分割的外部資料表，其目錄格式為 "year = yyyy/month = MM/day = dd"：

```kusto
.create external table ExternalPartitionedBlob (Timestamp:datetime, x:long, s:string) 
kind=blob
partition by format_datetime="'year='yyyy/'month='MM/'day='dd" bin(Timestamp, 1d)
dataformat=csv
( 
   h@'https://storageaccount.blob.core.windows.net/container1;secretKey'
)
with 
(
   docstring = "Docs",
   folder = "ExternalTables"
)
```

具有每月資料分割和目錄格式 "yyyy/MM" 的外部資料表：

```kusto
.create external table ExternalPartitionedBlob (Timestamp:datetime, x:long, s:string) 
kind=blob
partition by format_datetime="yyyy/MM" bin(Timestamp, 1d)
dataformat=csv
( 
   h@'https://storageaccount.blob.core.windows.net/container1;secretKey'
)
with 
(
   docstring = "Docs",
   folder = "ExternalTables"
)
```

具有兩個數據分割的外部資料表。 目錄結構是兩個數據分割的串連：格式化的 CustomerName，後面接著預設的日期時間格式。 例如，"CustomerName = softworks/2011/11/11"：

```kusto
.create external table ExternalMultiplePartitions (Timestamp:datetime, CustomerName:string) 
kind=blob
partition by 
   "CustomerName="CustomerName,
   bin(Timestamp, 1d)
dataformat=csv
( 
   h@'https://storageaccount.blob.core.windows.net/container1;secretKey'
)
with 
(
   docstring = "Docs",
   folder = "ExternalTables"   
)
```

**輸出**

|TableName|TableType|資料夾|DocString|屬性|ConnectionStrings|資料分割|
|---|---|---|---|---|---|---|
|ExternalMultiplePartitions|Blob|ExternalTables|文件|{"Format"： "Csv"，"壓縮"： false，"CompressionType"： null，"FileExtension"： "Csv"，"IncludeHeaders"： "None"，"Encoding"： null，"NamePrefix"： null}|["https://storageaccount.blob.core.windows.net/container1;*******"]}|[{"StringFormat"： "CustomerName = {0} "，"ColumnName"： "CustomerName"，"序數"： 0}，PartitionBy "：" 1.00：00： 00 "，" ColumnName "：" Timestamp "，" 序數 "： 1}]|

### <a name="artifact-filtering-logic"></a>成品篩選邏輯

在查詢外部資料表時，查詢引擎會藉由篩選掉不相關的外部儲存體成品（blob）來改善效能。 以下說明逐一查看 blob 並決定是否應該處理 blob 的程式。

1. 建立 URI 模式，代表找到 blob 的位置。 一開始，URI 模式等於外部資料表定義中提供的連接字串。 如果有定義任何資料分割，則會將它們附加至 URI 模式。
例如，如果連接字串為：， `https://storageaccount.blob.core.windows.net/container1` 且已定義 datetime partition： `partition by format_datetime="yyyy-MM-dd" bin(Timestamp, 1d)` ，則對應的 URI 模式會是： `https://storageaccount.blob.core.windows.net/container1/yyyy-MM-dd` ，而我們會在符合此模式的位置下尋找 blob。
如果已定義額外的字串分割 `"CustomerId" customerId` ，則對應的 URI 模式為： `https://storageaccount.blob.core.windows.net/container1/yyyy-MM-dd/CustomerId=*` 。

1. 針對在您已建立的 URI 模式下找到的所有*直接*blob，請檢查：

   * 資料分割值符合查詢中使用的述詞。
   * `NamePrefix`如果定義了這類屬性，Blob 名稱就會以為開頭。
   * `FileExtension`如果定義了這類屬性，Blob 名稱會以結尾。

一旦符合所有條件，查詢引擎就會提取並處理 blob。

### <a name="spark-virtual-columns-support"></a>Spark 虛擬資料行支援

從 Spark 匯出資料時，分割區資料行（在資料框架寫入器的方法中指定 `partitionBy` ）不會寫入資料檔案中。 此程式可避免資料重複，因為資料已出現在 "folder" 名稱中。 例如， `column1=<value>/column2=<value>/` 和 Spark 可以在讀取時辨識它。 不過，Kusto 會要求資料本身中必須有分割區資料行。 已規劃支援 Kusto 中的虛擬資料行。 在那之前，請使用下列因應措施：從 Spark 匯出資料時，在寫入資料框架之前，建立資料分割的所有資料行複本：

```kusto
df.withColumn("_a", $"a").withColumn("_b", $"b").write.partitionBy("_a", "_b").parquet("...")
```

在 Kusto 中定義外部資料表時，請指定分割區資料行，如下列範例所示：

```kusto
.create external table ExternalSparkTable(a:string, b:datetime) 
kind=blob
partition by 
   "_a="a,
   format_datetime="'_b='yyyyMMdd" bin(b, 1d)
dataformat=parquet
( 
   h@'https://storageaccount.blob.core.windows.net/container1;secretKey'
)
```

## <a name="show-external-table-artifacts"></a>：顯示外部資料表成品

* 傳回查詢給定外部資料表時將處理的所有成品清單。
* 需要[資料庫使用者](../management/access-control/role-based-authorization.md)權力。

**語法：** 

`.show` `external` `table` *TableName* `artifacts`

**輸出**

| 輸出參數 | 類型   | 描述                       |
|------------------|--------|-----------------------------------|
| Uri              | 字串 | 外部儲存體成品的 URI |

**範例：**

```kusto
.show external table T artifacts
```

**輸出**

| Uri                                                                     |
|-------------------------------------------------------------------------|
| `https://storageaccount.blob.core.windows.net/container1/folder/file.csv` |

## <a name="create-external-table-mapping"></a>。建立外部資料表對應

`.create``external` `table` *ExternalTableName* `json` `mapping` *MappingName* *MappingInJsonFormat*

建立新的對應。 如需詳細資訊，請參閱[資料](./mappings.md#json-mapping)對應。

**範例** 
 
```kusto
.create external table MyExternalTable JSON mapping "Mapping1" '[{ "column" : "rownumber", "datatype" : "int", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]'
```

**範例輸出**

| 名稱     | 種類 | 對應                                                           |
|----------|------|-------------------------------------------------------------------|
| mapping1 | JSON | [{"ColumnName"： "rownumber"，"ColumnType"： "int"，"Properties"： {"Path"： "$. rownumber"}}，{"ColumnName"： "rowguid"，"ColumnType"： ""，"Properties"： {"Path"： "$. rowguid"}}] |

## <a name="alter-external-table-mapping"></a>。 alter external table 對應

`.alter``external` `table` *ExternalTableName* `json` `mapping` *MappingName* *MappingInJsonFormat*

改變現有的對應。 
 
**範例** 
 
```kusto
.alter external table MyExternalTable JSON mapping "Mapping1" '[{ "column" : "rownumber", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]'
```

**範例輸出**

| 名稱     | 種類 | 對應                                                                |
|----------|------|------------------------------------------------------------------------|
| mapping1 | JSON | [{"ColumnName"： "rownumber"，"ColumnType"： ""，"Properties"： {"Path"： "$. rownumber"}}，{"ColumnName"： "rowguid"，"ColumnType"： ""，"Properties"： {"Path"： "$. rowguid"}}] |

## <a name="show-external-table-mappings"></a>：顯示外部資料表對應

`.show``external` `table` *ExternalTableName* `json` `mapping` *MappingName* 

`.show``external` `table` *ExternalTableName* ExternalTableName `json``mappings`

顯示對應（全部或依名稱指定的對應）。
 
**範例** 
 
```kusto
.show external table MyExternalTable JSON mapping "Mapping1" 

.show external table MyExternalTable JSON mappings 
```

**範例輸出**

| 名稱     | 種類 | 對應                                                                         |
|----------|------|---------------------------------------------------------------------------------|
| mapping1 | JSON | [{"ColumnName"： "rownumber"，"ColumnType"： ""，"Properties"： {"Path"： "$. rownumber"}}，{"ColumnName"： "rowguid"，"ColumnType"： ""，"Properties"： {"Path"： "$. rowguid"}}] |

## <a name="drop-external-table-mapping"></a>。卸載外部資料表對應

`.drop``external` `table` *ExternalTableName* `json` `mapping` *MappingName* 

卸載資料庫的對應。
 
**範例** 
 
```kusto
.drop external table MyExternalTable JSON mapping "Mapping1" 
```
## <a name="next-steps"></a>後續步驟

* [外部資料表一般控制命令](externaltables.md)
* [建立和改變外部 SQL 資料表](external-sql-tables.md)
