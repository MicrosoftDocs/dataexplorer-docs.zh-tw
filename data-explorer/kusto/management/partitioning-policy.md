---
title: 資料分割區策略管理 - Azure 資料資源管理員 |微軟文件
description: 本文介紹了 Azure 數據資源管理器中的數據分區策略管理。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/04/2020
ms.openlocfilehash: 0e1ff783195f26adf7f98e511ca155f43609098c
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663957"
---
# <a name="data-partitioning-policy-management"></a>資料分割區原則管理

[此處](../management/partitioningpolicy.md)詳細介紹了數據分區策略。

## <a name="show-policy"></a>顯示策略

```kusto
.show table [table_name] policy partitioning
```

該`.show`命令顯示應用於表的分區策略。

### <a name="output"></a>輸出

|原則名稱 | 實體名稱 | 原則 | 子實體 | 實體類型
|---|---|---|---|---
|資料分割區 | 資料表名稱 | 原則物件的 JSON 序列化 | null | Table

## <a name="alter-and-alter-merge-policy"></a>變更與變更合併政策

```kusto
.alter table [table_name] policy partitioning @'policy object, serialized as JSON'

.alter-merge table [table_name] policy partitioning @'partial policy object, serialized as JSON'
```

該`.alter`命令允許更改應用於表的分區策略。

該命令需要[資料庫管理員](access-control/role-based-authorization.md)許可權。

對策略的更改最多可能需要 1 小時才能生效。

### <a name="examples"></a>範例

#### <a name="setting-all-properties-of-the-policy-explicitly-at-table-level"></a>在表層級設定策略的所有屬性

```kusto
.alter table [table_name] policy partitioning @'{'
  '"PartitionKeys": ['
    '{'
      '"ColumnName": "my_string_column",'
      '"Kind": "Hash",'
      '"Properties": {'
        '"Function": "XxHash64",'
        '"MaxPartitionCount": 256,'
      '}'
    '},'
    '{'
      '"ColumnName": "my_datetime_column",'
      '"Kind": "UniformRange",'
      '"Properties": {'
        '"Reference": "1970-01-01T00:00:00",'
        '"RangeSize": "1.00:00:00"'
      '}'
    '}'
  ']'
'}'
```

#### <a name="setting-a-specific-property-of-the-policy-explicitly-at-table-level"></a>在表層級設定策略的特定屬性

要將`EffectiveDateTime`政策設定為其他值,請使用以下指令:

```kusto
.alter table [table_name] policy partitioning @'{"EffectiveDateTime":"2020-01-01"}'
```

## <a name="delete-policy"></a>移除原則

```kusto
.delete table [table_name] policy partitioning
```

該`.delete`命令刪除給定表的分區策略。
