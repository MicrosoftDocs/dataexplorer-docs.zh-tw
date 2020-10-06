---
title: 'series_fit_poly ( # A1-Azure 資料總管'
description: '本文說明 Azure 資料總管中 ( # A1 的 series_fit_poly。'
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/21/2020
ms.openlocfilehash: 68f9f55b1ff0c66bb5d50e624f9063d7e177f50a
ms.sourcegitcommit: d0f8d71261f8f01e7676abc77283f87fc450c7b1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91771772"
---
# <a name="series_fit_poly"></a>series_fit_poly ( # A1

從獨立變數套用多項式回歸 (x_series) 至相依變數 (y_series) 。 此函式會採用包含多個數列 (動態數值陣列的資料表) 並使用 [多項式回歸](https://en.wikipedia.org/wiki/Polynomial_regression)為每個數列產生最適合的高序位多項式。 

> [!TIP]
> * 若為平均間距的數列（由 [建立數列運算子](make-seriesoperator.md)所建立）的線性回歸，請使用較簡單的函式 [Series_fit_line ( # B1 ](series-fit-linefunction.md)。 請參閱 [範例 2](#example-2)。
> * 如果提供 *x_series* ，且回歸的執行程度很高，請考慮正規化為 [0-1] 範圍。 請參閱 [範例 3](#example-3)。
> * 如果 *x_series* 為 datetime 類型，則必須將它轉換成 double 和正規化。 請參閱 [範例 3](#example-3)。
> * 如需使用內嵌 Python 來執行多項式回歸的參考，請參閱 [series_fit_poly_fl ( # B1 ](../functions-library/series-fit-poly-fl.md)。


## <a name="syntax"></a>語法

`T | extend  series_fit_poly(`*y_series* `, `*x_series* `, `*角度*`)`
  
## <a name="arguments"></a>引數

|引數| 描述| 必要條件/選擇性| 備註|
|---|---|---|---|
| *y_series* | 包含 [相依變數](https://en.wikipedia.org/wiki/Dependent_and_independent_variables)的動態數值陣列。 | 必要 |
| *x_series* | 包含 [獨立變數](https://en.wikipedia.org/wiki/Dependent_and_independent_variables)的動態數值陣列。 | 選擇性。 只有不等 [間距的數列](https://en.wikipedia.org/wiki/Unevenly_spaced_time_series)才需要。 | 如果未指定，則會設定為預設值 [1，2，...，長度 (y_series) ]。|
| *程度* | 要符合之多項式的必要順序。 例如，1表示線性回歸，2用於二次回歸，依此類推。 | 選擇性 | 預設為 1 (線性回歸) 。|

## <a name="returns"></a>傳回

此 `series_fit_poly()` 函數會傳回下列資料行：

* `rsquare`： [r-正方形](https://en.wikipedia.org/wiki/Coefficient_of_determination) 是符合品質的標準量值。 值的範圍是 [0-1] 中的數位，其中1是最可能的適合，而0表示資料未排序且不符合任何線條。
* `coefficients`：數值陣列，以指定的程度來保存最適合多項式的係數，從最高的功率係數排列至最低。
* `variance`：相依變數 (y_series) 的變異數。
* `rvariance`：剩餘的變異數，也就是輸入資料值與近似值之間的變異數之間的差異。
* `poly_fit`：保留一連串最適合之多項式值的數值陣列。 數列長度等於 (y_series) 的相依變數長度。 用於製作圖表的值。

## <a name="examples"></a>範例

### <a name="example-1"></a>範例 1

X & y 軸上的第五個順序多項式與雜訊：

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 200 step 1
| project x = rand()*5 - 2.3
| extend y = pow(x, 5)-8*pow(x, 3)+10*x+6
| extend y = y + (rand() - 0.5)*0.5*y
| summarize x=make_list(x), y=make_list(y)
| extend series_fit_poly(y, x, 5)
| project-rename fy=series_fit_poly_y_poly_fit, coeff=series_fit_poly_y_coefficients
|fork (project x, y, fy) (project-away x, y, fy)
| render linechart 
```

:::image type="content" source="images/series-fit-poly-function/fifth-order-noise-1.png" alt-text="顯示第五個順序多項式符合具有雜訊的數列的圖表":::

:::image type="content" source="images/series-fit-poly-function/fifth-order-noise-table-1.png" alt-text="顯示第五個順序多項式符合具有雜訊的數列的圖表" border="false":::

### <a name="example-2"></a>範例 2

確認 [ `series_fit_poly` 度數 = 1] 符合 `series_fit_line` ：

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
demo_series1
| extend series_fit_line(y)
| extend series_fit_poly(y)
| project-rename y_line = series_fit_line_y_line_fit, y_poly = series_fit_poly_y_poly_fit
| fork (project x, y, y_line, y_poly) (project-away id, x, y, y_line, y_poly) 
| render linechart with(xcolumn=x, ycolumns=y, y_line, y_poly)
```

:::image type="content" source="images/series-fit-poly-function/fit-poly-line.png" alt-text="顯示第五個順序多項式符合具有雜訊的數列的圖表":::

:::image type="content" source="images/series-fit-poly-function/fit-poly-line-table.png" alt-text="顯示第五個順序多項式符合具有雜訊的數列的圖表" border="false":::
    
### <a name="example-3"></a>範例 3

不規則的 (未) 時間序列間距：

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
//
//  x-axis must be normalized to the range [0-1] if either degree is relatively big (>= 5) or original x range is big.
//  so if x is a time axis it must be normalized as conversion of timestamp to long generate huge numbers (number of 100 nano-sec ticks from 1/1/1970)
//
//  Normalization: x_norm = (x - min(x))/(max(x) - min(x))
//
irregular_ts
| extend series_stats(series_add(TimeStamp, 0))                                                                 //  extract min/max of time axis as doubles
| extend x = series_divide(series_subtract(TimeStamp, series_stats__min), series_stats__max-series_stats__min)  // normalize time axis to [0-1] range
| extend series_fit_poly(num, x, 8)
| project-rename fnum=series_fit_poly_num_poly_fit
| render timechart with(ycolumns=num, fnum)
```
:::image type="content" source="images/series-fit-poly-function/irregular-time-series-1.png" alt-text="顯示第五個順序多項式符合具有雜訊的數列的圖表":::
