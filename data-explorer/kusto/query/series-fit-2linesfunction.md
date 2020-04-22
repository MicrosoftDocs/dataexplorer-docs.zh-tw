---
title: series_fit_2lines() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的 series_fit_2lines()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 05163735c88c3229c13d76e3f8fde9bff091b239
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663512"
---
# <a name="series_fit_2lines"></a>series_fit_2lines()

在序列上應用兩個線段線性回歸,返回多個列。  

以包含動態數值陣列的運算式作為輸入,並應用[兩個線段線性回歸](https://en.wikipedia.org/wiki/Segmented_regression),以識別和量化序列中的趨勢變化。 函數反覆運算序列索引,並在每次反覆運算中將序列拆分為 2 個部分,將單獨的線(使用[series_fit_line()](series-fit-linefunction.md)貼合到每個零件,並計算總 r 平方。 最佳分割是將 r 平方最大化；此函式會傳回其參數︰
* `rsquare`: [r-平方](https://en.wikipedia.org/wiki/Coefficient_of_determination)是適合質量的標準度量。 這是在範圍 [0-1] 中的數字，其中 1 - 是最可能的配合，而 0 表示資料完全未依照順序，且不符合任何一條線
* `split_idx`:突破點到 2 個段的索引(基於零)
* `variance`:輸入資料的方差
* `rvariance`:剩餘方差,即輸入數據值與近似值(按 2 個線段)之間的方差。
* `line_fit`:數位陣列包含最佳擬合線的一系列值。 序列長度等於輸入陣列的長度。 它主要用於圖表。
* `right_rsquare`: 分割右方列的 r 平方,請參閱[series_fit_line ()](series-fit-linefunction.md)
* `right_slope`:右近似線的斜率(這是 y_ax_b 的斜率)
* `right_interception`:截取近似的左行(這是 y_ax_b 中的 b)
* `right_variance`: 分割右邊輸入資料的方差
* `right_rvariance`: 分割右邊輸入資料的餘差
* `left_rsquare`: 分割左邊的列的 r 平方,請參閱[series_fit_line()](series-fit-linefunction.md)
* `left_slope`:左側近似線的斜率(這是 y_ax_b 的斜率)
* `left_interception`:截取近似的左行(這是 y_ax_b 中的 b)
* `left_variance`: 分割左邊輸入資料的方差
* `left_rvariance`: 分割左邊輸入資料的餘差

*請注意*,此函數返回多個列,因此不能將其用作另一個函數的參數。

**語法**

專案`series_fit_2lines(` *x*`)`
* 將返回上述所有列,名稱如下:series_fit_2lines_x_rsquare、series_fit_2lines_x_split_idx等。
專案(rs、si、v)x`series_fit_2lines(` * *`)`
* 將傳回以下欄:rs(r-平方)、si(分割索引)、v(方差)和其他將看起來像series_fit_2lines_x_rvariance、series_fit_2lines_x_line_fit等擴展(rs、si、v)=`series_fit_2lines(`*x*`)`
* 只會傳回︰rs (r-square)、si (split index) 和 v (variance)。
  
**引數**

* *x*: 數值的動態陣列。  

> [!TIP]
> 使用此功能最方便的方法是將其應用於[製造系列](make-seriesoperator.md)運算符的結果。

**範例**

```kusto
print id=' ', x=range(bin(now(), 1h)-11h, bin(now(), 1h), 1h), y=dynamic([1,2.2, 2.5, 4.7, 5.0, 12, 10.3, 10.3, 9, 8.3, 6.2])
| extend (Slope,Interception,RSquare,Variance,RVariance,LineFit)=series_fit_line(y), (RSquare2, SplitIdx, Variance2,RVariance2,LineFit2)=series_fit_2lines(y)
| project id, x, y, LineFit, LineFit2
| render timechart
```

:::image type="content" source="images/samples/series-fit-2lines.png" alt-text="系列適合 2 行":::
