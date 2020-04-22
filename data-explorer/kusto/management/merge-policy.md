---
title: 合併策略管理 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的合併策略管理。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4093c9c09e4e268bc38cabdc6da27f0ac2ee17ab
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81664014"
---
# <a name="merge-policy-management"></a>合併原則管理

## <a name="show-policy"></a>顯示策略

```kusto
.show table [table_name] policy merge

.show table * policy merge

.show database [database_name] policy merge

.show database * policy merge
```

顯示資料庫或表的當前合併策略。
如果給定名稱為"*",則顯示給定實體類型(資料庫或表)的所有策略。

### <a name="output"></a>輸出

|原則名稱 | 實體名稱 | 原則 | 子實體 | 實體類型
|---|---|---|---|---
|範圍合併原則 | 資料庫/表名稱 | 表示策略的 JSON 格式字串 | 表列表(用於資料庫)|資料庫&#124;表

## <a name="alter-policy"></a>變更原則

### <a name="examples"></a>範例

#### <a name="1-setting-all-properties-of-the-policy-explicitly-at-table-level"></a>1. 在表等級顯示式設定策略的所有屬性:

```kusto
.alter table [table_name] policy merge 
@'{'
    '"ExtentSizeTargetInMb": 1024,'
    '"OriginalSizeInMbUpperBoundForRebuild": 2048,'
    '"RowCountUpperBoundForRebuild": 750000, '
    '"RowCountUpperBoundForMerge": 0, '
    '"MaxExtentsToMerge": 100, '
    '"LoopPeriod": "01:00:00", '
    '"MaxRangeInHours": 8, '
    '"AllowRebuild": true,'
    '"AllowMerge": true '
'}'
```

#### <a name="2-setting-all-properties-of-the-policy-explicitly-at-database-level"></a>2. 在資料庫等級顯示式設定策略的所有屬性:

```kusto
.alter database [database_name] policy merge 
@'{'
    '"ExtentSizeTargetInMb": 1024,'
    '"OriginalSizeInMbUpperBoundForRebuild": 2048,'
    '"RowCountUpperBoundForRebuild": 750000,'
    '"RowCountUpperBoundForMerge": 0,'
    '"MaxExtentsToMerge": 100,'
    '"LoopPeriod": "01:00:00",'
    '"MaxRangeInHours": 8,'
    '"AllowRebuild": true,'
    '"AllowMerge": true'
'}'
```

#### <a name="3-setting-the-default-merge-policy-at-database-level"></a>3. 在資料庫等級設定*預設*合併政策:

```kusto
.alter database [database_name] policy merge '{}'
```

#### <a name="4-altering-a-single-property-of-the-policy-at-database-level-keeping-all-other-properties-as-is"></a>4. 在資料庫等級更改策略的單個屬性,保持所有其他屬性的正樣:

```kusto
.alter-merge database [database_name] policy merge
@'{'
    '"ExtentSizeTargetInMb": 1024'
'}'
```

#### <a name="5-altering-a-single-property-of-the-policy-at-table-level-keeping-all-other-properties-as-is"></a>5. 在表等級變更策略的單個屬性,保持所有其他屬性不變:

```kusto
.alter-merge table [table_name] policy merge
@'{'
    '"RowCountUpperBoundForRebuild": 750000'
'}'
```

上述所有內容都返回實體(指定為限定名稱的資料庫或表)的更新擴展區合併策略作為輸出。

對策略的更改最多可能需要 1 小時才能生效。

## <a name="delete-policy-of-merge"></a>刪除合併政策

```kusto
.delete table [table_name] policy merge

.delete database [database_name] policy merge

```

該命令刪除給定實體的當前合併策略。
