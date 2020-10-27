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
ms.openlocfilehash: 31d051bf104778ecc4c2a743db6bda5ccf8ed89f
ms.sourcegitcommit: ef3d919dee27c030842abf7c45c9e82e6e8350ee
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92630121"
---
# <a name="cache-policy-command"></a>快取原則命令

本文說明用來建立和改變快取[原則](cachepolicy.md)的命令 

## <a name="displaying-the-cache-policy"></a>顯示快取原則

您可以在資料庫、資料表或 [具體化視圖](materialized-views/materialized-view-overview.md)上設定原則，並使用下列其中一個命令來顯示此原則：

* `.show``database` *DatabaseName* DatabaseName `policy``caching`
* `.show` `table` *TableName* `policy` `caching`
* `.show``materialized-view` *MaterializedViewName* MaterializedViewName `policy``caching`

## <a name="altering-the-cache-policy"></a>改變快取原則

```kusto
.alter <entity_type> <database_or_table_or_materialized-view_name> policy caching hot = <timespan>
```

變更多個資料表的快取原則， (在相同的資料庫內容) ：

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

* `entity_type` ：資料表、具體化 view、database 或 cluster
* `database_or_table_or_materialized-view`：如果實體是資料表或資料庫，則應該在命令中指定其名稱，如下所示： 
  - `database_name` 或 
  - `database_name.table_name` 或 
  - `table_name` 在特定資料庫的內容中執行時， () 

## <a name="deleting-the-cache-policy"></a>正在刪除快取原則

```kusto
.delete <entity_type> <database_or_table_name> policy caching
```

**範例**

顯示資料庫中資料表的快取原則 `MyTable` `MyDatabase` ：

```kusto
.show table MyDatabase.MyTable policy caching 
```

將資料庫內容中資料表 (的快取原則 `MyTable`) 設定為3天：

```kusto
.alter table MyTable policy caching hot = 3d
.alter materialized-view MyMaterializedView policy caching hot = 3d
```

將資料庫內容)  (多個資料表的原則設定為3天：

```kusto
.alter tables (MyTable1, MyTable2, MyTable3) policy caching hot = 3d
```

刪除資料表上設定的原則：

```kusto
.delete table MyTable policy caching
```

刪除具體化視圖上設定的原則：

```kusto
.delete materialized-view MyMaterializedView policy caching
```

刪除資料庫上設定的原則：

```kusto
.delete database MyDatabase policy caching
```
