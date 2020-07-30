---
title: series_fit_line （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 series_fit_line （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 0158753d3d2496e425247202d906633837aa023a
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351483"
---
# <a name="series_fit_line"></a>series_fit_line()

在數列上套用線性回歸，並傳回多個資料行。  

採用包含動態數值陣列做為輸入的運算式，並執行[線性回歸](https://en.wikipedia.org/wiki/Line_fitting)，以尋找最適合它的線條。 此函式需用於時間序列陣列，並符合 make-series 運算子的輸出。 函式會產生下列資料行：
* `rsquare`： [r-正方形](https://en.wikipedia.org/wiki/Coefficient_of_determination)是適合品質的標準量值。 值在範圍 [0-1] 中的數位，其中 1-是最適合的大小，而0表示資料未排序，且不符合任何行。 
* `slope`：近似線的斜率（"a"，從 y = ax + b）。
* `variance`：輸入資料的變異數。
* `rvariance`：剩餘變異數，也就是輸入資料值與近似值之間的差異。
* `interception`：攔截近似線（"b"，從 y = ax + b）。
* `line_fit`：數位陣列，其中包含一系列最適合行的值。 序列長度等於輸入陣列的長度。 用於圖表的值。

## <a name="syntax"></a>語法

`series_fit_line(`*x*`)`

## <a name="arguments"></a>引數

* *x*：數值的動態陣列。

> [!TIP]
> 使用此函式最方便的方式，是將它套用至[make 系列](make-seriesoperator.md)運算子的結果。

## <a name="examples"></a>範例

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print id=' ', x=range(bin(now(), 1h)-11h, bin(now(), 1h), 1h), y=dynamic([2,5,6,8,11,15,17,18,25,26,30,30])
| extend (RSquare,Slope,Variance,RVariance,Interception,LineFit)=series_fit_line(y)
| render timechart
```

:::image type="content" source="images/series-fit-line/series-fit-line.png" alt-text="數列擬合線":::

| RSquare | 斜率 | 變異數 | RVariance | Interception | LineFit                                                                                     |
|---------|-------|----------|-----------|--------------|---------------------------------------------------------------------------------------------|
| 0.982   | 2.730 | 98.628   | 1.686     | -1.666       | 1.064、3.7945、6.526、9.256、11.987、14.718、17.449、20.180、22.910、25.641、28.371、31.102 |
