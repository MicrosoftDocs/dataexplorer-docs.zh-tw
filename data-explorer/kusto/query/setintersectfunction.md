---
title: set_intersect() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的set_intersect()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/02/2019
ms.openlocfilehash: 0a1ef86573a408f644e26b3b23f0db42e327573a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507747"
---
# <a name="set_intersect"></a>set_intersect()

傳回`dynamic`所有 陣列中所有不同值集 (JSON) 陣列 - (arr1 [ arr2 ) ...

**語法**

`set_intersect(`*arr1*`, `*arr2*`[`,` *arr3*, ...])`

**引數**

* *阿爾1...arrN*: 輸入陣列以創建相交集(至少兩個陣列)。 所有參數都必須是動態陣列(請參閱[pack_array](packarrayfunction.md))。 

**傳回**

返回所有陣列中所有不同值集的動態陣列。 請參考[`set_union()`](setunionfunction.md)[`set_difference()`](setdifferencefunction.md)與 。

**範例**

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
|[1]|
|[2]|
|[3]|

```kusto
print arr = set_intersect(dynamic([1, 2, 3]), dynamic([4,5]))
```

|阿爾爾|
|---|
|[]|