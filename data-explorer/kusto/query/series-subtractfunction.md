---
title: series_subtract （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 series_subtract （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 388f24f12993bcdc91d86bfc3f3f20967e0b1cc5
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372425"
---
# <a name="series_subtract"></a>series_subtract()

計算兩個數值數列輸入的元素的減法。

**語法**

`series_subtract(`*series1* `,`*series2*`)`

**引數**

* *series1，series2*：輸入數值陣列，第二個是元素從第一個到動態陣列結果的減法。 所有引數都必須是動態陣列。 

**傳回**

在兩個輸入之間的計算專案的動態減法運算的動態陣列。 任何非數值元素或不存在的專案（不同大小的陣列）都會產生 `null` 元素值。

**範例**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| project s1 = pack_array(x,y,z), s2 = pack_array(z, y, x)
| extend s1_subtract_s2 = series_subtract(s1, s2)
```

|s1|s2|s1_subtract_s2|
|---|---|---|
|[1，2，4]|[4，2，1]|[-3，0，3]|
|[2，4，8]|[8，4，2]|[-6，0，6]|
|[3，6，12]|[12，6，3]|[-9，0，9]|
