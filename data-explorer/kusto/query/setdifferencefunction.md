---
title: set_difference （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 set_difference （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/02/2019
ms.openlocfilehash: 7e13a9b652e1bdadb325cd866bddd78761b25b85
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372385"
---
# <a name="set_difference"></a>set_difference()

傳回 `dynamic` 第一個陣列中但不在其他陣列中之所有相異值集合的（JSON）陣列-（（（arr1 \ arr2） \ arr3） \ ...）。

**語法**

`set_difference(`*arr1* `, `*arr2* `[` ，` *arr3*, ...])`

**引數**

* *arr1 .。。arrN*：輸入陣列以建立差異集合（至少兩個數組）。 所有引數都必須是動態陣列（請參閱[pack_array](packarrayfunction.md)）。 

**傳回**

傳回 arr1 中但不在其他陣列中的所有相異值集合的動態陣列。 請參閱 [`set_union()`](setunionfunction.md) 和 [`set_intersect()`](setintersectfunction.md) 。

**範例**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| extend w = z * 2
| extend a1 = pack_array(x,y,x,z), a2 = pack_array(x, y), a3 = pack_array(x,y,w)
| project set_difference(a1, a2, a3)
```

|Column1|
|---|
|[4]|
|8|
|12|

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr = set_difference(dynamic([1,2,3]), dynamic([1,2,3]))
```

|arr|
|---|
|[]|