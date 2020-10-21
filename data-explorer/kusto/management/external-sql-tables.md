---
title: 建立和變更外部 SQL 資料表-Azure 資料總管
description: 本文說明如何建立和變更外部 SQL 資料表。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: d6473fd0efd588bfc7a6990dd2f57d70960b40e2
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342751"
---
# <a name="create-and-alter-external-sql-tables"></a>建立和改變外部 SQL 資料表

在執行命令的資料庫中建立或改變外部 SQL 資料表。  

## <a name="syntax"></a>語法

 (`.create`  |  `.alter`  |  `.create-or-alter`) `external` `table` *TableName* ( [columnName： columnType]，... )   
`kind` `=` `sql`  
`table``=` *SqlTableName*  
`(`*SqlServerConnectionString*`)`  
[ `with` `(` `docstring` `=` *檔*] [ `,` `folder` `=` *資料夾資料夾*]， *property_name* `=` *值* `,` ... `)` ]

## <a name="parameters"></a>參數

* *TableName* -外部資料表名稱。 必須遵循 [機構名稱](../query/schema-entities/entity-names.md)的規則。 外部資料表的名稱不能與相同資料庫中的一般資料表相同。
* *SqlTableName* -SQL 資料表的名稱。
* *SqlServerConnectionString* -SQL server 的連接字串。 可以是下列其中一種方法： 
  * *Aad 整合式驗證* (`Authentication="Active Directory Integrated"`) ：使用者或應用程式會透過 AAD 向 Kusto 進行驗證，然後使用相同的權杖來存取 SQL Server 的網路端點。
  *  () 的使用者*名稱/密碼驗證* `User ID=...; Password=...;` 。 如果外部資料表是用於 [連續匯出](data-export/continuous-data-export.md)，則必須使用此方法來執行驗證。 

> [!WARNING]
> 包含機密資訊的連接字串和查詢應該經過混淆，以便從任何 Kusto 追蹤中省略這些連接字串和查詢。 如需詳細資訊，請參閱 [模糊字串常](../query/scalar-data-types/string.md#obfuscated-string-literals)值。

## <a name="optional-properties"></a>選用屬性

| 屬性            | 類型            | 說明                          |
|---------------------|-----------------|---------------------------------------------------------------------------------------------------|
| `folder`            | `string`        | 資料表的資料夾。                  |
| `docString`         | `string`        | 記錄資料表的字串。      |
| `firetriggers`      | `true`/`false`  | 如果 `true` 為，則指示目標系統引發 SQL 資料表上定義的 INSERT 觸發程式。 預設為 `false`。  (需詳細資訊，請參閱 [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql) 和 [SqlClient SqlBulkCopy](/dotnet/api/system.data.sqlclient.sqlbulkcopy))  |
| `createifnotexists` | `true`/ `false` | 如果為 `true` ，將會建立目標 SQL 資料表（如果不存在的話）; `primarykey` 在此情況下，必須提供屬性以指出作為主鍵的結果資料行。 預設為 `false`。  |
| `primarykey`        | `string`        | 如果 `createifnotexists` 為 `true` ，則產生的資料行名稱會當做 SQL 資料表的主鍵（如果是由這個命令建立的話）。                  |

> [!NOTE]
> * 如果資料表存在， `.create` 命令將會失敗並出現錯誤。 使用 `.create-or-alter` 或 `.alter` 來修改現有的資料表。 
> * 不支援改變外部 SQL 資料表的架構或格式。 

需要的 [資料庫使用者](../management/access-control/role-based-authorization.md) 權力 `.create` ，以及的 [資料表管理許可權](../management/access-control/role-based-authorization.md) `.alter` 。 
 
**範例** 

```kusto
.create external table MySqlExternalTable (x:long, s:string) 
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
| MySqlExternalTable | Sql       | ExternalTables | Docs      | {<br>  "TargetEntityKind": "sqltable'",<br>  "TargetEntityName": "MySqlTable",<br>  "TargetEntityConnectionString"： "Server = tcp:myserver. .net，1433;Authentication = Active Directory 整合式; 初始目錄 = mydatabase;」，<br>  "FireTriggers"： true，<br>  ">createifnotexists"： true，<br>  "PrimaryKey"： "x"<br>} |

## <a name="querying-an-external-table-of-type-sql"></a>查詢 SQL 類型的外部資料表

支援查詢外部 SQL 資料表。 請參閱 [查詢外部資料表](../../data-lake-query-data.md)。 

> [!Note]
> SQL 外部資料表查詢執行將會執行 `SELECT x, s FROM MySqlTable` 語句，其中 `x` 和 `s` 是外部資料表資料行名稱。 查詢的其餘部分會在 Kusto 端執行。

請考慮下列外部資料表查詢： 

```kusto
external_table('MySqlExternalTable') | count
```

Kusto 會執行 `SELECT x, s FROM MySqlTable` SQL 資料庫的查詢，後面接著 Kusto 端的計數。 在這種情況下，如果以 T-sql 撰寫 (`SELECT COUNT(1) FROM MySqlTable`) ，並使用 [sql_request 外掛程式](../query/sqlrequestplugin.md)（而不是使用外部資料表函數）來執行，則效能應更好。 同樣地，篩選不會推送至 SQL 查詢。  

當查詢需要讀取整個資料表 (或相關資料行時，請使用外部資料表來查詢 SQL 資料表，) 以便進一步在 Kusto 端執行。 當 SQL 查詢可在 T-sql 中優化時，請使用 [sql_request 外掛程式](../query/sqlrequestplugin.md)。

## <a name="next-steps"></a>後續步驟

* [外部資料表一般控制項命令](./external-table-commands.md)
* [建立和改變 Azure 儲存體或 Azure Data Lake 中的外部資料表](external-tables-azurestorage-azuredatalake.md)