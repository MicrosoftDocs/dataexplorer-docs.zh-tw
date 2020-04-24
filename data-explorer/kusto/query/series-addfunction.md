---
title: series_add() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的series_add()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: ea8c391060e487413a166c236abf789edcba0a0c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509056"
---
# <a name="series_add"></a>series_add()

計算兩個數位系列輸入的元素級添加。

**語法**

`series_add(`*列1*`,`*系列2*`)`

**引數**

* *系列1,系列2:* 輸入數值陣列,以元素為物件添加到動態陣列結果中。 所有參數必須是動態陣組。 

**傳回**

計算元素動態陣列,在兩個輸入之間添加操作。 任何非數位元素或不存在的元素(不同大小的陣列)都生成`null`元素值。

**範例**

```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| project s1 = pack_array(x,y,z), s2 = pack_array(z, y, x)
| extend s1_add_s2 = series_add(s1, s2)
```

|s1|s2|s1_add_s2|
|---|---|---|
|[1,2,4]|[4,2,1]|[5,4,5]|
|[2,4,8]|[8,4,2]|[10,8,10]|
|[3,6,12]|[12,6,3]|[15,12,15]|