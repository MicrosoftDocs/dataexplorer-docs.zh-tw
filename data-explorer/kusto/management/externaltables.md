---
title: 外部表管理 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的外部表管理。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 680a4e25d6b478fe171aa3296de81c0106417877
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744723"
---
# <a name="external-table-management"></a>外部表管理

有關外部表的概述,請參閱[外部表](../query/schema-entities/externaltables.md)。 

## <a name="common-external-tables-control-commands"></a>常見的外部表控制命令

以下_命令與任何外部_表(任何類型的)相關。

### <a name="show-external-tables"></a>.顯示外部表

* 返回資料庫中的所有外部表(或特定的外部表)。
* 需要[資料庫監控權限](../management/access-control/role-based-authorization.md)。

**語法：** 

`.show` `external` `tables`

`.show``external`*名稱*`table`

**輸出**

| 輸出參數 | 類型   | 描述                                                         |
|------------------|--------|---------------------------------------------------------------------|
| TableName        | 字串 | 外部表的名稱                                             |
| TableType        | 字串 | 外部表的類型                                              |
| 資料夾           | 字串 | 表的資料夾                                                     |
| 文件字串        | 字串 | 記錄表的字串                                       |
| 屬性       | 字串 | 表的 JSON 序列化屬性(特定於表的類型) |


**範例：**

```kusto
.show external tables
.show external table T
```

| TableName | TableType | 資料夾         | 文件字串 | 屬性 |
|-----------|-----------|----------------|-----------|------------|
| T         | Blob      | 外部表 | Docs      | {}         |


### <a name="show-external-table-schema"></a>.顯示外部表架構

* 返回外部表的架構,如 JSON 或 CSL。 
* 需要[資料庫監控權限](../management/access-control/role-based-authorization.md)。

**語法：** 

`.show``external``csl``table`表名 | ( ) `as` * * `json` `schema`

`.show``external`*名稱*`table``cslschema`

**輸出**

| 輸出參數 | 類型   | 描述                        |
|------------------|--------|------------------------------------|
| TableName        | 字串 | 外部表的名稱            |
| 結構描述           | 字串 | JSON 格式的表架構 |
| DatabaseName     | 字串 | 表的資料庫名稱             |
| 資料夾           | 字串 | 表的資料夾                    |
| 文件字串        | 字串 | 記錄表的字串      |

**範例：**

```kusto
.show external table T schema as JSON
```

```kusto
.show external table T schema as CSL
.show external table T cslschema
```

**輸出:**

*Json:*

| TableName | 結構描述    | DatabaseName | 資料夾         | 文件字串 |
|-----------|----------------------------------|--------------|----------------|-----------|
| T         | {"名稱":"外部 Blob",<br>"資料夾":"外部表",<br>"文件字串":"文檔",<br>"有序列隊":{"名稱":"x","鍵入":"系統.int64","CslType":"長","DocString":","\"名稱":"s","鍵入":"系統.String","CslType":"字串串","DocString":"\"\"\""\\"\"\""\""*" | DB           | 外部表 | Docs      |


*Csl:*

| TableName | 結構描述          | DatabaseName | 資料夾         | 文件字串 |
|-----------|-----------------|--------------|----------------|-----------|
| T         | x:長,s:字串 | DB           | 外部表 | Docs      |

### <a name="drop-external-table"></a>.刪除外部表

* 移除外部表 
* 此動作後無法回復外部表定義
* 需要[資料庫管理權限](../management/access-control/role-based-authorization.md)。

**語法：**  

`.drop``external`*名稱*`table`

**輸出**

返回刪除表的屬性。 請參考[.show 外部表](#show-external-tables)。

**範例：**

```kusto
.drop external table ExternalBlob
```

| TableName | TableType | 資料夾         | 文件字串 | 結構描述       | 屬性 |
|-----------|-----------|----------------|-----------|-----------------------------------------------------|------------|
| T         | Blob      | 外部表 | Docs      | [名稱":"x","CslType":"長"*,<br> • "名稱":"s","CslType":"字串"** | {}         |

## <a name="external-tables-in-azure-storage-or-azure-data-lake"></a>Azure 儲存或 Azure 資料湖中的外部表

以下命令介紹如何創建外部表。 該表可以位於 Azure Blob 儲存、Azure 資料湖儲存第 1 代或 Azure 資料湖儲存 Gen2 中。 
[儲存連線字串](../api/connection-strings/storage.md)描述為每個選項建立連接字串。 

### <a name="create-or-alter-external-table"></a>.創建或 .alter 外部表

**語法**

`.create``.alter` `table` *TableName*( ) 表名稱 ( 架構 )*Schema*  |  `external`  
`kind` `=` (`blob` | `adl`)  
`partition``by`[*Partition*分割`,`區 ]  
`dataformat``=`*格式*  
`(`  
*儲存連線字串*`,`= ...]  
`)`  
[`with` `(` `,``folder``=``=`*value*`,`*FolderName* *property_name**Documentation*] 文件 [ 資料夾名稱 】, property_name 值 ... `docstring` `=``)`]

在執行命令的資料庫中創建或更改新的外部表。

**參數**

* *表名稱*- 外部表名稱。 必須遵循[實體名稱](../query/schema-entities/entity-names.md)的規則。 外部表不能具有相同的名稱,因為同一資料庫中的常規表。
* *架構*- 格式的外部`ColumnName:ColumnType[, ColumnName:ColumnType ...]`資料架構 : . 如果外部資料架構未知,請使用[infer_storage_schema](../query/inferstorageschemaplugin.md)外掛程式,該外掛程式可以基於外部檔內容推斷架構。
* *分區*- 一個或多個分區定義(可選)。 請參閱下面的分區語法。
* *格式*- 資料格式。 查詢支援任何[引入格式](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats)。 匯[出機制](data-export/export-data-to-an-external-table.md)`CSV`使用外部表僅限於以下格式: `TSV` `JSON`、 `Parquet`、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 , , , , , , , , , , , , , , , , , , ,
* *儲存連線字串*- 一條或多條路徑到 Azure Blob 儲存 Blob 容器或 Azure 資料湖儲存檔案系統(虛擬目錄或資料夾),包括憑據。 關於詳細資訊,請參考[儲存連線字串](../api/connection-strings/storage.md)。 強烈建議提供多個存儲帳戶,以避免將大量數據[匯出](data-export/export-data-to-an-external-table.md)到外部表時限制存儲。 匯出將在提供的所有帳戶之間分配寫入。 

**區語法**

【`format_datetime =` *日期時間分區格式*】`bin(`*時間標記名*,*分區按時間範圍*`)`  
|   
【*字串格式前置字串*:*字串欄位 =**字串格式修復*\)

**分割區參數**

* *日期時間分區格式*- 輸出路徑中所需目錄結構的格式(可選)。 如果定義了分區且未指定格式,則預設值為"yyyy/MM/dd/HH/mm",基於分區ByTimeSpan。 例如,如果按 1d 分區,則結構將為"yyyy/MM/dd"。 如果按 1 小時分區,則結構將為"yyyy/MM/dd/HH"。
* *時間戳列名*- 對表進行分割區的日期時間列。 匯出到外部表時,時間戳列必須存在於外部表架構定義和匯出查詢的輸出中。
* *分割區時間範圍*─時間跨度文字,由該文字進行分區。
* *StringFormat前置字*串 - 將成為項目路徑的一部分的常量字串文字,在表值之前串聯(可選)。
* *StringFormatSuffix* - 將成為項目路徑的一部分的常量字串文本,在表值之後串聯(可選)。
* *字串欄位 ─*對表進行分割區的字串列。 字串列必須存在於外部表架構定義中。

**選擇性屬性**：

| 屬性         | 類型     | 描述       |
|------------------|----------|-------------------------------------------------------------------------------------|
| `folder`         | `string` | 表的資料夾                                                                     |
| `docString`      | `string` | 記錄表的字串                                                       |
| `compressed`     | `bool`   | 若設定,指示 Blob 是否`.gz`壓縮為 檔案                  |
| `includeHeaders` | `string` | 對於 CSV 或 TSV Blob,指示 blob 是否包含標頭                     |
| `namePrefix`     | `string` | 如果設置,則指示 blob 的前置字。 在寫入操作時,將使用此前綴寫入所有 Blob。 在讀取操作中,僅讀取具有此前綴的 Blob。 |
| `fileExtension`  | `string` | 如果設置,則指示 blob 的檔副檔名。 寫入時,blob 名稱將在此後綴後結束。 讀取時,將僅讀取具有此文件副檔名的 Blob。           |
| `encoding`       | `string` | 指示文字的編碼方式(`UTF8NoBOM`預設`UTF8BOM`) 或 。             |

> [!NOTE]
> * 如果表存在,`.create`則命令將失敗並出現錯誤。 用於`.alter`修改現有表。 
> * 不支援更改外部 blob 表的架構、格式或分區定義。 

需要的`.create`[資料庫使用者權限](../management/access-control/role-based-authorization.md)與`.alter`[表管理員權限](../management/access-control/role-based-authorization.md)。 
 
**範例** 

非分區外部表。 所有工件應直接位於定義的容器下:

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
   folder = "ExternalTables",
   namePrefix="Prefix"
)  
```

按日期時間分區的外部表。 專案以「yyyy/MM/dd」格式駐留在目錄中,在定義的路徑下:

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
   folder = "ExternalTables",
   namePrefix="Prefix"
)  
```

依日期時間分區的外部表,目錄格式為「年_yyyy/month=MM/day_dd」:

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
   folder = "ExternalTables",
   namePrefix="Prefix"
)
```

具有每月資料分割區與「yyyy/MM」目錄格式的外部表:

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
   folder = "ExternalTables",
   namePrefix="Prefix"
)
```

具有兩個分區的外部表。 目錄結構是兩個分區的串聯:格式化的客戶名稱,後跟預設日期時間格式。 例如「客戶名稱=軟作品/2011/11/11」:

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

|TableName|TableType|資料夾|文件字串|屬性|ConnectionStrings|資料分割|
|---|---|---|---|---|---|---|
|外部多個分割區|Blob|外部表|Docs|{"格式":"Csv","壓縮":false,"壓縮類型":null,"檔案擴展":"csv","包括標題":"無","編碼":null,"Name首綴":null]|["https://storageaccount.blob.core.windows.net/container1;*******"]}|{"StringFormat":"客戶名稱","{0}列名":"客戶名稱","ordinal":0},分區由":"1.00:00:00","列名":"時間戳","Ordinal":1]。|

#### <a name="spark-virtual-columns-support"></a>支援火花虛擬列

從 Spark 匯出資料時,分區列(在數據幀編`partitionBy`寫器 的方法中指定)不會寫入數據檔。 這樣做是為了避免資料重複,因為"資料夾"名稱中已有的數據(例如`column1=<value>/column2=<value>/`),Spark 可以在讀取時識別數據。 但是,Kusto 要求分區列存在於數據本身中。 計劃支援 Kusto 中的虛擬列。 在此之前,請使用以下解決方法:從 Spark 匯出資料時,在編寫資料框之前創建資料分區的所有欄的副本:

```kusto
df.withColumn("_a", $"a").withColumn("_b", $"b").write.partitionBy("_a", "_b").parquet("...")
```

在 Kusto 中定義外部表時,請指定分區列,如以下範例所示:

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

### <a name="show-external-table-artifacts"></a>.顯示外部表專案

* 返回查詢給定外部表時將處理的所有專案的清單。
* 需要[資料庫使用者權限](../management/access-control/role-based-authorization.md)。

**語法：** 

`.show``external`*名稱*`table``artifacts`

**輸出**

| 輸出參數 | 類型   | 描述                       |
|------------------|--------|-----------------------------------|
| Uri              | 字串 | 外部儲存工件的 URI |

**範例：**

```kusto
.show external table T artifacts
```

**輸出:**

| Uri                                                                     |
|-------------------------------------------------------------------------|
| https://storageaccount.blob.core.windows.net/container1/folder/file.csv |

### <a name="create-external-table-mapping"></a>.建立外部表對應

`.create``external``table`外部表名稱映射名稱 映射在Json格式`mapping`*MappingName**ExternalTableName*`json`*MappingInJsonFormat*

創建新映射。 有關詳細資訊,請參考[資料映射](./mappings.md#json-mapping)。

**範例** 
 
```kusto
.create external table MyExternalTable JSON mapping "Mapping1" '[{ "column" : "rownumber", "datatype" : "int", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]'
```

**範例輸出**

| 名稱     | 種類 | 對應                                                           |
|----------|------|-------------------------------------------------------------------|
| 映射1 | JSON | {"列名":"行號","列類型":"int","屬性":\"路徑":"$.行號"*,"列名":"行吉德","列式":"屬性":\"路徑":"$.rowguid"]。 |

### <a name="alter-external-table-mapping"></a>.alter 外部表對應

`.alter``external``table`外部表名稱映射名稱 映射在Json格式`mapping`*MappingName**ExternalTableName*`json`*MappingInJsonFormat*

更改現有映射。 
 
**範例** 
 
```kusto
.alter external table MyExternalTable JSON mapping "Mapping1" '[{ "column" : "rownumber", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]'
```

**範例輸出**

| 名稱     | 種類 | 對應                                                                |
|----------|------|------------------------------------------------------------------------|
| 映射1 | JSON | {"列名":"行號","列類型":""屬性":{"路徑":"$.rownumber"},\"列名":"行吉德","列式","屬性":"\""路徑":"$.rowguid"]。 |

### <a name="show-external-table-mappings"></a>.顯示外部表對應

`.show``external`*MappingName*`table`外部表名稱 映射名稱`mapping`*ExternalTableName*`json` 

`.show``external``table`外部*表名稱*`json``mappings`

顯示對應(全部或名稱指定的映射)。
 
**範例** 
 
```kusto
.show external table MyExternalTable JSON mapping "Mapping1" 

.show external table MyExternalTable JSON mappings 
```

**範例輸出**

| 名稱     | 種類 | 對應                                                                         |
|----------|------|---------------------------------------------------------------------------------|
| 映射1 | JSON | {"列名":"行號","列類型":""屬性":{"路徑":"$.rownumber"},\"列名":"行吉德","列式","屬性":"\""路徑":"$.rowguid"]。 |

### <a name="drop-external-table-mapping"></a>.刪除外部表對應

`.drop``external`*MappingName*`table`外部表名稱 映射名稱`mapping`*ExternalTableName*`json` 

從資料庫中刪除映射。
 
**範例** 
 
```kusto
.drop external table MyExternalTable JSON mapping "Mapping1" 
```

## <a name="external-sql-table"></a>外部 SQL 表

### <a name="create-or-alter-external-sql-table"></a>.建立或變更外部 sql 表

在執行命令的資料庫中創建或更改外部 SQL 表。  

**語法**

`.create` `table` *TableName*(`.alter` ) 表名稱 ([列名稱:列類型],...) `external`  |   
`kind` `=` `sql`  
`table``=` *SqlTable 名稱*  
`(`*SqlServer 連接字串*`)`  
[`with` `(` `,``folder``=``=`*value*`,`*FolderName* *property_name**Documentation*] 文件 [ 資料夾名稱 】, property_name 值 ... `docstring` `=``)`]


**參數:**

* *表名稱*- 外部表名稱。 必須遵循[實體名稱](../query/schema-entities/entity-names.md)的規則。 外部表不能具有相同的名稱,因為同一資料庫中的常規表。
* *SqlTable 名稱*- SQL 表的名稱。
* *SqlServer 連接字串*- 到 SQL 伺服器的連接字串。 可以是下列其中一項： 
    * **AAD 整合身份驗證**(`Authentication="Active Directory Integrated"`): 使用者或應用程式透過 AAD 對 Kusto 進行身分驗證,然後使用相同的權杖來存取 SQL Server 網路終結點。
    * **使用者名稱/密碼認證**`User ID=...; Password=...;`( 。 如果外部表用於[連續匯出](data-export/continuous-data-export.md),則必須使用此方法執行身份驗證。 

> [!WARNING]
> 包含機密資訊的連接字串和查詢應混淆,以便從任何 Kusto 跟蹤中省略它們。 關於詳細資訊,請參考[模糊字串文字](../query/scalar-data-types/string.md#obfuscated-string-literals)。

**選擇屬性**
| 屬性            | 類型            | 描述                          |
|---------------------|-----------------|---------------------------------------------------------------------------------------------------|
| `folder`            | `string`        | 表的資料夾。                  |
| `docString`         | `string`        | 記錄表的字串。      |
| `firetriggers`      | `true`/`false`  | 如果`true`指示目標系統觸發 SQL 表上定義的 INSERT 觸發器。 預設值為 `false`。 (有關詳細資訊,請參閱[BULK INSERT](https://msdn.microsoft.com/library/ms188365.aspx)和[系統.Data.SqlClient.SqlBulkCopy](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy(v=vs.110).aspx)) |
| `createifnotexists` | `true`/ `false` | 如果`true`,將創建目標 SQL 表(如果不存在);在這種情況下`primarykey`,必須提供該屬性來指示作為主鍵的結果列。 預設值為 `false`。  |
| `primarykey`        | `string`        | 如果`createifnotexists``true`是 ,則指示結果中的列的名稱,如果該列是由此命令創建的,則該列將用作 SQL 表的主鍵。                  |

> [!NOTE]
> * 如果表存在,則`.create`命令將失敗並出現錯誤。 用於`.alter`修改現有表。 
> * 不支援更改外部 SQL 表的架構或格式。 

需要的`.create`[資料庫使用者權限](../management/access-control/role-based-authorization.md)與`.alter`[表管理權限](../management/access-control/role-based-authorization.md)。 
 
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

| TableName   | TableType | 資料夾         | 文件字串 | 屬性                            |
|-------------|-----------|----------------|-----------|---------------------------------------|
| 外部 Sql | Sql       | 外部表 | Docs      | {<br>  "目標實體":"可定位",<br>  "目標實體名稱":"我的 SqlTable",<br>  "目標實體連接字串":"伺服器_tcp:myserver.database.windows.net,1433;身份驗證=活動目錄集成;初始目錄=my資料庫;",<br>  "火觸發":真實,<br>  "創建不存在":真實,<br>  "主鍵":"x"<br>} |

### <a name="querying-an-external-table-of-type-sql"></a>查詢 SQL 類型的外部表 
與其他類型的外部表類似,支援查詢外部 SQL 表。 請參考[查詢外部表](https://docs.microsoft.com/azure/data-explorer/data-lake-query-data)。 請注意,SQL 外部表查詢實現將從 SQL 表中執行完整的「SELECT +」(或選擇相關列),而查詢的其餘部分將在 Kusto 端執行。 請考慮以下外部表查詢: 

```kusto
external_table('MySqlExternalTable') | count
```

Kusto 將對 SQL 資料庫執行「SELECT + 從 TABLE」查詢,然後對 Kusto 端執行計數。 在這種情況下,如果直接用 T-SQL("SELECT COUNT(1)從 TABLE"寫入並使用[sql_request外掛程式](../query/sqlrequestplugin.md)執行,而不是使用外部表函數,性能預期會更好。 同樣,篩選器不會推送到 SQL 查詢。  
當查詢需要讀取整個表(或相關列)以在 Kusto 端進一步執行時,建議使用外部表來查詢 SQL 表。 當 SQL 查詢可以在 T-SQL 中顯著最佳化時,請使用[sql_request外掛程式](../query/sqlrequestplugin.md)。
