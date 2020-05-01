---
title: Kusto IngestionBatching 原則管理-Azure 資料總管
description: 本文說明 Azure 資料總管中的 IngestionBatching 原則。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: e9823fd0cd44dd2e5bd0731cc59086961ce86d8c
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617760"
---
# <a name="ingestionbatching-policy"></a>IngestionBatching 原則

[IngestionBatching 原則](batchingpolicy.md)是一個原則物件，可根據指定的設定來決定資料匯總應該在資料內嵌時停止的時間。

原則可以設定為`null`，在此情況下會使用預設值，將最大的批次處理時間範圍設為：5分鐘、1000個專案，以及1g 的總批次大小或 Kusto 所設定的預設叢集值。

如果未針對特定實體設定原則，則會尋找較高的階層層級原則，如果全部都設為 null，則會使用預設值。 

原則的限制為10秒，不建議使用大於15分鐘的值。

## <a name="displaying-the-ingestionbatching-policy"></a>顯示 IngestionBatching 原則

您可以在資料庫或資料表上設定此原則，並使用下列其中一個命令來顯示它：

* `.show``database` *DatabaseName* DatabaseName `policy``ingestionbatching`
* `.show``table` * *DatabaseName`.` * * TableName `policy``ingestionbatching`

## <a name="altering-the-ingestionbatching-policy"></a>改變 IngestionBatching 原則

```kusto
.alter <entity_type> <database_or_table_name> policy ingestionbatching @'<ingestionbatching policy json>'
```

改變多個資料表的 IngestionBatching 原則（在相同的資料庫內容中）：

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

* `entity_type`：資料表、資料庫
* `database_or_table`：如果實體是資料表或資料庫，則應在命令中指定其名稱，如下所示- 
  - `database_name` 或 
  - `database_name.table_name` 或 
  - `table_name`（在特定資料庫的內容中執行時）

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
