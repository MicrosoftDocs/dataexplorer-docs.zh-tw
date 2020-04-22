---
title: 限制語句 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 資料資源管理器中的"限制"語句。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: cbd21c01956f817c5db19a93104028dba2b2b2b4
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765982"
---
# <a name="restrict-statement"></a>Restrict 陳述式

::: zone pivot="azuredataexplorer"

限制語句限制表/檢視實體的集,這些實體對它遵循的查詢語句可見。 例如,在包含兩個表`A`(`B`的 )的資料庫中,應用程式可以防止查詢的其餘`B`部分訪問,並且只能透過使用檢視"查看"有限形式的表`A`。

限制語句的主要方案適用於接受來自用戶的查詢並希望在這些查詢上應用行級安全機制的中間層應用程式。 中介層應用程式可以使用**邏輯模型**(一組定義限制使用者存取資料的檢視的允許語句)來為使用者的查詢提供首碼(例如`T | where UserId == "..."`。 作為添加的最後一個語句,它僅限制使用者對邏輯模型的訪問。

**語法**

`restrict``access` `to` `(` 【*實體規格 】* `,``)`

*其中實體規格器*是:
* 由 let 語句定義為表格檢視的標識碼。
* 表引用(類似於聯合語句使用的表引用)。
* 由模式聲明定義的模式。

限制語句未指定的所有表、表格檢視或模式對查詢的其餘部分都變為"不可見"。 

**注意事項**

限制語句可用於限制對另一個資料庫或群集中的實體的訪問(群集名稱中不支援通配符)。

**引數**

限制語句可以獲取一個或多個參數,這些參數定義實體名稱解析期間的允許限制。 實體可以是:
- [讓語句](./letstatement.md)出現在語`restrict`句 之前。 

```kusto
// Limit access to 'Test' let statement only
let Test = () { print x=1 };
restrict access to (Test);
```

- 在資料庫中定義的[表](../management/tables.md)或[函數](../management/functions.md)。

```kusto
// Assuming the database that the query uses has table Table1 and Func1 defined in the metadata, 
// and other database 'DB2' has Table2 defined in the metadata
 
restrict access to (database().Table1, database().Func1, database('DB2').Table2);
```

- 通配符模式,可以符合[允許文句](./letstatement.md)或表/函數的倍數  

```kusto
let Test1 = () { print x=1 };
let Test2 = () { print y=1 };
restrict access to (*);
// Now access is restricted to Test1, Test2 and no tables/functions are accessible.

// Assuming the database that the query uses has table Table1 and Func1 defined in the metadata.
// Assuming that database 'DB2' has table Table2 and Func2 defined in the metadata
restricts access to (database().*);
// Now access is restricted to all tables/functions of the current database ('DB2' is not accessible).

// Assuming the database that the query uses has table Table1 and Func1 defined in the metadata.
// Assuming that database 'DB2' has table Table2 and Func2 defined in the metadata
restricts access to (database('DB2').*);
// Now access is restricted to all tables/functions of the database 'DB2'
```


**範例**

下面的範例展示如何中間層應用程式使用邏輯模型來準備使用者的查詢,該邏輯模型阻止使用者查詢任何其他用戶的數據。

```kusto
// Assume the database has a single table, UserData,
// with a column called UserID and other columns that hold
// per-user private information.
//
// The middle-tier application generates the following statements.
// Note that "username@domain.com" is something the middle-tier application
// derives per-user as it authenticates the user.
let RestrictedData = view () { Data | where UserID == "username@domain.com" };
restrict access to (RestrictedData);
// The rest of the query is something that the user types.
// This part can only reference RestrictedData; attempting to reference Data
// will fail.
RestrictedData | summarize IrsLovesMe=sum(Salary) by Year, Month
```

```kusto
// Restricting access to Table1 in the current database (database() called without parameters)
restrict access to (database().Table1);
Table1 | count

// Restricting acess to Table1 in the current database and Table2 in database 'DB2'
restrict access to (database().Table1, database('DB2').Table2);
union 
    (Table1),
    (database('DB2').Table2))
| count

// Restricting access to Test statement only
let Test = () { range x from 1 to 10 step 1 };
restrict access to (Test);
Test
 
// Assume that there is a table called Table1, Table2 in the database
let View1 = view () { Table1 | project Column1 };
let View2 = view () { Table2 | project Column1, Column2 };
restrict access to (View1, View2);
 
// When those statements appear before the command - the next works
let View1 = view () { Table1 | project Column1 };
let View2 = view () { Table2 | project Column1, Column2 };
restrict access to (View1, View2);
View1 |  count
 
// When those statements appear before the command - the next access is not allowed
let View1 = view () { Table1 | project Column1 };
let View2 = view () { Table2 | project Column1, Column2 };
restrict access to (View1, View2);
Table1 |  count
```

::: zone-end

::: zone pivot="azuremonitor"

Azure 監視器不支援此功能

::: zone-end
