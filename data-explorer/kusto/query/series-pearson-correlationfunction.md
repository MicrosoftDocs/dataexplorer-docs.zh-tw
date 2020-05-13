---
title: series_pearson_correlation （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 series_pearson_correlation （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/31/2019
ms.openlocfilehash: 9187c10ad62b4d925bf6211e64657fba5ae17b63
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372513"
---
# <a name="series_pearson_correlation"></a>series_pearson_correlation()

計算兩個數值數列輸入的皮耳森相互關聯係數。

請參閱：[皮耳森相互關聯係數](https://en.wikipedia.org/wiki/Pearson_correlation_coefficient)。

**語法**

`series_pearson_correlation(`*Series1* `,`*Series2*`)`

**引數**

* *Series1，Series2*：輸入用於計算相互關聯係數的數值陣列。 所有引數都必須是相同長度的動態陣列。 

**傳回**

兩個輸入之間的計算皮耳森相互關聯係數。 任何非數值元素或不存在的專案（不同大小的陣列）都會產生 `null` 結果。

**範例**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range s1 from 1 to 5 step 1 | extend s2 = 2*s1 // Perfect correlation
| summarize s1 = make_list(s1), s2 = make_list(s2)
| extend correlation_coefficient = series_pearson_correlation(s1,s2)
```

|s1|s2|correlation_coefficient|
|---|---|---|
|[1，2，3，4，5]|[2，4，6，8，10]|1|
