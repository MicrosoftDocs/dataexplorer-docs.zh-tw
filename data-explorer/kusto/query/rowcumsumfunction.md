---
title: 'row_cumsum ( # A1-Azure 資料總管 |Microsoft Docs'
description: '本文描述 Azure 資料總管中 ( # A1 row_cumsum。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6ad1df20972238bee17217f5d9de19a020b4cbce
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242847"
---
# <a name="row_cumsum"></a>row_cumsum()

計算已序列化之資料列 [集中資料行](./windowsfunctions.md#serialized-row-set)的累計總和。

## <a name="syntax"></a>語法

`row_cumsum``(`*詞彙*[ `,` *重新開機*]`)`

* 「詞彙」（ *Term* ）是指出要加總的值的運算式。
  運算式必須是下列其中一種類型的純量： `decimal` 、 `int` 、 `long` 或 `real` 。 Null *詞彙* 值不會影響總和。
* *Restart* 是類型的選擇性引數 `bool` ，可指出何時應重新開機累積作業 (設定回 0) 。 可以用來表示資料的資料分割;請參閱下列第二個範例。

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

此範例示範如何在這裡計算資料分割 (的) 累計總和 (， `salary` 方法是 `name`) ：

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