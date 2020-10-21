---
title: 'set_union ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 set_union。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/02/2019
ms.openlocfilehash: 2fff5763f7eba13e48d0cbdb0e85af666c385308
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242632"
---
# <a name="set_union"></a>set_union()

傳回 `dynamic` 所有位於陣列中的相異值集合的陣列- (arr1 ∪ arr2 ∪ ... ) 。

## <a name="syntax"></a>語法

`set_union(`*arr1* `, `*arr2* `[` 、` *arr3*, ...]``)`

## <a name="arguments"></a>引數

* *arr1 .。。arrN*：輸入陣列來建立聯集 (至少有兩個數組) 。 所有引數都必須是動態陣列 (請參閱 [pack_array](packarrayfunction.md)) 。 

## <a name="returns"></a>傳回

傳回所有陣列中所有相異值集合的動態陣列。 請參閱 [`set_intersect()`](setintersectfunction.md)  和 [`set_difference()`](setdifferencefunction.md) 。

## <a name="example"></a>範例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| extend w = z * 2
| extend a1 = pack_array(x,y,x,z), a2 = pack_array(x, y), a3 = pack_array(w)
| project set_union(a1, a2, a3)
```

|資料行1|
|---|
|[1、2、4、8]|
|[2，4，8，16]|
|[3，6，12，24]|
