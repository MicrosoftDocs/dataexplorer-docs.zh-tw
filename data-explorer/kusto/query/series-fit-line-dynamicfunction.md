---
title: 'series_fit_line_dynamic ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 series_fit_line_dynamic。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 157a9d88327cc4f35566c9e0b3890e2124098048
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242061"
---
# <a name="series_fit_line_dynamic"></a>series_fit_line_dynamic()

對數列套用線性回歸，並傳回動態物件。  

採用包含動態數值陣列的運算式做為輸入，並執行 [線性回歸](https://en.wikipedia.org/wiki/Line_fitting) ，以找出最適合的行。 此函式需用於時間序列陣列，並符合 make-series 運算子的輸出。 它會產生具有下列內容的動態值：
* `rsquare`： [r-正方形](https://en.wikipedia.org/wiki/Coefficient_of_determination) 是符合品質的標準量值。 它是範圍 [0-1] 中的數位，其中1是最合適的大小，0表示資料未排序且不符合任何一行
* `slope`：近似線的斜率 (*y = ax + b*的*a*-值) 
* `variance`：輸入資料的變異數
* `rvariance`：剩餘變異數，也就是輸入資料值與近似值之間的變異數。
* `interception`：從*y = ax + b* (的*b*值攔截近似值行) 
* `line_fit`：包含一系列最佳符合線值的數值陣列。 序列長度等於輸入陣列的長度。 它主要用於製作圖表。

這個運算子類似于 [series_fit_line](series-fit-linefunction.md)，但與傳回 `series-fit-line` 動態包不同。

## <a name="syntax"></a>語法

`series_fit_line_dynamic(`*X*`)`

## <a name="arguments"></a>引數

* *x*：數值的動態陣列。

> [!TIP]
> 使用此函式最方便的方式，就是將它套用至 [make series](make-seriesoperator.md) 運算子的結果。

## <a name="examples"></a>範例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print id=' ', x=range(bin(now(), 1h)-11h, bin(now(), 1h), 1h), y=dynamic([2,5,6,8,11,15,17,18,25,26,30,30])
| extend fit=series_fit_line_dynamic(y)
| extend RSquare=fit.rsquare, Slope=fit.slope, Variance=fit.variance,RVariance=fit.rvariance,Interception=fit.interception,LineFit=fit.line_fit
| render timechart
```
 
:::image type="content" source="images/series-fit-line/series-fit-line.png" alt-text="數列符合線":::

| RSquare | 斜率 | 變異數 | RVariance | Interception | LineFit                                                                                     |
|---------|-------|----------|-----------|--------------|---------------------------------------------------------------------------------------------|
| 0.982   | 2.730 | 98.628   | 1.686     | -1.666       | 1.064、3.7945、6.526、9.256、11.987、14.718、17.449、20.180、22.910、25.641、28.371、31.102 |
 
