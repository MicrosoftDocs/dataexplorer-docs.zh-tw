---
title: Restrict 語句-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 Restrict 語句。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: f510e62e6b1ad828f0e132bbad214dc7119f8235
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243030"
---
# <a name="restrict-statement"></a>Restrict 陳述式

::: zone pivot="azuredataexplorer"

Restrict 語句會限制在其後面的查詢語句可以看見的資料表/視圖實體集。 例如，在包含兩個數據表 () 的資料庫中， `A` `B` 應用程式可以防止查詢的其餘部分存取 `B` ，而且只會使用視圖來「查看」資料表的有限形式 `A` 。

Restrict 語句的主要案例是用於接受使用者查詢，並想要對這些查詢套用資料列層級安全性機制的中介層應用程式。 仲介層應用程式可以在使用者的查詢前面加上 **邏輯模型**，這是一組 let 語句，定義限制使用者存取資料 (例如) 的視圖 `T | where UserId == "..."` 。 當加入最後一個語句時，它會限制使用者只能存取邏輯模型。

> [!NOTE]
> 您可以使用 restrict 語句來限制對另一個資料庫或叢集中實體的存取， () 的叢集名稱中不支援萬用字元。

## <a name="syntax"></a>語法

`restrict``access` `to` `(`[*EntitySpecifier* [ `,` ...]]`)`

其中 *EntitySpecifier* 是下列其中一項：
* Let 語句定義為表格式視圖的識別碼。
* 資料表參考 (類似 union 語句所使用的) 。
* 模式宣告所定義的模式。

不是由 restrict 語句指定的所有資料表、表格式視圖或模式都會變成「不可見」至查詢的其餘部分。 

## <a name="arguments"></a>引數

Restrict 語句可以取得一或多個參數，在實體的名稱解析期間定義寬鬆的限制。 實體可以是：
* 語句之前出現[let 語句](./letstatement.md) `restrict` 。 

  ```kusto
  // Limit access to 'Test' let statement only
  let Test = () { print x=1 };
  restrict access to (Test);
  ```

* 資料庫中繼資料中定義的[資料表](../management/tables.md)或[函數](../management/functions.md)。

    ```kusto
    // Assuming the database that the query uses has table Table1 and Func1 defined in the metadata, 
    // and other database 'DB2' has Table2 defined in the metadata
    
    restrict access to (database().Table1, database().Func1, database('DB2').Table2);
    ```

* 可符合 [let 語句](./letstatement.md) 或資料表/函式倍數的萬用字元模式  

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

## <a name="examples"></a>範例

下列範例示範中介層應用程式如何在使用者的查詢前面加上可防止使用者查詢任何其他使用者資料的邏輯模型。

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
