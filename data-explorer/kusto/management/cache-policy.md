---
title: 快取原則-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的快取原則。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 319a71e5db7019ed28001f44a1d4a4bcb21984e9
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967242"
---
# <a name="cache-policy-command"></a>快取原則命令

本文說明用來建立和改變快取[原則](cachepolicy.md)的命令 

## <a name="displaying-the-cache-policy"></a>顯示快取原則

您可以在資料或資料表上設定此原則，並使用下列其中一個命令來顯示它：

* `.show` `database` *DatabaseName* `policy` `caching`
* `.show``table` *DatabaseName* `.` *TableName* TableName `policy``caching`

## <a name="altering-the-cache-policy"></a>改變快取原則

```kusto
.alter <entity_type> <database_or_table_name> policy caching hot = <timespan>
```

改變多個資料表的快取原則（在相同的資料庫內容中）：

```kusto
.alter tables (table_name [, ...]) policy caching hot = <timespan>
```

快取原則：

```kusto
{
  "DataHotSpan": {
    "Value": "3.00:00:00"
  },
  "IndexHotSpan": {
    "Value": "3.00:00:00"
  }
}
```

* `entity_type`：資料表、資料庫或叢集
* `database_or_table`：如果實體是資料表或資料庫，則應在命令中指定其名稱，如下所示- 
  - `database_name` 或 
  - `database_name.table_name` 或 
  - `table_name`（在特定資料庫的內容中執行時）

## <a name="deleting-the-cache-policy"></a>刪除快取原則

```kusto
.delete <entity_type> <database_or_table_name> policy caching
```

**範例**

顯示資料庫中資料表的快取原則 `MyTable` `MyDatabase` ：

```kusto
.show table MyDatabase.MyTable policy caching 
```

將資料表的快取原則 `MyTable` （在資料庫內容中）設定為3天：

```kusto
.alter table MyTable policy caching hot = 3d
```

將多個資料表的原則（在資料庫內容中）設為3天：

```kusto
.alter tables (MyTable1, MyTable2, MyTable3) policy caching hot = 3d
```

刪除資料表上設定的原則：

```kusto
.delete table MyTable policy caching
```

刪除資料庫上所設定的原則：

```kusto
.delete database MyDatabase policy caching
```
