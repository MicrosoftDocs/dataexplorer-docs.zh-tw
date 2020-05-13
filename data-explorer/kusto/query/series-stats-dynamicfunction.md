---
title: series_stats_dynamic （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 series_stats_dynamic （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/10/2020
ms.openlocfilehash: 87cee5244fb1276733d4cf44d0477cc3351b947c
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372462"
---
# <a name="series_stats_dynamic"></a>series_stats_dynamic()

傳回動態物件中數列的統計資料。  

函式 `series_stats_dynamic()` 會採用包含動態數值陣列做為輸入的資料行，並使用下列內容產生動態值：
* `min`：輸入陣列中的最小值
* `min_idx`：輸入陣列中最小值的第一個位置
* `max`：輸入陣列中的最大值
* `max_idx`：輸入陣列中最大值的第一個位置
* `avg`：輸入陣列的平均值
* `variance`：輸入陣列的樣本變異數
* `stdev`：輸入陣列的樣本標準差

**語法**

`series_stats_dynamic(`*x* `[,` *ignore_nonfinite*`])`

**引數**

* *x*：動態陣列資料格，這是數值的陣列。 
* *ignore_nonfinite*： Boolean （選擇性，預設值： `false` ）旗標，指定是否要計算統計資料，同時忽略非有限值（*null*、 *NaN*、 *inf*等等）。 如果設定為 `false` 傳回的結果，則為， `null` 如果陣列中有非有限的值，則為。

**範例**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print x=dynamic([23,46,23,87,4,8,3,75,2,56,13,75,32,16,29]) 
| project stats=series_stats_dynamic(x)
```

|stats
|---|
|{「分鐘」：2.0、「min_idx」：8、「最大值」：87.0、「max_idx」：3、「平均」：32.8、「stdev」：28.503633853548269、「變異數」： 812.45714285714291}
