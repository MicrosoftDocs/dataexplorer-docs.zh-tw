---
title: series_fit_line_dynamic() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的 series_fit_line_dynamic()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 03bbb143504d0540156d5f18d4ed527ab5d13d38
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663548"
---
# <a name="series_fit_line_dynamic"></a>series_fit_line_dynamic()

在序列上應用線性回歸,返回動態物件。  

以包含動態數值陣列的運算式作為輸入並執行[線性回歸](https://en.wikipedia.org/wiki/Line_fitting),以便找到最適合它的線。 此函式需用於時間序列陣列，並符合 make-series 運算子的輸出。 它產生有以下內容的動態值:
* `rsquare`: [r-平方](https://en.wikipedia.org/wiki/Coefficient_of_determination)是適合質量的標準度量。 這是在範圍 [0-1] 中的數字，其中 1 - 是最可能的配合，而 0 表示資料完全未依照順序，且不符合任何一條線 
* `slope`:近似線的斜率(這是 y_ax_b 的斜率)
* `variance`:輸入資料的方差
* `rvariance`:剩餘方差,即輸入數據值與近似值之間的方差。
* `interception`:攔截近似線(這是 y_ax_b 中的 b)
* `line_fit`:數位陣列包含最佳擬合線的一系列值。 序列長度等於輸入陣列的長度。 它主要用於圖表。

這個運算符類似於[series_fit_line,](series-fit-linefunction.md)但`series-fit-line`不像 它返回一個動態袋。

**語法**

`series_fit_line_dynamic(`*X.*`)`

**引數**

* *x*: 數值的動態陣列。

> [!TIP]
> 使用此功能最方便的方法是將其應用於[製造系列](make-seriesoperator.md)運算符的結果。

**範例**

```kusto
print id=' ', x=range(bin(now(), 1h)-11h, bin(now(), 1h), 1h), y=dynamic([2,5,6,8,11,15,17,18,25,26,30,30])
| extend fit=series_fit_line_dynamic(y)
| extend RSquare=fit.rsquare, Slope=fit.slope, Variance=fit.variance,RVariance=fit.rvariance,Interception=fit.interception,LineFit=fit.line_fit
| render timechart
```

:::image type="content" source="images/samples/series-fit-line.png" alt-text="系列擬合線":::

| RSquare | Slope | Variance | RVariance | Interception | LineFit                                                                                     |
|---------|-------|----------|-----------|--------------|---------------------------------------------------------------------------------------------|
| 0.982   | 2.730 | 98.628   | 1.686     | -1.666       | 1.064, 3.7945, 6.526, 9.256, 11.987, 14.718, 17.449, 20.180, 22.910, 25.641, 28.371, 31.102 |