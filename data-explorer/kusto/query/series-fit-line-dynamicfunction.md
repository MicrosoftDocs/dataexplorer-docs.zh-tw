---
title: series_fit_line_dynamic （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 series_fit_line_dynamic （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 756650db23d531ec37636c0e7bd781a74ef9fdc3
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372686"
---
# <a name="series_fit_line_dynamic"></a>series_fit_line_dynamic()

在數列上套用線性回歸，並傳回動態物件。  

採用包含動態數值陣列做為輸入的運算式，並執行[線性回歸](https://en.wikipedia.org/wiki/Line_fitting)，以便找出最適合它的行。 此函式需用於時間序列陣列，並符合 make-series 運算子的輸出。 它會產生具有下列內容的動態值：
* `rsquare`： [r-正方形](https://en.wikipedia.org/wiki/Coefficient_of_determination)是適合品質的標準量值。 這是在範圍 [0-1] 中的數字，其中 1 - 是最可能的配合，而 0 表示資料完全未依照順序，且不符合任何一條線 
* `slope`：近似線的斜率（這是來自 y = ax + b 的 a）
* `variance`：輸入資料的變異數
* `rvariance`：剩餘變異數，也就是輸入資料值與近似值之間的差異。
* `interception`：攔截近似線（這是 b，y = ax + b）
* `line_fit`：數位陣列，其中包含一系列最適合行的值。 序列長度等於輸入陣列的長度。 它主要用於圖表。

這個運算子與[series_fit_line](series-fit-linefunction.md)相似，但不同的是， `series-fit-line` 它會傳回動態包。

**語法**

`series_fit_line_dynamic(`*x*`)`

**引數**

* *x*：數值的動態陣列。

> [!TIP]
> 使用此函式最方便的方式，是將它套用至[make 系列](make-seriesoperator.md)運算子的結果。

**範例**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print id=' ', x=range(bin(now(), 1h)-11h, bin(now(), 1h), 1h), y=dynamic([2,5,6,8,11,15,17,18,25,26,30,30])
| extend fit=series_fit_line_dynamic(y)
| extend RSquare=fit.rsquare, Slope=fit.slope, Variance=fit.variance,RVariance=fit.rvariance,Interception=fit.interception,LineFit=fit.line_fit
| render timechart
```

:::image type="content" source="images/series-fit-line/series-fit-line.png" alt-text="數列擬合線":::

| RSquare | Slope | Variance | RVariance | Interception | LineFit                                                                                     |
|---------|-------|----------|-----------|--------------|---------------------------------------------------------------------------------------------|
| 0.982   | 2.730 | 98.628   | 1.686     | -1.666       | 1.064、3.7945、6.526、9.256、11.987、14.718、17.449、20.180、22.910、25.641、28.371、31.102 |
