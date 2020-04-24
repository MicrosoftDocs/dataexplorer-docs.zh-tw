---
title: 保留策略 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的保留策略。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: b0366bef619d815bbe58f91730eff70ec847c239
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520344"
---
# <a name="retention-policy"></a>保留原則

本文介紹了用於創建和更改[保留策略](retentionpolicy.md)的控制命令。

## <a name="show-retention-policy"></a>顯示保留原則

```kusto
.show <entity_type> <database_or_table> policy retention

.show <entity_type> *  policy retention
```

* `entity_type`:表或資料庫
* `database_or_table`或`database_name``database_name.table_name`或`table_name`( 在資料庫中內文中 )

**範例**

顯示名為的`MyDatabase`資料庫的保留原則:

```kusto
.show database MyDatabase policy retention
```

## <a name="delete-retention-policy"></a>刪除保留原則

刪除數據保留策略是動態設置無限制的數據保留。

刪除表的數據保留策略將導致表從資料庫級別派生保留策略。

```kusto
.delete <entity_type> <database_or_table> policy retention
```

* `entity_type`:表或資料庫
* `database_or_table`或`database_name``database_name.table_name`或`table_name`( 在資料庫中內文中 )

**範例**

刪除名為的`MyTable1`表格的保留政策:

```kusto
.delete table MyTable policy retention
```


## <a name="alter-retention-policy"></a>變更保留原則

```kusto
.alter <entity_type> <database_or_table> policy retention <retention_policy>

.alter tables (<table_name> [, ...]) policy retention <retention_policy>

.alter-merge <entity_type> <database_or_table> policy retention <retention_policy>

.alter-merge <entity_type> <database_or_table_name> policy retention [softdelete = <timespan>] [recoverability = disabled|enabled]
```

* `entity_type`:表或資料庫
* `database_or_table`或`database_name``database_name.table_name`或`table_name`( 在資料庫中內文中 )
* `table_name`:資料庫上下文中表的名稱。  通配符(`*`此處允許)。
* `retention_policy` :

```
    "{ 
        \"SoftDeletePeriod\": \"10.00:00:00\", \"Recoverability\": \"Disabled\"
    }" 
```

**範例**

顯示名為的`MyDatabase`資料庫的保留原則:

```kusto
.show database MyDatabase policy retention
```

設定具有 10 天軟刪除週期和關閉資料可復原性的保留政策:

```kusto
.alter-merge table Table1 policy retention softdelete = 10d recoverability = disabled
```

設定具有 10 天軟刪除週期並開啟的資料可復原性的保留原則:

```kusto
.alter table Table1 policy retention "{\"SoftDeletePeriod\": \"10.00:00:00\", \"Recoverability\": \"Enabled\"}"
```

設定與上述相同的保留策略,但這次為多個表(表 1、表 2 和表 3):

```kusto
.alter tables (Table1, Table2, Table3) policy retention "{\"SoftDeletePeriod\": \"10.00:00:00\", \"Recoverability\": \"Enabled\"}"
```

設定具有預設值的保留策略:100 年作為軟刪除週期,並啟用可恢復性:

```kusto
.alter table Table1 policy retention "{}"
```