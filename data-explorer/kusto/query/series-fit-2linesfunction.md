---
title: series_fit_2lines （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 series_fit_2lines （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 6c16b535962271a7aaf4acad63f52da028b49e33
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618731"
---
# <a name="series_fit_2lines"></a>series_fit_2lines()

在數列上套用兩個區段的線性回歸，並傳回多個資料行。  

採用包含動態數值陣列做為輸入的運算式，並套用[兩個區段的線性回歸](https://en.wikipedia.org/wiki/Segmented_regression)，以識別並量化數列中的趨勢變更。 函式會逐一查看數列索引，而在每個反復專案中，將數列分割為2個部分，將單獨的線條（使用[series_fit_line （）](series-fit-linefunction.md)）放入每個部分，並計算 r 平方的總數。 最佳分割是將 r 平方最大化；此函式會傳回其參數︰
* `rsquare`： [r-正方形](https://en.wikipedia.org/wiki/Coefficient_of_determination)是適合品質的標準量值。 這是在範圍 [0-1] 中的數字，其中 1 - 是最可能的配合，而 0 表示資料完全未依照順序，且不符合任何一條線
* `split_idx`：中中斷點對2區段的索引（以零為基底）
* `variance`：輸入資料的變異數
* `rvariance`：剩餘變異數，也就是輸入資料值與近似值（依2個線段）之間的差異。
* `line_fit`：數位陣列，其中包含一系列最適合行的值。 序列長度等於輸入陣列的長度。 它主要用於圖表。
* `right_rsquare`：分割右側線條的 r 平方，請參閱[series_fit_line （）](series-fit-linefunction.md)
* `right_slope`：右邊近似線的斜率（這是來自 y = ax + b 的 a）
* `right_interception`：攔截近似的左邊線（這是 b，y = ax + b）
* `right_variance`：分割右側輸入資料的變異數
* `right_rvariance`：分割右側輸入資料的剩餘變異數
* `left_rsquare`：分割左邊的線條 r 平方，請參閱[series_fit_line （）](series-fit-linefunction.md)
* `left_slope`：左側近似線的斜率（這是來自 y = ax + b 的 a）
* `left_interception`：攔截近似的左邊線（這是 b，y = ax + b）
* `left_variance`：分割左側輸入資料的變異數
* `left_rvariance`：分割左側輸入資料的剩餘變異數

*請注意*，此函式會傳回多個資料行，因此不能當做另一個函數的引數使用。

**語法**

專案`series_fit_2lines(` *x*`)`
* 會傳回所有前述具有下列名稱的資料行： series_fit_2lines_x_rsquare、series_fit_2lines_x_split_idx 等等。
project （rs，si，v） =`series_fit_2lines(`*x*`)`
* 會傳回下列資料行： rs （r-正方形）、si （分割索引）、v （變異數），而其餘部分看起來會像 series_fit_2lines_x_rvariance、series_fit_2lines_x_line_fit 等等。 extend （rs，si，v`series_fit_2lines(`） =*x*`)`
* 只會傳回︰rs (r-square)、si (split index) 和 v (variance)。
  
**引數**

* *x*：數值的動態陣列。  

> [!TIP]
> 使用此函式最方便的方式，是將它套用至[make 系列](make-seriesoperator.md)運算子的結果。

**範例**

```kusto
print id=' ', x=range(bin(now(), 1h)-11h, bin(now(), 1h), 1h), y=dynamic([1,2.2, 2.5, 4.7, 5.0, 12, 10.3, 10.3, 9, 8.3, 6.2])
| extend (Slope,Interception,RSquare,Variance,RVariance,LineFit)=series_fit_line(y), (RSquare2, SplitIdx, Variance2,RVariance2,LineFit2)=series_fit_2lines(y)
| project id, x, y, LineFit, LineFit2
| render timechart
```

:::image type="content" source="images/series-fit-2lines/series-fit-2lines.png" alt-text="數列符合2行":::
