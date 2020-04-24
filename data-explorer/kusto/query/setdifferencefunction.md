---
title: set_difference() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的 set_difference()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/02/2019
ms.openlocfilehash: d4edb8ec46fca99b7dd58b11bbd54442a9340c7a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507798"
---
# <a name="set_difference"></a>set_difference()

返回第`dynamic`一個陣列中但不在其他陣列中的所有不同值集的 (JSON) 陣列 - ((((arr1 = arr2) = arr3 = ...)。

**語法**

`set_difference(`*arr1*`, `*arr2*`[`,` *arr3*, ...])`

**引數**

* *阿爾1...arrN*: 輸入陣列以建立一個差異集(至少兩個陣列)。 所有參數都必須是動態陣列(請參閱[pack_array](packarrayfunction.md))。 

**傳回**

返回 arr1 中但不在其他陣列中的所有不同值集的動態陣列。 請參考[`set_union()`](setunionfunction.md)[`set_intersect()`](setintersectfunction.md)與 。

**範例**

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
|[8]|
|[12]|

```kusto
print arr = set_difference(dynamic([1,2,3]), dynamic([1,2,3]))
```

|阿爾爾|
|---|
|[]|