---
title: set_union （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 set_union （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/02/2019
ms.openlocfilehash: 7e719ffa448ce3060a0a520de2c031b7d4b7d109
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372343"
---
# <a name="set_union"></a>set_union()

傳回 `dynamic` 任何陣列中所有相異值集合的（JSON）陣列-（arr1 ∪ arr2 ∪ ...）。

**語法**

`set_union(`*arr1* `, `*arr2* `[` ，` *arr3*, ...]``)`

**引數**

* *arr1 .。。arrN*：輸入陣列以建立聯集（至少兩個數組）。 所有引數都必須是動態陣列（請參閱[pack_array](packarrayfunction.md)）。 

**傳回**

傳回任何陣列中所有相異值集合的動態陣列。 請參閱 [`set_intersect()`](setintersectfunction.md) 和 [`set_difference()`](setdifferencefunction.md) 。

**範例**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| extend w = z * 2
| extend a1 = pack_array(x,y,x,z), a2 = pack_array(x, y), a3 = pack_array(w)
| project set_union(a1, a2, a3)
```

|Column1|
|---|
|[1，2，4，8]|
|[2，4，8，16]|
|[3，6，12，24]|
