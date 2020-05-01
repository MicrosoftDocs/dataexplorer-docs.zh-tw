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
ms.openlocfilehash: 1ad9b359422b51084f1be1c64d27d656313d9296
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82616315"
---
# <a name="data-partitioning-policy-management"></a>資料分割原則管理

[這裡](../management/partitioningpolicy.md)詳述資料分割原則。

## <a name="show-policy"></a>顯示原則

```kusto
.show table [table_name] policy partitioning
```

此`.show`命令會顯示資料表上套用的資料分割原則。

### <a name="output"></a>輸出

|原則名稱 | 實體名稱 | 原則 | 子實體 | 實體類型
|---|---|---|---|---
|DataPartitioning | 資料表名稱 | 原則物件的 JSON 序列化 | null | Table

## <a name="alter-and-alter-merge-policy"></a>alter 和 alter-merge 原則

```kusto
.alter table [table_name] policy partitioning @'policy object, serialized as JSON'

.alter-merge table [table_name] policy partitioning @'partial policy object, serialized as JSON'
```

此`.alter`命令可讓您變更套用於資料表的資料分割原則。

此命令需要[DatabaseAdmin](access-control/role-based-authorization.md)許可權。

對原則所做的變更可能需要最多1小時才會生效。

### <a name="examples"></a>範例

#### <a name="setting-all-properties-of-the-policy-explicitly-at-table-level"></a>在資料表層級明確設定原則的所有屬性

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

#### <a name="setting-a-specific-property-of-the-policy-explicitly-at-table-level"></a>在資料表層級明確設定原則的特定屬性

若要將`EffectiveDateTime`原則的設定為不同的值，請使用下列命令：

```kusto
.alter-merge table [table_name] policy partitioning @'{"EffectiveDateTime":"2020-01-01"}'
```

## <a name="delete-policy"></a>刪除原則

```kusto
.delete table [table_name] policy partitioning
```

`.delete`命令會刪除給定資料表的資料分割原則。
