---
title: 外部 SQL 資料表-Azure 資料總管
description: 本文說明 Azure 資料總管中的外部 SQL 資料表建立。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 9de01863ddb832421c5b6dbfe65b8ae451382fb7
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373378"
---
# <a name="external-sql-table"></a>外部 SQL 資料表

在執行命令的資料庫中，建立或改變外部 SQL 資料表。  

## <a name="syntax"></a>語法

（ `.create`  |  `.alter` ） `external` `table` *TableName* （[columnName： columnType]，...）  
`kind` `=` `sql`  
`table``=` *SqlTableName*  
`(`*SqlServerConnectionString*`)`  
[ `with` `(` [ `docstring` `=` *檔*] [ `,` `folder` `=` *資料夾資料夾*]， *property_name* `=` *值* `,` ... `)` ]

## <a name="parameters"></a>參數

* *TableName* -外部資料表名稱。 必須遵循[機構名稱](../query/schema-entities/entity-names.md)的規則。 外部資料表不能與相同資料庫中的一般資料表具有相同的名稱。
* *SqlTableName* -SQL 資料表的名稱。
* *SqlServerConnectionString* -SQL server 的連接字串。 可以是下列其中一種方法： 
  * *Aad 整合式驗證*（ `Authentication="Active Directory Integrated"` ）：使用者或應用程式會透過 AAD 向 Kusto 進行驗證，然後使用相同的權杖來存取 SQL Server 網路端點。
  * 使用者*名稱/密碼驗證*（ `User ID=...; Password=...;` ）。 如果外部資料表用於[連續匯出](data-export/continuous-data-export.md)，則必須使用此方法來執行驗證。 

> [!WARNING]
> 包含機密資訊的連接字串和查詢應該進行模糊處理，以便從任何 Kusto 追蹤中省略它們。 如需詳細資訊，請參閱[模糊的字串常](../query/scalar-data-types/string.md#obfuscated-string-literals)值。

## <a name="optional-properties"></a>選擇性屬性

| 屬性            | 類型            | 描述                          |
|---------------------|-----------------|---------------------------------------------------------------------------------------------------|
| `folder`            | `string`        | 資料表的資料夾。                  |
| `docString`         | `string`        | 記錄資料表的字串。      |
| `firetriggers`      | `true`/`false`  | 如果 `true` 為，則指示目標系統引發 SQL 資料表上定義的 INSERT 觸發程式。 預設值為 `false`。 （如需詳細資訊，請參閱[BULK INSERT](https://msdn.microsoft.com/library/ms188365.aspx)和[SqlClient. SqlBulkCopy）。](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy(v=vs.110).aspx) |
| `createifnotexists` | `true`/ `false` | 如果為 `true` ，則會建立目標 SQL 資料表（如果尚未存在的話）; `primarykey` 在此情況下，必須提供屬性，以指出做為主要索引鍵的結果資料行。 預設值為 `false`。  |
| `primarykey`        | `string`        | 如果 `createifnotexists` 為 `true` ，則產生的資料行名稱將會當做 SQL 資料表的主要金鑰使用（如果此命令是由這個命令所建立）。                  |

> [!NOTE]
> * 如果資料表存在，此 `.create` 命令將會失敗並產生錯誤。 使用 `.alter` 修改現有的資料表。 
> * 不支援改變外部 SQL 資料表的架構或格式。 

需要的[資料庫使用者許可權](../management/access-control/role-based-authorization.md) `.create` ，以及的[資料表管理員許可權](../management/access-control/role-based-authorization.md) `.alter` 。 
 
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
| ExternalSql | Sql       | ExternalTables | Docs      | {<br>  "TargetEntityKind": "sqltable'",<br>  "TargetEntityName": "MySqlTable",<br>  "TargetEntityConnectionString"： "Server = tcp:myserver. net，1433;Authentication = Active Directory 整合; 初始目錄 = mydatabase; "，<br>  "FireTriggers"： true，<br>  "CreateIfNotExists"： true，<br>  "PrimaryKey"： "x"<br>} |

## <a name="querying-an-external-table-of-type-sql"></a>查詢 SQL 類型的外部資料表 

支援查詢外部 SQL 資料表。 請參閱[查詢外部資料表](../../data-lake-query-data.md)。 

> [!Note]
> SQL 外部資料表查詢實行會從 SQL 資料表執行完整的「選取 *」（或選取相關的資料行）。 查詢的其餘部分將會在 Kusto 端執行。 

請考慮下列外部資料表查詢： 

```kusto
external_table('MySqlExternalTable') | count
```

Kusto 會對 SQL 資料庫執行 ' SELECT * from TABLE ' 查詢，後面接著 Kusto 端的計數。 在這種情況下，如果直接以 T-sql （[SELECT COUNT （1） FROM TABLE]）撰寫，並使用[sql_request 外掛程式](../query/sqlrequestplugin.md)執行，而不是使用外部資料表函數，效能就會更好。 同樣地，篩選器也不會推送至 SQL 查詢。  

當查詢需要讀取整個資料表（或相關的資料行），以便在 Kusto 端進一步執行時，請使用外部資料表來查詢 SQL 資料表。 當 SQL 查詢可以在 T-sql 中優化時，請使用[sql_request 外掛程式](../query/sqlrequestplugin.md)。
