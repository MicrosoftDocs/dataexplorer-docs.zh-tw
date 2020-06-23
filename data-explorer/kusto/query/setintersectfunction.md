---
title: set_intersect （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 set_intersect （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/02/2019
ms.openlocfilehash: f611f660a791117159ad5fce4c024914d9e6909b
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2020
ms.locfileid: "85264737"
---
# <a name="set_intersect"></a>set_intersect()

傳回 `dynamic` 所有陣列中所有相異值集合的陣列-（arr1 ∩ arr2 ∩ ...）。

**語法**

`set_intersect(`*arr1* `, `*arr2* `[` ，` *arr3*, ...])`

**引數**

* *arr1 .。。arrN*：輸入陣列以建立交集集合（至少兩個數組）。 所有引數都必須是動態陣列（請參閱[pack_array](packarrayfunction.md)）。 

**傳回**

傳回所有陣列中所有相異值集合的動態陣列。 請參閱 [`set_union()`](setunionfunction.md) 和 [`set_difference()`](setdifferencefunction.md) 。

**範例**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| extend w = z * 2
| extend a1 = pack_array(x,y,x,z), a2 = pack_array(x, y), a3 = pack_array(w,x)
| project set_intersect(a1, a2, a3)
```

|Column1|
|---|
| [1]|
|2|
|[3]|

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr = set_intersect(dynamic([1, 2, 3]), dynamic([4,5]))
```

|arr|
|---|
|[]|
