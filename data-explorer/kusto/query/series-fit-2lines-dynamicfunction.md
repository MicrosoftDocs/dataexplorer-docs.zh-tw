---
title: 'series_fit_2lines_dynamic ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 series_fit_2lines_dynamic。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: c16e0918ce45df131490686ce20db4086fddf4c0
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242117"
---
# <a name="series_fit_2lines_dynamic"></a>series_fit_2lines_dynamic()

在數列上套用兩個區段線性回歸，並傳回動態物件。  

採用包含動態數值陣列的運算式做為輸入，並套用 [兩個區段線性回歸](https://en.wikipedia.org/wiki/Segmented_regression) ，以找出並量化數列中的趨勢變更。 函數會逐一查看數列索引。 在每個反復專案中，它會將數列分割成兩個部分，並使用 [series_fit_line ( # B1 ](series-fit-linefunction.md) 或 [Series_fit_line_dynamic ( # B3 ](series-fit-line-dynamicfunction.md)來容納不同的行。 此函式會將這兩個部分的行放在同一行，並計算所有的 R 平方值。 最佳分割是最大化 R 平方的分割。 函式會使用下列內容，傳回動態值中的參數：

* `rsquare`： [R 平方](https://en.wikipedia.org/wiki/Coefficient_of_determination) 是符合品質的標準量值。 它是在 [0-1] 範圍內的數位，其中1是最適合的大小，而0表示資料未排序且不符合任何一行。
* `split_idx`：中中斷點到兩個區段的索引， (以零為基底的) 。
* `variance`：輸入資料的變異數。
* `rvariance`：剩餘的變異數，也就是兩個線段)  (的輸入資料值之間的變異差異。
* `line_fit`：保留一系列最合適行之值的數值陣列。 序列長度等於輸入陣列的長度。 它是用來製作圖表。
* `right.rsquare`：分割右邊的線 r-平方，請參閱 [series_fit_line ( # B1 ](series-fit-linefunction.md) 或 [Series_fit_line_dynamic ( # B3 ](series-fit-line-dynamicfunction.md)。
* `right.slope`：右邊近似線的斜率 (y = ax + b 的形式) 。
* `right.interception`：從 y = ax + b) 攔截近似的左邊線 (b。
* `right.variance`：分割右邊輸入資料的變異數。
* `right.rvariance`：分割右邊輸入資料的剩餘變異數。
* `left.rsquare`：位於分割左邊的線 r 平方，請參閱 [series_fit_line ( # A1]。 (series-fit-linefunction.md) 或 [series_fit_line_dynamic ( # B5 ](series-fit-line-dynamicfunction.md)。
* `left.slope`：左邊近似線的斜率 (y = ax + b 的形式) 。
* `left.interception`：攔截左上行的近似左邊線 (格式為 y = ax + b) 。
* `left.variance`：分割左邊的輸入資料變異數。
* `left.rvariance`：分割左側輸入資料的剩餘變異數。

這個運算子類似于 [series_fit_2lines](series-fit-2linesfunction.md)。 與不同的 `series-fit-2lines` 是，它會傳回動態包。

## <a name="syntax"></a>語法

`series_fit_2lines_dynamic(`*X*`)`

## <a name="arguments"></a>引數

* *x*：數值的動態陣列。  

> [!TIP]
> 使用此函式最方便的方式，就是將它套用至「 [製作系列](make-seriesoperator.md) 」運算子的結果。

## <a name="example"></a>範例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print id=' ', x=range(bin(now(), 1h)-11h, bin(now(), 1h), 1h), y=dynamic([1,2.2, 2.5, 4.7, 5.0, 12, 10.3, 10.3, 9, 8.3, 6.2])
| extend LineFit=series_fit_line_dynamic(y).line_fit, LineFit2=series_fit_2lines_dynamic(y).line_fit
| project id, x, y, LineFit, LineFit2
| render timechart
```

:::image type="content" source="images/series-fit-2lines/series-fit-2lines.png" alt-text="數列符合2行":::
