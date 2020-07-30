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
ms.openlocfilehash: 709da983599b4e8b0c8b06cf7bff4276ba03b5cf
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351160"
---
# <a name="set_intersect"></a>set_intersect()

傳回 `dynamic` 所有陣列中所有相異值集合的陣列-（arr1 ∩ arr2 ∩ ...）。

## <a name="syntax"></a>語法

`set_intersect(`*arr1* `, `*arr2* `[` ，` *arr3*, ...])`

## <a name="arguments"></a>引數

* *arr1 .。。arrN*：輸入陣列以建立交集集合（至少兩個數組）。 所有引數都必須是動態陣列。 如需詳細資訊，請參閱[pack_array](packarrayfunction.md)。 

## <a name="returns"></a>傳回

傳回所有陣列中所有相異值集合的動態陣列。 請參閱 [`set_union()`](setunionfunction.md) 和 [`set_difference()`](setdifferencefunction.md) 。

## <a name="example"></a>範例

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
