---
title: series_decompose_forecast （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 series_decompose_forecast （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/26/2019
ms.openlocfilehash: 9676da9d12e2654cd4d92538f183a2630971d078
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372873"
---
# <a name="series_decompose_forecast"></a>series_decompose_forecast()

根據數列分解進行預測。

接受包含數列（動態數值陣列）做為輸入的運算式，並預測最後尾端點的值（如需分解方法的詳細資訊，請參閱[series_decompose](series-decomposefunction.md) ）。
 
**語法**

`series_decompose_forecast(`*數列* `,`*點* `[,`*季節性* `,`*趨勢* `,`*Seasonality_threshold*`])`

**引數**

* *數列*：動態陣列資料格，這是數值的陣列。 [Make 系列](make-seriesoperator.md)或[make_list](makelist-aggfunction.md)運算子的結果輸出通常是。
* *Points*：整數，指定要預測之數列結尾的點數（預測）。 這些點會從學習（回歸）流程中排除。
* *季節性*：控制季節性分析的整數，其中包含下列其中一項：
    * -1：使用[series_periods_detect](series-periods-detectfunction.md) （預設值）自動偵測季節性。 
    * period：正整數，指定區間數目的預期時間長度。例如，如果數列位於1h 的 bin 中，則每週期間為168的 bin。
    * 0：無季節性（略過解壓縮此元件）。   
* *趨勢*：控制趨勢分析的字串，其中包含下列其中一項：
    * "linefit"：使用線性回歸（預設值）將趨勢元件解壓縮。    
    * "avg"：將趨勢元件定義為平均（x）。
    * "none"：沒有趨勢，略過解壓縮此元件。   
* *Seasonality_threshold*：當*季節性*設定為自動偵測時，季節性分數的閾值，預設的分數臨界值為 `0.6` 。 如需詳細資訊，請參閱[series_periods_detect](series-periods-detectfunction.md)。

**退貨**

 具有預測數列的動態陣列
  

**注意事項**

* 原始輸入數列的動態陣列應包含要預測的數位*點位置*，這通常是使用[make 系列](make-seriesoperator.md)來完成，並在範圍內指定包含要預測時間範圍的結束時間。
    
* 應該啟用季節性和（或）趨勢，否則函式是多餘的，而且只會傳回填滿零的數列。

**範例**

在下列範例中，我們會在每小時的資料細微性中產生一系列的4周，每週季節性和小型向上趨勢，然後使用 `make-series` ，並將另一個空的周新增至數列。 `series_decompose_forecast`呼叫的是一周（24 * 7 點），它會自動偵測季節性和趨勢，並產生整個5週期間的預測。 

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let ts=range t from 1 to 24*7*4 step 1 // generate 4 weeks of hourly data
| extend Timestamp = datetime(2018-03-01 05:00) + 1h * t 
| extend y = 2*rand() + iff((t/24)%7>=5, 5.0, 15.0) - (((t%24)/10)*((t%24)/10)) + t/72.0 // generate a series with weekly seasonality and ongoing trend
| extend y=iff(t==150 or t==200 or t==780, y-8.0, y) // add some dip outliers
| extend y=iff(t==300 or t==400 or t==600, y+8.0, y) // add some spike outliers
| make-series y=max(y) on Timestamp in range(datetime(2018-03-01 05:00), datetime(2018-03-01 05:00)+24*7*5h, 1h); // create a time series of 5 weeks (last week is empty)
ts 
| extend y_forcasted = series_decompose_forecast(y, 24*7)  // forecast a week forward
| render timechart 
```

:::image type="content" source="images/series-decompose-forecastfunction/series-decompose-forecast.png" alt-text="數列分解預測":::
