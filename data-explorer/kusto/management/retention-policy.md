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
ms.openlocfilehash: ebbd9aa5544d97ef1e980bcb3a53f74dbde66547
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967531"
---
# <a name="retention-policy-command"></a>保留原則命令

本文說明用來建立和改變[保留原則](retentionpolicy.md)的控制命令。

## <a name="show-retention-policy"></a>顯示保留原則

```kusto
.show <entity_type> <database_or_table> policy retention

.show <entity_type> *  policy retention
```

* `entity_type`：資料表或資料庫
* `database_or_table`： `database_name` 或 `database_name.table_name` 或 `table_name` （在資料庫內容中）

**範例**

顯示名為之資料庫的保留原則 `MyDatabase` ：

```kusto
.show database MyDatabase policy retention
```

## <a name="delete-retention-policy"></a>刪除保留原則

刪除資料保留原則是 affectively 設定無限制的資料保留。

刪除資料表的資料保留原則會導致資料表從資料庫層級衍生保留原則。

```kusto
.delete <entity_type> <database_or_table> policy retention
```

* `entity_type`：資料表或資料庫
* `database_or_table`： `database_name` 或 `database_name.table_name` 或 `table_name` （在資料庫內容中）

**範例**

刪除名為之資料表的保留原則 `MyTable1` ：

```kusto
.delete table MyTable policy retention
```


## <a name="alter-retention-policy"></a>改變保留原則

```kusto
.alter <entity_type> <database_or_table> policy retention <retention_policy>

.alter tables (<table_name> [, ...]) policy retention <retention_policy>

.alter-merge <entity_type> <database_or_table> policy retention <retention_policy>

.alter-merge <entity_type> <database_or_table_name> policy retention [softdelete = <timespan>] [recoverability = disabled|enabled]
```

* `entity_type`：資料表或資料庫
* `database_or_table`： `database_name` 或 `database_name.table_name` 或 `table_name` （在資料庫內容中）
* `table_name`：資料庫內容中的資料表名稱。  萬用字元（ `*` 可在這裡使用）。
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

使用10天的虛刪除週期和停用的資料復原能力來設定保留原則：

```kusto
.alter-merge table Table1 policy retention softdelete = 10d recoverability = disabled
```

設定具有10天虛刪除週期並啟用資料復原能力的保留原則：

```kusto
.alter table Table1 policy retention "{\"SoftDeletePeriod\": \"10.00:00:00\", \"Recoverability\": \"Enabled\"}"
```

會設定與上述相同的保留原則，但這次適用于多個資料表（Table1、Table2 和 Table3）：

```kusto
.alter tables (Table1, Table2, Table3) policy retention "{\"SoftDeletePeriod\": \"10.00:00:00\", \"Recoverability\": \"Enabled\"}"
```

設定具有預設值的保留原則：100年，做為虛刪除期間和復原能力的啟用：

```kusto
.alter table Table1 policy retention "{}"
```