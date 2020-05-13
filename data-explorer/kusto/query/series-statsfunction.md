---
title: series_stats （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 series_stats （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/10/2020
ms.openlocfilehash: 3fe88a5d53faaca4512d614d3e62204ac26e6fc5
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372438"
---
# <a name="series_stats"></a>series_stats()

`series_stats()`傳回多個資料行中數列的統計資料。  

函式 `series_stats()` 會採用包含動態數值陣列做為輸入的資料行，並計算下列資料行：
* `min`：輸入陣列中的最小值
* `min_idx`：輸入陣列中最小值的第一個位置
* `max`：輸入陣列中的最大值
* `max_idx`：輸入陣列中最大值的第一個位置
* `avg`：輸入陣列的平均值
* `variance`：輸入陣列的樣本變異數
* `stdev`：輸入陣列的樣本標準差

> [!NOTE] 
> 此函式會傳回多個資料行，因此不能當做另一個函數的引數使用。

**語法**

project `series_stats(` *x* `[,` *ignore_nonfinite* `])` 或 extend `series_stats(` *x*會傳回 `)` 前述所有具有下列名稱的資料行： series_stats_x_min、series_stats_x_min_idx 等等。
 
專案（m，mi） = `series_stats(` *x* `)` 或 extend （m，mi） = `series_stats(` *x*會傳回 `)` 下列資料行： m （min）和 mi （min_idx）。

**引數**

* *x*：動態陣列資料格，這是數值的陣列。 
* *ignore_nonfinite*： Boolean （選擇性，預設值： `false` ）旗標，指定是否要計算統計資料，同時忽略非有限值（*null*、 *NaN*、 *inf*等等）。 如果設定為 `false` ，則傳回的值會是 `null` 陣列中是否有非有限值。

**範例**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print x=dynamic([23,46,23,87,4,8,3,75,2,56,13,75,32,16,29]) 
| project series_stats(x)

```

|series_stats_x_min|series_stats_x_min_idx|series_stats_x_max|series_stats_x_max_idx|series_stats_x_avg|series_stats_x_stdev|series_stats_x_variance|
|---|---|---|---|---|---|---|
|2|8|87|3|32.8|28.5036338535483|812.457142857143|
