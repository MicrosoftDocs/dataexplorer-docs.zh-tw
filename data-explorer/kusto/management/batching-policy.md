---
title: 引入批次處理策略 - Azure 資料資源管理員 :微軟文件
description: 本文介紹 Azure 數據資源管理器中的引入批處理策略。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 232767e390669a08312f10d3999d19264fb29f26
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744268"
---
# <a name="ingestionbatching-policy"></a>引入批次處理原則

[引入批處理策略](batchingpolicy.md)是一個策略物件,它根據指定的設置確定數據聚合在數據引入期間應停止的時間。

可以將策略設置為`null`,在這種情況下,使用預設值,將最大批處理時間跨度設置為:5 分鐘、1000 個專案和 1G 的總批處理大小或 Kusto 設置的預設群集值。

如果未為特定實體設置策略,它將查找更高層次結構級別的策略,如果所有策略都設置為 null,則將使用預設值。 

該策略的下限為 10 秒,不建議使用大於 15 分鐘的值。

## <a name="displaying-the-ingestionbatching-policy"></a>顯示引入批次處理原則

策略可以在資料庫或表上設定,並使用以下指令之一顯示:

* `.show``database`*資料庫名稱*`policy``ingestionbatching`
* `.show``table`*資料庫名稱*`.`*表格名稱*`policy``ingestionbatching`

## <a name="altering-the-ingestionbatching-policy"></a>變更引入批次處理原則

```kusto
.alter <entity_type> <database_or_table_name> policy ingestionbatching @'<ingestionbatching policy json>'
```

變更多個表的引入批次處理策略(在同一資料庫上下文中):

```kusto
.alter tables (table_name [, ...]) policy ingestionbatching @'<ingestionbatching policy json>'
```

引入批次處理原則:

```kusto
{
  "MaximumBatchingTimeSpan": "00:05:00",
  "MaximumNumberOfItems": 500, 
  "MaximumRawDataSizeMB": 1024
}
```

* `entity_type`:表,資料庫
* `database_or_table`:如果實體是表或資料庫,則應在指令中指定其名稱,如下所示 : 
  - `database_name` 或 
  - `database_name.table_name` 或 
  - `table_name`( 在特定資料庫的上下文中執行時 )

## <a name="deleting-the-ingestionbatching-policy"></a>移除引入批次處理原則

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
