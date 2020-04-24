---
title: row_cumsum() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的row_cumsum()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 92ebec75dcd7e44d59f964dc735e857f22f1ad00
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510212"
---
# <a name="row_cumsum"></a>row_cumsum()

計算[序列化行集中](./windowsfunctions.md#serialized-row-set)列的累積總和。

**語法**

`row_cumsum``(`*術語*`,`=*重新啟動*|`)`

* *術語*是指示要求和的值的表達式。
  表示式必須是`decimal`以下類型之一的標量: `int``long`、`real`或 。 空*術語*值不會影響總和。
* *重新啟動*是一種可選的`bool`類型 參數,指示何時應重新啟動累積操作(設置回 0)。 它可用於指示數據的分區;請參閱下面的第二個範例。

**傳回**

函數返回其參數的累積總和。

**範例**

下面的示例演示如何計算前幾個偶整數的累積和數。

```kusto
datatable (a:long) [
    1, 2, 3, 4, 5, 6, 7, 8, 9, 10
]
| where a%2==0
| serialize cs=row_cumsum(a)
```

a    | cs
-----|-----
2    | 2
4    | 6
6    | 12
8    | 20
10   | 30

此範例展示如何在資料分割區時計算`salary`的累積總和(此處, 的 ) (`name`此處, 通過 )

```kusto
datatable (name:string, month:int, salary:long)
[
    "Alice", 1, 1000,
    "Bob",   1, 1000,
    "Alice", 2, 2000,
    "Bob",   2, 1950,
    "Alice", 3, 1400,
    "Bob",   3, 1450,
]
| order by name asc, month asc
| extend total=row_cumsum(salary, name != prev(name))
```

NAME   | 月  | 工資  | total
-------|--------|---------|------
Alice  | 1      | 1000    | 1000
Alice  | 2      | 2000    | 3000
Alice  | 3      | 1400    | 4400
Bob    | 1      | 1000    | 1000
Bob    | 2      | 1950    | 2950
Bob    | 3      | 1450    | 4400