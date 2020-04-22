---
title: 快取策略 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的緩存策略。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: ca14703b2548bdb23dc3e6e352aeaacbc17303b4
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744479"
---
# <a name="cache-policy"></a>快取原則

本文介紹用於建立與變更[快取原則的指令](cachepolicy.md) 

## <a name="displaying-the-cache-policy"></a>顯示快取原則

策略可以在資料或表上設定,並使用以下指令之一顯示:

* `.show``database`*資料庫名稱*`policy``caching`
* `.show``table`*資料庫名稱*`.`*表格名稱*`policy``caching`

## <a name="altering-the-cache-policy"></a>變更快取原則

```kusto
.alter <entity_type> <database_or_table_name> policy caching hot = <timespan>
```

變更多個表的快取原則 (在同一資料庫上下文中):

```kusto
.alter tables (table_name [, ...]) policy caching hot = <timespan>
```

快取原則:

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

* `entity_type`:表、資料庫或群集
* `database_or_table`:如果實體是表或資料庫,則應在指令中指定其名稱,如下所示 : 
  - `database_name` 或 
  - `database_name.table_name` 或 
  - `table_name`( 在特定資料庫的上下文中執行時 )

## <a name="deleting-the-cache-policy"></a>移除快取原則

```kusto
.delete <entity_type> <database_or_table_name> policy caching
```

**範例**

在資料庫中`MyTable``MyDatabase`顯示表的快取原則 :

```kusto
.show table MyDatabase.MyTable policy caching 
```

將表`MyTable`的快取原則 (在資料庫上下文中)設定為 3 天:

```kusto
.alter table MyTable policy caching hot = 3d
```

將多個表的策略(在資料庫上下文中)設定為 3 天:

```kusto
.alter tables (MyTable1, MyTable2, MyTable3) policy caching hot = 3d
```

刪除表上的策略集:

```kusto
.delete table MyTable policy caching
```

刪除資料庫上的策略集:

```kusto
.delete database MyDatabase policy caching
```
