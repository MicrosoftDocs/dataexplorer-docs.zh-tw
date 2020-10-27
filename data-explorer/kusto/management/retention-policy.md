---
title: Kusto 保留原則管理-Azure 資料總管
description: 本文說明 Azure 資料總管中的保留原則。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 79cac49a553a2b906947b4c85948b67718641587
ms.sourcegitcommit: ef3d919dee27c030842abf7c45c9e82e6e8350ee
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92630070"
---
# <a name="retention-policy-command"></a>保留原則命令

本文說明用來建立及改變 [保留原則](retentionpolicy.md)的控制命令。

## <a name="show-retention-policy"></a>顯示保留原則

```kusto
.show <entity_type> <database_or_table_or_materialized_view> policy retention

.show <entity_type> *  policy retention
```

* `entity_type` ：資料表、具體化視圖或資料庫
* `database_or_table_or_materialized_view`： `database_name` 或 `database_name.table_name` 或 `table_name` (資料庫內容) 或 `materialized_view_name`

**範例**

顯示名為之資料庫的保留原則 `MyDatabase` ：

```kusto
.show database MyDatabase policy retention
```

## <a name="delete-retention-policy"></a>刪除保留原則

刪除資料保留原則是 affectively 設定無限制的資料保留。

刪除資料表的資料保留原則，將會導致資料表衍生資料庫層級的保留原則。

```kusto
.delete <entity_type> <database_or_table_or_materialized_view> policy retention
```

* `entity_type` ：資料表、具體化視圖或資料庫
* `database_or_table_or_materialized_view`： `database_name` 或 `database_name.table_name` 或 `table_name` (資料庫內容) 或 `materialized_view_name`

**範例**

刪除名為的資料表的保留原則 `MyTable1` ：

```kusto
.delete table MyTable policy retention
```


## <a name="alter-retention-policy"></a>改變保留原則

```kusto
.alter <entity_type> <database_or_table_or_materialized_view> policy retention <retention_policy>

.alter tables (<table_name> [, ...]) policy retention <retention_policy>

.alter-merge <entity_type> <database_or_table_or_materialized_view> policy retention <retention_policy>

.alter-merge <entity_type> <database_or_table_or_materialized_view> policy retention [softdelete = <timespan>] [recoverability = disabled|enabled]
```

* `entity_type` ：資料表或資料庫或具體化視圖
* `database_or_table_or_materialized_view`： `database_name` 或 `database_name.table_name` 或 `table_name` (資料庫內容) 或 `materialized_view_name`
* `table_name` ：資料庫內容中的資料表名稱。  `*`此處允許萬用字元 () 。
* `retention_policy` :

```kusto
    "{ 
        \"SoftDeletePeriod\": \"10.00:00:00\", \"Recoverability\": \"Disabled\"
    }" 
```

**範例**

顯示名為之資料庫的保留原則 `MyDatabase` ：

```kusto
.show database MyDatabase policy retention
```

以10天的虛刪除期間設定保留原則，並停用資料復原能力：

```kusto
.alter-merge table Table1 policy retention softdelete = 10d recoverability = disabled

.alter-merge materialized-view View1 policy retention softdelete = 10d recoverability = disabled
```

以10天的虛刪除期間設定保留原則，並啟用資料復原能力：

```kusto
.alter table Table1 policy retention "{\"SoftDeletePeriod\": \"10.00:00:00\", \"Recoverability\": \"Enabled\"}"

.alter materialized-view View1 policy retention "{\"SoftDeletePeriod\": \"10.00:00:00\", \"Recoverability\": \"Enabled\"}"
```

設定與上述相同的保留原則，但這次是針對多個資料表 (Table1、Table2 和 Table3) ：

```kusto
.alter tables (Table1, Table2, Table3) policy retention "{\"SoftDeletePeriod\": \"10.00:00:00\", \"Recoverability\": \"Enabled\"}"
```

設定保留原則，其預設值：100年（已啟用虛刪除期間和復原能力）：

```kusto
.alter table Table1 policy retention "{}"
```