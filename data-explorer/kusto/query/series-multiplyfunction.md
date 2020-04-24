---
title: series_multiply)- Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的series_multiply()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: fa000d1058730e0232790e7f0e3976fa203519c0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81508206"
---
# <a name="series_multiply"></a>series_multiply()

計算兩個數位系列輸入的元乘法。

**語法**

`series_multiply(`*列1*`,`*系列2*`)`

**引數**

* *系列1,系列2:* 輸入數值陣列,按元素乘以動態數位結果。 所有參數必須是動態陣組。 

**傳回**

兩個輸入之間計算元素乘法操作的動態陣列。 任何非數位元素或不存在的元素(不同大小的陣列)都生成`null`元素值。

**範例**

```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| project s1 = pack_array(x,y,z), s2 = pack_array(z, y, x)
| extend s1_multiply_s2 = series_multiply(s1, s2)
```

|s1         |s2|        s1_multiply_s2|
|---|---|---|
|[1,2,4]    |[4,2,1]|   [4,4,4]|
|[2,4,8]    |[8,4,2]|   [16,16,16]|
|[3,6,12]   |[12,6,3]|  [36,36,36]|