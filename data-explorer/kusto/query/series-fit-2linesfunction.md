---
title: 'series_fit_2lines ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 series_fit_2lines。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: f056a14d7f1a88a7f4b77e01920ae2f00ae4bc45
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242100"
---
# <a name="series_fit_2lines"></a>series_fit_2lines()

在數列上套用兩個區段線性回歸，並傳回多個資料行。  

採用包含動態數值陣列的運算式做為輸入，並套用 [兩個區段線性回歸](https://en.wikipedia.org/wiki/Segmented_regression) ，以找出並量化數列中的趨勢變更。 函數會逐一查看數列索引。 在每個反復專案中，此函式會將數列分割成兩個部分， (使用 [series_fit_line ( # B2 ](series-fit-linefunction.md)) 個別的程式碼，並計算 r 平方總計。 最佳分割是將 r 平方最大化；此函式會傳回其參數︰


|參數  |描述  |
|---------|---------|
|`rsquare`     | [R 平方](https://en.wikipedia.org/wiki/Coefficient_of_determination) 是符合品質的標準量值。 它是範圍 [0-1] 中的數位，其中1是最適合的大小，0表示資料未排序且不符合任何一行。        |
|`split_idx`     |   中中斷點到兩個區段的索引， (以零為基礎的) 。      |
|`variance`     | 輸入資料的變異數。        |
|`rvariance`     | 剩餘變異數，也就是兩個線段)  (的輸入資料值之間的差異。        |
|`line_fit`     | 數值陣列，包含一系列最合適的行值。 序列長度等於輸入陣列的長度。 它主要用於製作圖表。        |
|`right_rsquare`     | 在分割右邊的行的 R-平方，請參閱 [series_fit_line ( # B1 ](series-fit-linefunction.md)。        |
|`right_slope`     | 右邊近似線的斜率 (的 y = ax + b) 形式。         |
|`right_interception`     |  從 y = ax + b) 攔截近似的左邊線 (b。       |
|`right_variance`    | 分割右邊輸入資料的變異數。        |
|`right_rvariance`     | 分割右邊輸入資料的剩餘變異數。        |
|`left_rsquare`     | 分割左邊線的 R-平方，請參閱 [series_fit_line ( # B1 ](series-fit-linefunction.md)。        |
|`left_slope`    | 左邊近似線的斜率 (y = ax + b 的形式) 。        |
|`left_interception`     |   攔截 y = ax + b) 形式的近似左邊線 (。      |
|`left_variance`     | 分割左側輸入資料的變異數。        |
|`left_rvariance`     | 分割左側輸入資料的剩餘變異數。        |


> [!Note]
> 此函式會傳回多個資料行，所以無法當做另一個函式的引數使用。

## <a name="syntax"></a>語法

專案 `series_fit_2lines(` *x*`)`
* 將會傳回具有下列名稱的所有上述資料行： series_fit_2lines_x_rsquare、series_fit_2lines_x_split_idx 等。

專案 (rs、si、v) = `series_fit_2lines(` *x*`)`
* 將會傳回下列資料行： rs (r-平方) 、si (分割索引) 、v (變異數) ，其餘部分將看起來像 series_fit_2lines_x_rvariance、series_fit_2lines_x_line_fit 等等。

擴充 (rs、si、v) = `series_fit_2lines(` *x*`)`
* 只會傳回︰rs (r-square)、si (split index) 和 v (variance)。
  
## <a name="arguments"></a>引數

* *x*：數值的動態陣列。  

> [!TIP]
> 使用此函式最方便的方式，就是將它套用至「 [製作系列](make-seriesoperator.md) 」運算子的結果。

## <a name="examples"></a>範例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print id=' ', x=range(bin(now(), 1h)-11h, bin(now(), 1h), 1h), y=dynamic([1,2.2, 2.5, 4.7, 5.0, 12, 10.3, 10.3, 9, 8.3, 6.2])
| extend (Slope,Interception,RSquare,Variance,RVariance,LineFit)=series_fit_line(y), (RSquare2, SplitIdx, Variance2,RVariance2,LineFit2)=series_fit_2lines(y)
| project id, x, y, LineFit, LineFit2
| render timechart
```

:::image type="content" source="images/series-fit-2lines/series-fit-2lines.png" alt-text="數列符合2行":::
