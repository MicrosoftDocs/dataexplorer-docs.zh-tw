---
title: set_union() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的set_union()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/02/2019
ms.openlocfilehash: de9220ea6f9e23e458dd379fb164317842a48c94
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507662"
---
# <a name="set_union"></a>set_union()

返回任何`dynamic`陣列中所有不同值集的 (JSON) 陣列 - (arr1 [ arr2 [ ...)。

**語法**

`set_union(`*arr1*`, `*arr2*`[`,` *arr3*, ...]``)`

**引數**

* *阿爾1...arrN*: 輸入陣列以創建一個聯合集(至少兩個陣列)。 所有參數都必須是動態陣列(請參閱[pack_array](packarrayfunction.md))。 

**傳回**

返回任何陣列中所有不同值集的動態陣列。 請參考[`set_intersect()`](setintersectfunction.md)[`set_difference()`](setdifferencefunction.md)與 。

**範例**

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
|[1,2,4,8]|
|[2,4,8,16]|
|[3,6,12,24]|