---
title: Restrict 語句-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的限制語句。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 8476680ad5b8206dcd7dfe98bf116bb5b6dcefdc
ms.sourcegitcommit: 085e212fe9d497ee6f9f477dd0d5077f7a3e492e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/22/2020
ms.locfileid: "85133445"
---
# <a name="restrict-statement"></a>Restrict 陳述式

::: zone pivot="azuredataexplorer"

Restrict 語句會限制資料表/視圖實體的集合，這些實體會顯示在其後面的查詢語句。 例如，在包含兩個數據表（，）的資料庫中 `A` `B` ，應用程式可以防止查詢的其他部分存取， `B` 而且只會使用 view 來「查看」有限的資料表形式 `A` 。

Restrict 語句的主要案例是針對接受使用者查詢的仲介層應用程式，而且想要在這些查詢上套用資料列層級的安全性機制。 中介層應用程式可以在使用者的查詢前面加上**邏輯模型**，這組 let 語句定義了限制使用者存取資料的視圖（例如 `T | where UserId == "..."` ）。 作為最後新增的語句，它會限制使用者只能存取邏輯模型。

**語法**

`restrict``access` `to` `(`[*EntitySpecifier* [ `,` ...]]`)`

其中*EntitySpecifier*是下列其中一項：
* 由 let 語句定義為表格式視圖的識別碼。
* 資料表參考（與 union 語句所使用的相同）。
* 模式聲明所定義的模式。

所有資料表、表格式視圖或不是由 restrict 語句指定的模式都會變成「不可見」，以供查詢的其餘部分使用。 

**注意事項**

Restrict 語句可用來限制存取另一個資料庫或叢集中的實體（叢集名稱中不支援萬用字元）。

**引數**

Restrict 語句可以在實體的名稱解析期間，取得定義寬鬆限制的一個或多個參數。 實體可以是：
- [let 語句](./letstatement.md)出現在 `restrict` 語句之前。 

```kusto
// Limit access to 'Test' let statement only
let Test = () { print x=1 };
restrict access to (Test);
```

- 資料庫中繼資料中定義的[資料表](../management/tables.md)或[函數](../management/functions.md)。

```kusto
// Assuming the database that the query uses has table Table1 and Func1 defined in the metadata, 
// and other database 'DB2' has Table2 defined in the metadata
 
restrict access to (database().Table1, database().Func1, database('DB2').Table2);
```

- 可以比對[let 語句](./letstatement.md)或資料表/函式之倍數的萬用字元模式  

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

下列範例示範中介層應用程式如何在邏輯模型前面加上使用者的查詢，以防止使用者查詢任何其他使用者的資料。

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

// Restricting access to Table1 in the current database and Table2 in database 'DB2'
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

Azure 監視器不支援這項功能

::: zone-end
