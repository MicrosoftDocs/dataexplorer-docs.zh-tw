---
title: 合併原則管理-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的合併原則管理。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9ef6f2cd2359e35e90c3903738adf82728e9da40
ms.sourcegitcommit: a562ce255ac706ca1ca77d272a97b5975235729d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/26/2020
ms.locfileid: "83867064"
---
# <a name="merge-policy-management"></a>合併原則管理

## <a name="show-policy"></a>顯示原則

```kusto
.show table [table_name] policy merge

.show table * policy merge

.show database [database_name] policy merge

.show database * policy merge
```

顯示資料庫或資料表的目前合併原則。
如果指定的名稱為 ' * '，則顯示指定之實體類型（資料庫或資料表）的所有原則。

### <a name="output"></a>輸出

|原則名稱 | 實體名稱 | 原則 | 子實體 | 實體類型
|---|---|---|---|---
|ExtentsMergePolicy | 資料庫/資料表名稱 | JSON 格式的字串，表示原則 | 資料表的清單（適用于資料庫）|資料庫 &#124; 資料表

## <a name="alter-policy"></a>改變原則

### <a name="examples"></a>範例

#### <a name="1-setting-all-properties-of-the-policy-explicitly-at-table-level"></a>1. 在資料表層級明確設定原則的所有屬性：

```kusto
.alter table [table_name] policy merge 
@'{'
    '"ExtentSizeTargetInMb": 1024,'
    '"OriginalSizeInMbUpperBoundForRebuild": 2048,'
    '"OriginalSizeInMbUpperBoundForMerge": 4096,'
    '"RowCountUpperBoundForRebuild": 750000,'
    '"RowCountUpperBoundForMerge": 0,'
    '"MaxExtentsToMerge": 100,'
    '"LoopPeriod": "01:00:00",'
    '"MaxRangeInHours": 8,'
    '"AllowRebuild": true,'
    '"AllowMerge": true '
'}'
```

#### <a name="2-setting-all-properties-of-the-policy-explicitly-at-database-level"></a>2. 在資料庫層級明確設定原則的所有屬性：

```kusto
.alter database [database_name] policy merge 
@'{'
    '"ExtentSizeTargetInMb": 1024,'
    '"OriginalSizeInMbUpperBoundForRebuild": 2048,'
    '"OriginalSizeInMbUpperBoundForMerge": 4096,'
    '"RowCountUpperBoundForRebuild": 750000,'
    '"RowCountUpperBoundForMerge": 0,'
    '"MaxExtentsToMerge": 100,'
    '"LoopPeriod": "01:00:00",'
    '"MaxRangeInHours": 8,'
    '"AllowRebuild": true,'
    '"AllowMerge": true'
'}'
```

#### <a name="3-setting-the-default-merge-policy-at-database-level"></a>3. 在資料庫層級設定*預設*的合併原則：

```kusto
.alter database [database_name] policy merge '{}'
```

#### <a name="4-altering-a-single-property-of-the-policy-at-database-level-keeping-all-other-properties-as-is"></a>4. 在資料庫層級改變原則的單一屬性，並保持所有其他屬性的一致：

```kusto
.alter-merge database [database_name] policy merge
@'{'
    '"ExtentSizeTargetInMb": 1024'
'}'
```

#### <a name="5-altering-a-single-property-of-the-policy-at-table-level-keeping-all-other-properties-as-is"></a>5. 在資料表層級改變原則的單一屬性，並保持所有其他屬性的一致：

```kusto
.alter-merge table [table_name] policy merge
@'{'
    '"RowCountUpperBoundForRebuild": 750000'
'}'
```

上述所有專案都會傳回實體（指定為限定名稱的資料庫或資料表）的更新範圍合併原則作為其輸出。

對原則所做的變更可能需要最多1小時才會生效。

## <a name="delete-policy-of-merge"></a>刪除合併的原則

```kusto
.delete table [table_name] policy merge

.delete database [database_name] policy merge

```

命令會刪除指定實體的目前合併原則。
