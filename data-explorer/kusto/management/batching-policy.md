---
title: Kusto IngestionBatching 原則管理命令-Azure 資料總管
description: 本文說明 Azure 資料總管中的 IngestionBatching 原則命令。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 2ff3578b055fd487f3d339dc9b7fb4aa1ad11e14
ms.sourcegitcommit: d9e203a54b048030eeb6d05b01a65902ebe4e0b8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2020
ms.locfileid: "97371436"
---
# <a name="ingestionbatching-policy-command"></a>IngestionBatching 原則命令

[IngestionBatching 原則](batchingpolicy.md)是一種原則物件，可決定資料根據指定的設定在內嵌期間停止的時間。

原則可以設定為 `null` ，在此情況下會使用預設值、將最大批次處理時間範圍設定為：5分鐘、1000個專案，以及 Kusto 所設定的預設叢集值。

如果未設定特定實體的原則，則會尋找較高的階層層級原則，如果全部都設定為 null，則會使用預設值。 

**IngestionBatching 限制：**

| 類型 | 預設 | 最小值 | 最大值
|---|---|---|---|
| 項目數 | 1000 | 1 | 2000 |
| 資料大小 (MB)  | 1000 | 100 | 1000 |
| 時間 | 5 分鐘 | 10 秒 | 15 分鐘 |

## <a name="displaying-the-ingestionbatching-policy"></a>顯示 IngestionBatching 原則

您可以在資料庫或資料表上設定原則，並使用下列其中一個命令來顯示此原則：

* `.show``database`  DatabaseName `policy``ingestionbatching`
* `.show``table` *DatabaseName* `.`  TableName `policy``ingestionbatching`

## <a name="altering-the-ingestionbatching-policy"></a>改變 IngestionBatching 原則

```kusto
.alter <entity_type> <database_or_table_name> policy ingestionbatching @'<ingestionbatching policy json>'
```

針對相同資料庫內容中 (的多個資料表變更 IngestionBatching 原則) ：

```kusto
.alter tables (table_name [, ...]) policy ingestionbatching @'<ingestionbatching policy json>'
```

IngestionBatching 原則：

```kusto
{
  "MaximumBatchingTimeSpan": "00:05:00",
  "MaximumNumberOfItems": 500, 
  "MaximumRawDataSizeMB": 1024
}
```

* `entity_type` ：資料表、資料庫
* `database_or_table`：如果實體是資料表或資料庫，則應該在命令中指定其名稱，如下所示： 
  - `database_name` 或 
  - `database_name.table_name` 或 
  - `table_name` 在特定資料庫的內容中執行時， () 

## <a name="deleting-the-ingestionbatching-policy"></a>正在刪除 IngestionBatching 原則

```kusto
.delete <entity_type> <database_or_table_name> policy ingestionbatching
```

**範例**

```kusto
// Show IngestionBatching policy for table `MyTable` in database `MyDatabase`
.show table MyDatabase.MyTable policy ingestionbatching 

// Set IngestionBatching policy on table `MyTable` (in database context) to batch ingress data by 30 seconds, 500 files, or 1GB (whatever comes first)
.alter table MyTable policy ingestionbatching @'{"MaximumBatchingTimeSpan":"00:00:30", "MaximumNumberOfItems": 500, "MaximumRawDataSizeMB": 1024}'

// Set IngestionBatching policy on multiple tables (in database context) to batch ingress data by 1 minute, 20 files, or 300MB (whatever comes first)
.alter tables (MyTable1, MyTable2, MyTable3) policy ingestionbatching @'{"MaximumBatchingTimeSpan":"00:01:00", "MaximumNumberOfItems": 20, "MaximumRawDataSizeMB": 300}'

// Delete IngestionBatching policy on table `MyTable`
.delete table MyTable policy ingestionbatching

// Delete IngestionBatching policy on database `MyDatabase`
.delete database MyDatabase policy ingestionbatching
```
