---
title: row_cumsum （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 row_cumsum （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 83dc48589fce7332c8e24d1e5a47c75a6cfca608
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87345720"
---
# <a name="row_cumsum"></a>row_cumsum()

計算序列化資料列[集中資料行](./windowsfunctions.md#serialized-row-set)的累計總和。

## <a name="syntax"></a>語法

`row_cumsum``(`*詞彙*[ `,` *重新開機*]`)`

* *詞彙*是運算式，表示要加總的值。
  運算式必須是下列其中一種類型的純量： `decimal` 、 `int` 、 `long` 或 `real` 。 Null*詞彙*值不會影響總和。
* *Restart*是類型的選擇性引數 `bool` ，表示何時應重新開機累積作業（設定回0）。 它可以用來表示資料的分割區;請參閱下列第二個範例。

## <a name="returns"></a>傳回

函數會傳回其引數的累計總和。

## <a name="examples"></a>範例

下列範例顯示如何計算前幾個偶數整數的累計總和。

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

這個範例示範如何計算資料分割時的累計總和（此處的 `salary` ） `name` ：

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