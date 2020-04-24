---
title: series_divide() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的series_divide()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 8e8b806c325da9bfce5f79ce5a5c4e5cfadaa838
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81508835"
---
# <a name="series_divide"></a>series_divide()

計算兩個數位系列輸入的元素劃分。

**語法**

`series_divide(`*列1*`,`*系列2*`)`

**引數**

* *系列1,系列2:* 輸入數值陣列,第一個按元素將第二個劃分為動態陣組結果。 所有參數必須是動態陣組。 

**傳回**

兩個輸入之間計算元素的按點操作的動態陣列。 任何非數位元素或不存在的元素(不同大小的陣列)都生成`null`元素值。

注意:即使輸入是整數,結果序列也是雙類型。 除以零後,雙除以零(例如 2/0 生成雙(\inf)。"

**範例**

```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| project s1 = pack_array(x,y,z), s2 = pack_array(z, y, x)
| extend s1_divide_s2 = series_divide(s1, s2)
```

|s1         |s2|        s1_divide_s2|
|---|---|---|
|[1,2,4]    |[4,2,1]|   [0.25,1.0,4.0]|
|[2,4,8]    |[8,4,2]|   [0.25,1.0,4.0]|
|[3,6,12]   |[12,6,3]|  [0.25,1.0,4.0]|