---
title: series_equals （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 series_equals （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: 85c6df85814d4b4aa4e1b00786d3d206d02116cd
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372833"
---
# <a name="series_equals"></a>series_equals()

計算 `==` 兩個數值數列輸入的元素成對 equals （）邏輯運算。

**語法**

`series_equals (`*Series1* `,`*Series2*`)`

**引數**

* *Series1，Series2*：輸入要比較元素的數值陣列。 所有引數都必須是動態陣列。 

**傳回**

布林值的動態陣列，其中包含兩個輸入之間的計算元素成對邏輯運算。 任何非數值元素或不存在的專案（不同大小的陣列）都會產生 `null` 元素值。

**範例**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print s1 = dynamic([1,2,4]), s2 = dynamic([4,2,1])
| extend s1_equals_s2 = series_equals(s1, s2)
```

|s1|s2|s1_equals_s2|
|---|---|---|
|[1，2，4]|[4，2，1]|[false，true，false]|

**另請參閱**

如需整個數列統計資料的比較，請參閱：
* [series_stats()](series-statsfunction.md)
* [series_stats_dynamic()](series-stats-dynamicfunction.md)
