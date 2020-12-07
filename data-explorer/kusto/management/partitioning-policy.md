---
title: 資料分割原則管理-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的資料分割原則管理。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/04/2020
ms.openlocfilehash: f34042af4578643f8b1b4120345cb37f66b71830
ms.sourcegitcommit: a36981785765b85a961f275be24d297d38e498fd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96310012"
---
# <a name="partitioning-policy-command"></a>資料分割原則命令

[此處](../management/partitioningpolicy.md)詳述資料分割原則。

## <a name="show-policy"></a>顯示原則

```kusto
.show table [table_name] policy partitioning
```

此 `.show` 命令會顯示套用於資料表上的資料分割原則。

### <a name="output"></a>輸出

|原則名稱 | 實體名稱 | 原則 | 子實體 | 實體類型
|---|---|---|---|---
|DataPartitioning | 資料表名稱 | 原則物件的 JSON 序列化 | null | 資料表

## <a name="alter-and-alter-merge-policy"></a>alter 和 alter-merge 原則

```kusto
.alter table [table_name] policy partitioning @'policy object, serialized as JSON'

.alter-merge table [table_name] policy partitioning @'partial policy object, serialized as JSON'
```

此 `.alter` 命令可讓您變更套用在資料表上的資料分割原則。

此命令需要 [DatabaseAdmin](access-control/role-based-authorization.md) 許可權。

原則的變更最多可能需要1小時的時間才會生效。

### <a name="examples"></a>範例

#### <a name="setting-a-policy-with-a-hash-partition-key"></a>使用雜湊分割索引鍵設定原則

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
    '}'
  ']'
'}'
```

#### <a name="setting-a-policy-with-a-uniform-range-datetime-partition-key"></a>設定具有統一範圍 datetime 分割區索引鍵的原則

```kusto
.alter table [table_name] policy partitioning @'{'
  '"PartitionKeys": ['
    '{'
      '"ColumnName": "my_datetime_column",'
      '"Kind": "UniformRange",'
      '"Properties": {'
        '"Reference": "1970-01-01T00:00:00",'
        '"RangeSize": "1.00:00:00"'
        '"OverrideCreationTime": false'
      '}'
    '}'
  ']'
'}'
```

#### <a name="setting-a-policy-with-both-kinds-of-partition-keys"></a>以這兩種分割區索引鍵設定原則

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
        '"OverrideCreationTime": false'
      '}'
    '}'
  ']'
'}'
```

#### <a name="setting-a-specific-property-of-the-policy-explicitly-at-table-level"></a>在資料表層級明確設定原則的特定屬性

若要將 `EffectiveDateTime` 原則的設定為不同的值，請使用下列命令：

```kusto
.alter-merge table [table_name] policy partitioning @'{"EffectiveDateTime":"2020-01-01"}'
```

## <a name="delete-policy"></a>刪除原則

```kusto
.delete table [table_name] policy partitioning
```

此 `.delete` 命令會刪除給定資料表的分割原則。
