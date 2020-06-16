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
ms.openlocfilehash: 7bcba1cbcbcbd712278696d897febaee5714703f
ms.sourcegitcommit: 8e097319ea989661e1958efaa1586459d2b69292
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/15/2020
ms.locfileid: "84780587"
---
# <a name="create-and-alter-external-tables-in-azure-storage-or-azure-data-lake"></a>建立和改變 Azure 儲存體或 Azure Data Lake 中的外部資料表

下列命令說明如何建立位於 Azure Blob 儲存體、Azure Data Lake 存放區 Gen1 或 Azure Data Lake 存放區 Gen2 中的外部資料表。 

## <a name="create-or-alter-external-table"></a>。 create 或. alter external table

**語法**

（ `.create`  |  `.alter` ） `external` `table` *[TableName](#table-name)* `(` *[架構](#schema)*`)`  
`kind` `=` (`blob` | `adl`)  
[資料分割 `partition` `by` `(` *[Partitions](#partitions)* `)` [ `pathformat` `=` `(` *[PathFormat](#path-format)* `)` ]]  
`dataformat``=` *[格式](#format)*  
`(`*[StorageConnectionString](#connection-string)* [ `,` ...]`)`   
[ `with` `(` *[PropertyName](#properties)* `=` *[值](#properties)* `,` ... `)` ]  

在執行命令的資料庫中，建立或改變新的外部資料表。

> [!NOTE]
> * 如果資料表存在， `.create` 命令將會失敗並產生錯誤。 使用 `.alter` 修改現有的資料表。 
> * 不支援改變外部 blob 資料表的架構、格式或資料分割定義。 
> * 作業需要的[資料庫使用者許可權](../management/access-control/role-based-authorization.md) `.create` 和的[資料表管理員許可權](../management/access-control/role-based-authorization.md) `.alter` 。 

**參數**

<a name="table-name"></a>
*TableName*

遵守[機構名稱](../query/schema-entities/entity-names.md)規則的外部資料表名稱。
外部資料表不能與相同資料庫中的一般資料表具有相同的名稱。

<a name="schema"></a>
*Schema*

外部資料結構描述使用下列格式：

&nbsp;&nbsp;*ColumnName* `:`*ColumnType* [ `,` *ColumnName* `:` *ColumnType* ...]

其中， *ColumnName*會遵循[實體命名](../query/schema-entities/entity-names.md)規則，而*ColumnType*是其中一種[支援的資料類型](../query/scalar-data-types/index.md)。

> [!TIP]
> 如果外部資料架構不明，請使用[推斷 \_ 儲存 \_ 架構](../query/inferstorageschemaplugin.md)外掛程式，這有助於根據外部檔案內容推斷架構。

<a name="partitions"></a>
*資料分割*

用來分割外部資料表的資料行清單（以逗號分隔）。 分割區資料行可存在於資料檔案本身，或檔案路徑的 sa 部分（深入瞭解[虛擬資料行](#virtual-columns)）。

資料分割清單是資料分割資料行的任意組合，使用下列其中一種格式來指定：

* 分割區，代表[虛擬資料行](#virtual-columns)。

  *PartitionName* `:`(`datetime` | `string`)

* 以字串資料行值為基礎的資料分割。

  *PartitionName* `:``string` `=` *ColumnName*

* 分割區，以字串資料行值[hash](../query/hashfunction.md)，模數*數位*為基礎。

  *PartitionName* `:``long` `=` `hash``(` *ColumnName* `,` *數目*`)`

* 資料分割，以日期時間資料行的截斷值為基礎。 請參閱[startofyear](../query/startofyearfunction.md)、 [startofmonth](../query/startofmonthfunction.md)、 [startofweek](../query/startofweekfunction.md)、 [startofday](../query/startofdayfunction.md)或[bin](../query/binfunction.md)函數的相關檔。

  *PartitionName* `:``datetime` `=` （ `startofyear` \| `startofmonth` \| `startofweek` \| `startofday` ） `(` *ColumnName*`)`  
  *PartitionName* `:``datetime` `=` `bin``(` *ColumnName* `,` *TimeSpan*`)`


<a name="path-format"></a>
*PathFormat*

外部資料 URI 檔案路徑格式，除了分割區之外，還可以指定。 路徑格式是一系列的資料分割元素和文字分隔符號：

&nbsp;&nbsp;[*StringSeparator*]*Partition* [*StringSeparator*] [*partition* [*StringSeparator*] ...]  

其中*partition*指的是在子句中宣告的資料分割 `partition` `by` ，而*StringSeparator*是以引號括住的任何文字。

您可以使用轉譯為字串的分割區元素，並以對應的文字分隔符號來建立原始檔案路徑前置詞。 若要指定用來轉譯 datetime 資料分割值的格式，可以使用下列宏：

&nbsp;&nbsp;`datetime_pattern``(` *DateTimeFormat* `,` *PartitionName*`)`  

其中*DateTimeFormat*會遵循 .net 格式規格，並允許將格式規範括在大括弧中。 例如，下列兩種格式是相等的：

&nbsp;&nbsp;`'year='yyyy'/month='MM`和`year={yyyy}/month={MM}`

根據預設，datetime 值會使用下列格式來轉譯：

| 分割區函數    | 預設格式 |
|-----------------------|----------------|
| `startofyear`         | `yyyy`         |
| `startofmonth`        | `yyyy/MM`      |
| `startofweek`         | `yyyy/MM/dd`   |
| `startofday`          | `yyyy/MM/dd`   |
| `bin(`*排*`, 1d)` | `yyyy/MM/dd`   |
| `bin(`*排*`, 1h)` | `yyyy/MM/dd/HH` |
| `bin(`*排*`, 1m)` | `yyyy/MM/dd/HH/mm` |

如果外部資料表定義中省略了*PathFormat* ，則會假設使用分隔符號來分隔所有分割區（以其定義的完全相同的順序） `/` 。 資料分割會使用其預設字串呈現來轉譯。

<a name="format"></a>
*編排*

資料格式，任何內嵌[格式](../../ingestion-supported-formats.md)。

> [!NOTE]
> 使用[匯出案例](data-export/export-data-to-an-external-table.md)的外部資料表僅限於下列格式： `CSV` 、 `TSV` `JSON` 和 `Parquet` 。

<a name="connection-string"></a>
*StorageConnectionString*

Azure Blob 儲存體 Blob 容器或 Azure Data Lake 存放區檔案系統（虛擬目錄或資料夾）（包括認證）的一或多個路徑。
如需詳細資訊，請參閱[儲存體連接字串](../api/connection-strings/storage.md)。

> [!TIP]
> 提供多個單一儲存體帳戶，以避免在將大量資料[匯出](data-export/export-data-to-an-external-table.md)至外部資料表時進行儲存體節流。 匯出會在提供的所有帳戶之間散發寫入。 

<a name="properties"></a>
*選擇性屬性*

| 屬性         | 類型     | 描述       |
|------------------|----------|-------------------------------------------------------------------------------------|
| `folder`         | `string` | 資料表的資料夾                                                                     |
| `docString`      | `string` | 記錄資料表的字串                                                       |
| `compressed`     | `bool`   | 如果設定，則指出檔案是否壓縮成檔案 `.gz` （僅用於[匯出案例](data-export/export-data-to-an-external-table.md)） |
| `includeHeaders` | `string` | 針對 CSV 或 TSV 檔案，指出檔案是否包含標頭                     |
| `namePrefix`     | `string` | 如果設定，則表示檔案的前置詞。 在寫入作業中，所有檔案都會以這個前置詞寫入。 讀取作業時，只會讀取具有這個前置詞的檔案。 |
| `fileExtension`  | `string` | 如果設定，則表示檔案的副檔名。 在寫入時，檔案名的結尾會是這個尾碼。 讀取時，只會讀取具有此副檔名的檔案。           |
| `encoding`       | `string` | 表示文字的編碼方式： `UTF8NoBOM` （預設）或 `UTF8BOM` 。             |

> [!TIP]
> 若要深入瞭解在 `namePrefix` `fileExtension` 查詢期間于資料檔案篩選中扮演的角色和屬性，請參閱檔案[篩選邏輯](#file-filtering)一節。
 
<a name="examples"></a>
**典型** 

非資料分割的外部資料表。 資料檔案應直接放在定義的容器底下：

```kusto
.create external table ExternalTable (x:long, s:string)  
kind=blob 
dataformat=csv 
( 
   h@'https://storageaccount.blob.core.windows.net/container1;secretKey' 
) 
```

依日期分割的外部資料表。 日期檔案應該放在預設日期時間格式的目錄中 `yyyy/MM/dd` ：

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

先依客戶名稱分割的外部資料表，再按日期。 預期的目錄結構為，例如 `customer_name=Softworks/2019/02/01` ：

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

外部資料表會先依客戶名稱雜湊（模數10）進行分割，然後再依日期進行分割。 預期的目錄結構為，例如 `customer_id=5/dt=20190201` 。 資料檔案名稱的結尾都是 `.txt` 副檔名：

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
|ExternalTable|Blob|ExternalTables|Docs|{"Format"： "Csv"，"壓縮"： false，"CompressionType"： null，"FileExtension"： null，"IncludeHeaders"： "None"，"Encoding"： null，"NamePrefix"： null}|["https://storageaccount.blob.core.windows.net/container1;\*\*\*\*\*\*\*"]|[{"Mod"：10，"Name"： "CustomerId"，"ColumnName"： "CustomerName"，"序數"： 0}，{"Function"： "StartOfDay"，"Name"： "Date"，"ColumnName"： "Timestamp"，"序數"： 1}]|"customer \_ id =" CustomerId "/dt =" 日期時間 \_ 模式（"yyyyMMdd"，日期）|

<a name="virtual-columns"></a>
**虛擬資料行**

從 Spark 匯出資料時，分割區資料行（在資料框架寫入器的方法中指定 `partitionBy` ）不會寫入資料檔案中。 此程式可避免資料重複，因為資料已出現在 "folder" 名稱中。 例如， `column1=<value>/column2=<value>/` 和 Spark 可以在讀取時辨識它。

外部資料表支援下列指定虛擬資料行的語法：

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

在查詢外部資料表時，查詢引擎會藉由篩選掉不相關的外部儲存體檔案來改善效能。 以下說明逐一查看檔案和決定是否應處理檔案的程式。

1. 建立 URI 模式，代表找到檔案的位置。 一開始，URI 模式等於外部資料表定義中提供的連接字串。 如果有定義任何資料分割，則會使用*[PathFormat](#path-format)* 來轉譯資料分割，然後將其附加至 URI 模式。

2. 針對在建立的 URI 模式下找到的所有檔案，檢查：

   * 資料分割值符合查詢中使用的述詞。
   * `NamePrefix`如果定義了這類屬性，Blob 名稱就會以為開頭。
   * `FileExtension`如果定義了這類屬性，Blob 名稱會以結尾。

一旦符合所有條件之後，查詢引擎就會提取並處理該檔案。

> [!NOTE]
> 初始 URI 模式是使用查詢述詞值所建立。 這最適合用於一組有限的字串值，也適用于已關閉的時間範圍。 

## <a name="show-external-table-artifacts"></a>：顯示外部資料表成品

傳回查詢給定外部資料表時將處理的所有檔案清單。

> [!NOTE]
> 作業需要[資料庫使用者](../management/access-control/role-based-authorization.md)權力。

**語法：** 

`.show``external` `table` *TableName* `artifacts` [ `limit` *MaxResults*]

其中*MaxResults*是選擇性參數，可以設定來限制結果的數目。

**輸出**

| 輸出參數 | 類型   | 描述                       |
|------------------|--------|-----------------------------------|
| Uri              | 字串 | 外部儲存體資料檔案的 URI |

> [!TIP]
> 視檔案數目而定，逐一查看外部資料表所參考的所有檔案可能會相當耗費成本。 `limit`如果您只想要查看一些 URI 範例，請務必使用參數。

**範例：**

```kusto
.show external table T artifacts
```

**輸出：**

| Uri                                                                     |
|-------------------------------------------------------------------------|
| `https://storageaccount.blob.core.windows.net/container1/folder/file.csv` |

## <a name="create-external-table-mapping"></a>。建立外部資料表對應

`.create``external` `table` *ExternalTableName* `json` `mapping` *MappingName* *MappingInJsonFormat*

建立新的對應。 如需詳細資訊，請參閱[資料](./mappings.md#json-mapping)對應。

**範例** 
 
```kusto
.create external table MyExternalTable json mapping "Mapping1" '[{"Column": "rownumber", "Properties": {"Path": "$.rownumber"}}, {"Column": "rowguid", "Properties": {"Path": "$.rowguid"}}]'
```

**範例輸出**

| 名稱     | 種類 | 對應                                                           |
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

| 名稱     | 種類 | 對應                                                                |
|----------|------|------------------------------------------------------------------------|
| mapping1 | JSON | [{"ColumnName"： "rownumber"，"Properties"： {"Path"： "$. rownumber"}}，{"ColumnName"： "rowguid"，"Properties"： {"Path"： "$ rowguid"}}] |

## <a name="show-external-table-mappings"></a>：顯示外部資料表對應

`.show``external` `table` *ExternalTableName* `json` `mapping` *MappingName* 

`.show``external` `table` *ExternalTableName* ExternalTableName `json``mappings`

顯示對應（全部或依名稱指定的對應）。
 
**範例** 
 
```kusto
.show external table MyExternalTable json mapping "Mapping1" 

.show external table MyExternalTable json mappings 
```

**範例輸出**

| 名稱     | 種類 | 對應                                                                         |
|----------|------|---------------------------------------------------------------------------------|
| mapping1 | JSON | [{"ColumnName"： "rownumber"，"Properties"： {"Path"： "$. rownumber"}}，{"ColumnName"： "rowguid"，"Properties"： {"Path"： "$ rowguid"}}] |

## <a name="drop-external-table-mapping"></a>。卸載外部資料表對應

`.drop``external` `table` *ExternalTableName* `json` `mapping` *MappingName* 

卸載資料庫的對應。
 
**範例** 
 
```kusto
.drop external table MyExternalTable json mapping "Mapping1" 
```
## <a name="next-steps"></a>後續步驟

* [外部資料表一般控制命令](externaltables.md)
* [建立和改變外部 SQL 資料表](external-sql-tables.md)
