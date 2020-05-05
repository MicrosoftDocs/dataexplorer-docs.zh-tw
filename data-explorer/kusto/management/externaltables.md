---
title: 外部資料表管理-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 中的外部資料表管理資料總管。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: c52f0649531678e31310f5a1f4bfb97f99f15857
ms.sourcegitcommit: 4f68d6dbfa6463dbb284de0aa17fc193d529ce3a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/04/2020
ms.locfileid: "82741949"
---
# <a name="external-table-management"></a>外部資料表管理

如需外部資料表的總覽，請參閱[外部資料表](../query/schema-entities/externaltables.md)。 

## <a name="common-external-tables-control-commands"></a>一般外部資料表控制項命令

下列命令與_任何_外部資料表相關（任何類型）。

### <a name="show-external-tables"></a>：顯示外部資料表

* 傳回資料庫中的所有外部資料表（或特定的外部資料表）。
* 需要[資料庫監視器許可權](../management/access-control/role-based-authorization.md)。

**語法：** 

`.show` `external` `tables`

`.show``external` *TableName* TableName `table`

**輸出**

| 輸出參數 | 類型   | 描述                                                         |
|------------------|--------|---------------------------------------------------------------------|
| TableName        | 字串 | 外部資料表的名稱                                             |
| TableType        | 字串 | 外部資料表的類型                                              |
| 資料夾           | 字串 | 資料表的資料夾                                                     |
| DocString        | 字串 | 記錄資料表的字串                                       |
| 屬性       | 字串 | 資料表的 JSON 序列化屬性（特定于資料表類型） |


**範例：**

```kusto
.show external tables
.show external table T
```

| TableName | TableType | 資料夾         | DocString | 屬性 |
|-----------|-----------|----------------|-----------|------------|
| T         | Blob      | ExternalTables | Docs      | {}         |


### <a name="show-external-table-schema"></a>：顯示外部資料表架構

* 以 JSON 或 CSL 的形式傳回外部資料表的架構。 
* 需要[資料庫監視器許可權](../management/access-control/role-based-authorization.md)。

**語法：** 

`.show``external` `schema` *TableName* （`json`） `table` `as`  |  `csl`

`.show``external` *TableName* TableName `table``cslschema`

**輸出**

| 輸出參數 | 類型   | 描述                        |
|------------------|--------|------------------------------------|
| TableName        | 字串 | 外部資料表的名稱            |
| 結構描述           | 字串 | JSON 格式的資料表架構 |
| DatabaseName     | 字串 | 資料表的資料庫名稱             |
| 資料夾           | 字串 | 資料表的資料夾                    |
| DocString        | 字串 | 記錄資料表的字串      |

**範例：**

```kusto
.show external table T schema as JSON
```

```kusto
.show external table T schema as CSL
.show external table T cslschema
```

**輸出**

*json*

| TableName | 結構描述    | DatabaseName | 資料夾         | DocString |
|-----------|----------------------------------|--------------|----------------|-----------|
| T         | {"Name"： "ExternalBlob"，<br>"Folder"： "ExternalTables"，<br>"DocString"： "檔"，<br>"OrderedColumns"： [{"Name"： "x"，"Type"： "System.object"，"CslType"： "long"，"DocString"： ""}，{"Name"： "s"，"Type"： "system.string"，"CslType"： ""}]} | DB           | ExternalTables | Docs      |


*csl*

| TableName | 結構描述          | DatabaseName | 資料夾         | DocString |
|-----------|-----------------|--------------|----------------|-----------|
| T         | x:long,s:string | DB           | ExternalTables | Docs      |

### <a name="drop-external-table"></a>. drop external table

* 卸載外部資料表 
* 無法在此作業之後還原外部資料表定義
* 需要[資料庫管理員許可權](../management/access-control/role-based-authorization.md)。

**語法：**  

`.drop``external` *TableName* TableName `table`

**輸出**

傳回已卸載之資料表的屬性。 請參閱[。顯示外部資料表](#show-external-tables)。

**範例：**

```kusto
.drop external table ExternalBlob
```

| TableName | TableType | 資料夾         | DocString | 結構描述       | 屬性 |
|-----------|-----------|----------------|-----------|-----------------------------------------------------|------------|
| T         | Blob      | ExternalTables | Docs      | [{"Name"： "x"，"CslType"： "long"}，<br> {"Name"： "s"，"CslType"： "string"}] | {}         |

## <a name="external-tables-in-azure-storage-or-azure-data-lake"></a>Azure 儲存體或 Azure Data Lake 中的外部資料表

下列命令描述如何建立外部資料表。 資料表可以位於 Azure Blob 儲存體、Azure Data Lake 存放區 Gen1 或 Azure Data Lake 存放區 Gen2 中。 
[儲存體連接字串](../api/connection-strings/storage.md)說明如何為每個選項建立連接字串。 

### <a name="create-or-alter-external-table"></a>。 create 或. alter external table

**語法**

`.create`  | （`.alter`） `external` *TableName （* *架構*） `table`  
`kind` `=` (`blob` | `adl`)  
[`partition` `by` *Partition* [`,` ....]]  
`dataformat` `=` *[格式]*  
`(`  
*StorageConnectionString* [`,` ...]  
`)`  
[`with` `(`[`docstring` `folder` *Documentation* `,` `=` *value* *property_name* `,`檔] [ `=` *資料夾資料夾*]，property_name 值 ... `=``)`]

在執行命令的資料庫中，建立或改變新的外部資料表。

**參數**

* *TableName* -外部資料表名稱。 必須遵循[機構名稱](../query/schema-entities/entity-names.md)的規則。 外部資料表不能與相同資料庫中的一般資料表具有相同的名稱。
* *架構*-格式為的外部資料架構`ColumnName:ColumnType[, ColumnName:ColumnType ...]`：。 如果外部資料結構描述不明，請使用[infer_storage_schema](../query/inferstorageschemaplugin.md)外掛程式，它可以根據外部檔案內容推斷架構。
* *Partition* -一或多個資料分割定義（選擇性）。 請參閱下面的分割語法。
* *Format* -資料格式。 任何內嵌[格式](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats)都支援查詢。 使用[匯出案例](data-export/export-data-to-an-external-table.md)的外部資料表僅限於下列格式`CSV`：、 `TSV`、 `JSON`、。 `Parquet`
* *StorageConnectionString* -一或多個 Azure Blob 儲存體 Blob 容器或 Azure Data Lake 存放區檔案系統（虛擬目錄或資料夾）（包括認證）的路徑。 如需詳細資訊，請參閱[儲存體連接字串](../api/connection-strings/storage.md)。 強烈建議您提供一個以上的儲存體帳戶，以避免在將大量資料[匯出](data-export/export-data-to-an-external-table.md)至外部資料表時進行儲存節流。 匯出會在提供的所有帳戶之間散發寫入。 

**資料分割語法**

[`format_datetime =` *DateTimePartitionFormat*]`bin(` *TimestampColumnName*、 *PartitionByTimeSpan*`)`  
|   
[*StringFormatPrefix*]*StringColumnName* [*StringFormatSuffix*]）

**資料分割參數**

* *DateTimePartitionFormat* -輸出路徑中所需目錄結構的格式（選擇性）。 如果已定義資料分割，且未指定格式，則預設值為 "yyyy/MM/dd/HH/mm" （根據 PartitionByTimeSpan）。 例如，如果您依1d 分割，結構將會是 "yyyy/MM/dd"。 如果您依1h 分割，結構將會是 "yyyy/MM/dd/HH"。
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
| `compressed`     | `bool`   | 如果設定，則表示 blob 是否壓縮為`.gz`檔案                  |
| `includeHeaders` | `string` | 針對 CSV 或 TSV blob，表示 blob 是否包含標頭                     |
| `namePrefix`     | `string` | 如果設定，則表示 blob 的前置詞。 在寫入作業中，所有 blob 都會以這個前置詞寫入。 讀取作業時，只會讀取具有此前置詞的 blob。 |
| `fileExtension`  | `string` | 如果設定，則表示 blob 的副檔名。 在寫入時，blob 名稱的結尾會是這個尾碼。 讀取時，只會讀取具有此副檔名的 blob。           |
| `encoding`       | `string` | 表示文字的編碼方式： `UTF8NoBOM` （預設）或。 `UTF8BOM`             |

如需有關查詢中外部資料表參數的詳細資訊，請參閱成品[篩選邏輯](#artifact-filtering-logic)。

> [!NOTE]
> * 如果資料表存在， `.create`命令將會失敗並產生錯誤。 使用`.alter`修改現有的資料表。 
> * 不支援改變外部 blob 資料表的架構、格式或資料分割定義。 

需要的`.create` [資料庫使用者許可權](../management/access-control/role-based-authorization.md)，以及的`.alter`[資料表管理員許可權](../management/access-control/role-based-authorization.md)。 
 
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

依日期時間分割的外部資料表。 成品是以 "yyyy/MM/dd" 格式的目錄存放在定義的路徑底下：

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

具有兩個數據分割的外部資料表。 目錄結構是兩個數據分割的串連：格式化的 CustomerName，後面接著預設的日期時間格式。 例如 "CustomerName = softworks/2011/11/11"：

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
|ExternalMultiplePartitions|Blob|ExternalTables|Docs|{"Format"： "Csv"，"壓縮"： false，"CompressionType"： null，"FileExtension"： "Csv"，"IncludeHeaders"： "None"，"Encoding"： null，"NamePrefix"： null}|["https://storageaccount.blob.core.windows.net/container1;*******"]}|[{"StringFormat"： "CustomerName ={0}"，"ColumnName"： "CustomerName"，"序數"： 0}，PartitionBy "：" 1.00：00： 00 "，" ColumnName "：" Timestamp "，" 序數 "： 1}]|

#### <a name="artifact-filtering-logic"></a>成品篩選邏輯

在查詢外部資料表時，查詢引擎會篩選掉不相關的外部儲存體成品（blob），以改善查詢效能。 下列程式會逐一查看 blob，並決定是否應該處理 blob。

1. 建立 URI 模式，代表找到 blob 的位置。 一開始，URI 模式等於外部資料表定義中提供的連接字串。 如果有定義任何資料分割，則會將它們附加至 URI 模式。
例如，如果連接字串為： `https://storageaccount.blob.core.windows.net/container1` ，且已定義 datetime partition： `partition by format_datetime="yyyy-MM-dd" bin(Timestamp, 1d)`，則對應的 URI 模式會是： `https://storageaccount.blob.core.windows.net/container1/yyyy-MM-dd`，而我們會在符合此模式的位置下尋找 blob。
如果已定義額外的字串分割`"CustomerId" customerId` ，則對應的 URI 模式為： `https://storageaccount.blob.core.windows.net/container1/yyyy-MM-dd/CustomerId=*`等等。

2. 針對在您已建立的 URI 模式下找到的所有*直接*blob，請檢查：

 * 資料分割值符合查詢中使用的述詞。
 * 如果定義了這`NamePrefix`類屬性，Blob 名稱就會以為開頭。
 * 如果定義了這`FileExtension`類屬性，Blob 名稱會以結尾。

一旦符合所有條件，查詢引擎就會提取並處理 blob。

#### <a name="spark-virtual-columns-support"></a>Spark 虛擬資料行支援

從 Spark 匯出資料時，分割區資料行（在資料框架寫入器的`partitionBy`方法中指定）不會寫入資料檔案中。 這是為了避免資料重複，因為資料已出現在「資料夾」名稱（例如`column1=<value>/column2=<value>/`）中，而 Spark 則可以在讀取時辨識。 不過，Kusto 會要求資料本身中必須有分割區資料行。 已規劃支援 Kusto 中的虛擬資料行。 在那之前，請使用下列因應措施：從 Spark 匯出資料時，在寫入資料框架之前，建立資料分割的所有資料行複本：

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

### <a name="show-external-table-artifacts"></a>：顯示外部資料表成品

* 傳回查詢給定外部資料表時將處理的所有成品清單。
* 需要[資料庫使用者](../management/access-control/role-based-authorization.md)權力。

**語法：** 

`.show``external` *TableName* TableName `table``artifacts`

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

### <a name="create-external-table-mapping"></a>。建立外部資料表對應

`.create``external` `mapping` *MappingName* *MappingInJsonFormat* *ExternalTableName* ExternalTableName `json` MappingInJsonFormat `table`

建立新的對應。 如需詳細資訊，請參閱[資料](./mappings.md#json-mapping)對應。

**範例** 
 
```kusto
.create external table MyExternalTable JSON mapping "Mapping1" '[{ "column" : "rownumber", "datatype" : "int", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]'
```

**範例輸出**

| 名稱     | 種類 | 對應                                                           |
|----------|------|-------------------------------------------------------------------|
| mapping1 | JSON | [{"ColumnName"： "rownumber"，"ColumnType"： "int"，"Properties"： {"Path"： "$. rownumber"}}，{"ColumnName"： "rowguid"，"ColumnType"： ""，"Properties"： {"Path"： "$. rowguid"}}] |

### <a name="alter-external-table-mapping"></a>。 alter external table 對應

`.alter``external` `mapping` *MappingName* *MappingInJsonFormat* *ExternalTableName* ExternalTableName `json` MappingInJsonFormat `table`

改變現有的對應。 
 
**範例** 
 
```kusto
.alter external table MyExternalTable JSON mapping "Mapping1" '[{ "column" : "rownumber", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]'
```

**範例輸出**

| 名稱     | 種類 | 對應                                                                |
|----------|------|------------------------------------------------------------------------|
| mapping1 | JSON | [{"ColumnName"： "rownumber"，"ColumnType"： ""，"Properties"： {"Path"： "$. rownumber"}}，{"ColumnName"： "rowguid"，"ColumnType"： ""，"Properties"： {"Path"： "$. rowguid"}}] |

### <a name="show-external-table-mappings"></a>：顯示外部資料表對應

`.show``external` `mapping` *MappingName* *ExternalTableName* ExternalTableName MappingName `table` `json` 

`.show``external` `json` *ExternalTableName* ExternalTableName `table``mappings`

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

### <a name="drop-external-table-mapping"></a>。卸載外部資料表對應

`.drop``external` `mapping` *MappingName* *ExternalTableName* ExternalTableName MappingName `table` `json` 

卸載資料庫的對應。
 
**範例** 
 
```kusto
.drop external table MyExternalTable JSON mapping "Mapping1" 
```

## <a name="external-sql-table"></a>外部 SQL 資料表

### <a name="create-or-alter-external-sql-table"></a>。建立或改變外部 sql 資料表

在執行命令的資料庫中，建立或改變外部 SQL 資料表。  

**語法**

`.create`  | （`.alter`） `external` TableName （[columnName： columnType]，...） *TableName* `table`  
`kind` `=` `sql`  
`table``=` *SqlTableName*  
`(`*SqlServerConnectionString*`)`  
[`with` `(`[`docstring` `folder` *Documentation* `,` `=` *value* *property_name* `,`檔] [ `=` *資料夾資料夾*]，property_name 值 ... `=``)`]


**參數**

* *TableName* -外部資料表名稱。 必須遵循[機構名稱](../query/schema-entities/entity-names.md)的規則。 外部資料表不能與相同資料庫中的一般資料表具有相同的名稱。
* *SqlTableName* -SQL 資料表的名稱。
* *SqlServerConnectionString* -SQL server 的連接字串。 可以是下列其中一項： 
    * **Aad 整合**式驗證`Authentication="Active Directory Integrated"`（）：使用者或應用程式會透過 AAD 向 Kusto 進行驗證，然後使用相同的權杖來存取 SQL Server 網路端點。
    * 使用者**名稱/密碼驗證**（`User ID=...; Password=...;`）。 如果外部資料表用於[連續匯出](data-export/continuous-data-export.md)，則必須使用此方法來執行驗證。 

> [!WARNING]
> 包含機密資訊的連接字串和查詢應該進行模糊處理，以便從任何 Kusto 追蹤中省略它們。 如需詳細資訊，請參閱[模糊字串常](../query/scalar-data-types/string.md#obfuscated-string-literals)值。

**選擇性屬性**
| 屬性            | 類型            | 描述                          |
|---------------------|-----------------|---------------------------------------------------------------------------------------------------|
| `folder`            | `string`        | 資料表的資料夾。                  |
| `docString`         | `string`        | 記錄資料表的字串。      |
| `firetriggers`      | `true`/`false`  | 如果`true`為，則指示目標系統引發 SQL 資料表上定義的 INSERT 觸發程式。 預設值為 `false`。 （如需詳細資訊，請參閱[BULK INSERT](https://msdn.microsoft.com/library/ms188365.aspx)和[SqlClient. SqlBulkCopy）。](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy(v=vs.110).aspx) |
| `createifnotexists` | `true`/ `false` | 如果`true`為，則會建立目標 SQL 資料表（如果尚未存在的話）。在`primarykey`此情況下，必須提供屬性，以指出做為主要索引鍵的結果資料行。 預設值為 `false`。  |
| `primarykey`        | `string`        | 如果`createifnotexists`為`true`，則表示結果中的資料行名稱，如果此命令是由這個命令所建立，則會用來當做 SQL 資料表的主鍵。                  |

> [!NOTE]
> * 如果資料表存在，此`.create`命令將會失敗並產生錯誤。 使用`.alter`修改現有的資料表。 
> * 不支援改變外部 SQL 資料表的架構或格式。 

需要的`.create` [資料庫使用者許可權](../management/access-control/role-based-authorization.md)，以及的`.alter`[資料表管理員許可權](../management/access-control/role-based-authorization.md)。 
 
**範例** 

```kusto
.create external table ExternalSql (x:long, s:string) 
kind=sql
table=MySqlTable
( 
   h@'Server=tcp:myserver.database.windows.net,1433;Authentication=Active Directory Integrated;Initial Catalog=mydatabase;'
)
with 
(
   docstring = "Docs",
   folder = "ExternalTables", 
   createifnotexists = true,
   primarykey = x,
   firetriggers=true
)  
```

**輸出**

| TableName   | TableType | 資料夾         | DocString | 屬性                            |
|-------------|-----------|----------------|-----------|---------------------------------------|
| ExternalSql | Sql       | ExternalTables | Docs      | {<br>  "TargetEntityKind": "sqltable",<br>  "TargetEntityName": "MySqlTable",<br>  "TargetEntityConnectionString"： "Server = tcp:myserver. net，1433;Authentication = Active Directory 整合; 初始目錄 = mydatabase; "，<br>  "FireTriggers"： true，<br>  "CreateIfNotExists"： true，<br>  "PrimaryKey"： "x"<br>} |

### <a name="querying-an-external-table-of-type-sql"></a>查詢 SQL 類型的外部資料表 
Similarl 至其他類型的外部資料表時，支援查詢外部 SQL 資料表。 請參閱[查詢外部資料表](https://docs.microsoft.com/azure/data-explorer/data-lake-query-data)。 請注意，SQL 外部資料表查詢實行會從 SQL 資料表執行完整的「選取 *」（或選取相關的資料行），而查詢的其餘部分則會在 Kusto 端執行。 請考慮下列外部資料表查詢： 

```kusto
external_table('MySqlExternalTable') | count
```

Kusto 會對 SQL 資料庫執行 ' SELECT * from TABLE ' 查詢，後面接著 Kusto 端的計數。 在這種情況下，如果直接以 T-sql （[SELECT COUNT （1） FROM TABLE]）撰寫，並使用[sql_request 外掛程式](../query/sqlrequestplugin.md)執行，而不是使用外部資料表函數，效能就會更好。 同樣地，篩選器也不會推送至 SQL 查詢。  
建議在查詢需要讀取整個資料表（或相關的資料行）以在 Kusto 端進一步執行時，使用外部資料表來查詢 SQL 資料表。 當 SQL 查詢可以在 T-sql 中大幅優化時，請使用[sql_request 外掛程式](../query/sqlrequestplugin.md)。
