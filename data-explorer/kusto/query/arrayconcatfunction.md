---
title: array_concat （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 array_concat （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 681178cc12d145b1c574357e87ae4f7b33d736c4
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225644"
---
# <a name="array_concat"></a>array_concat()

將數個動態陣列串連成單一陣列。

**語法**

`array_concat(`*arr1* `[` ， ` *arr2*, ...]` ） '

**引數**

* *arr1 .。。arrN*：要串連成動態陣列的輸入陣列。 所有引數都必須是動態陣列（請參閱[pack_array](packarrayfunction.md)）。 

**傳回**

具有 arr1、arr2、...、arrN 之陣列的動態陣列。

**範例**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| extend a1 = pack_array(x,y,z), a2 = pack_array(x, y)
| project array_concat(a1, a2)
```

|Column1|
|---|
|[1，2，4，1，2]|
|[2，4，8，2，4]|
|[3，6，12，3，6]|
