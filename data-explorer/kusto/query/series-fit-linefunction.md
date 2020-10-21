---
title: 'series_fit_line ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 series_fit_line。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 9731e3384fb0109c37ad6c0ca262a954ef5dd470
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248463"
---
# <a name="series_fit_line"></a>series_fit_line()

對數列套用線性回歸，並傳回多個資料行。  

採用包含動態數值陣列的運算式做為輸入，並執行 [線性回歸](https://en.wikipedia.org/wiki/Line_fitting) ，以找出最適合的行。 此函式需用於時間序列陣列，並符合 make-series 運算子的輸出。 函數會產生下列資料行：
* `rsquare`： [r-正方形](https://en.wikipedia.org/wiki/Coefficient_of_determination) 是符合品質的標準量值。 值的範圍是 [0-1] 中的數位，其中1是最可能的適合，而0表示資料未排序且不符合任何線條。 
* `slope`：近似線的斜率 ( "a" from y = ax + b) 。
* `variance`：輸入資料的變異數。
* `rvariance`：剩餘的變異數，也就是輸入資料值與近似值之間的變異數之間的差異。
* `interception`：從 y = ax + b)  ( "b" 攔截近似的行。
* `line_fit`：保留一系列最合適行之值的數值陣列。 序列長度等於輸入陣列的長度。 用於製作圖表的值。

## <a name="syntax"></a>語法

`series_fit_line(`*X*`)`

## <a name="arguments"></a>引數

* *x*：數值的動態陣列。

> [!TIP]
> 使用此函式最方便的方式，就是將它套用至「 [製作系列](make-seriesoperator.md) 」運算子的結果。

## <a name="examples"></a>範例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print id=' ', x=range(bin(now(), 1h)-11h, bin(now(), 1h), 1h), y=dynamic([2,5,6,8,11,15,17,18,25,26,30,30])
| extend (RSquare,Slope,Variance,RVariance,Interception,LineFit)=series_fit_line(y)
| render timechart
```

:::image type="content" source="images/series-fit-line/series-fit-line.png" alt-text="數列符合線":::

| RSquare | 斜率 | 變異數 | RVariance | Interception | LineFit                                                                                     |
|---------|-------|----------|-----------|--------------|---------------------------------------------------------------------------------------------|
| 0.982   | 2.730 | 98.628   | 1.686     | -1.666       | 1.064、3.7945、6.526、9.256、11.987、14.718、17.449、20.180、22.910、25.641、28.371、31.102 |
