---
title: series_decompose_forecast() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的 series_decompose_forecast()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/26/2019
ms.openlocfilehash: 67f464949bbc432e73932c4d8bff8290ef8f0f8f
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663534"
---
# <a name="series_decompose_forecast"></a>series_decompose_forecast()

基於序列分解的預測。

以包含序列(動態數值陣列)的運算式作為輸入並預測最後尾隨點的值(有關分解方法的更多詳細資訊,請參閱[series_decompose)。](series-decomposefunction.md)
 
**語法**

`series_decompose_forecast(`*系列*`,`*Points*`,`*Seasonality_threshold*點*Trend*`[,`季節性`,`趨勢Seasonality_threshold*Seasonality*`])`

**引數**

* *系列*:動態陣列儲存格,是數值陣列。 通常,[生產系列](make-seriesoperator.md)或[make_list](makelist-aggfunction.md)運算符的結果輸出。
* *點*: 指定要預測(預測)的序列末尾的點數的整數。 這些點從學習(回歸)過程中排除。
* *季節性*:控制季節性分析的整數,包含以下之一:
    * -1:使用[series_periods_detect(](series-periods-detectfunction.md)預設)自動檢測季節性。 
    * 期間:正整數,以條柱數指定預期週期。例如,如果序列位於 1h bin 中,則每周週期為 168 個條柱。
    * 0:無季節性(跳過提取此元件)。   
* *趨勢*: 控制趨勢分析的字串,包含以下之一:
    * "線擬":使用線性回歸(預設)提取趨勢分量。    
    * "平均":將趨勢分量定義為平均值(x)。
    * "無":沒有趨勢,跳過提取此元件。   
* *Seasonality_threshold*:*當季節性*設定為自動偵測時,季節性分數的閾值為 。,預設分數閾`0.6`值為 。 有關詳細資訊,請參閱[series_periods_detect](series-periods-detectfunction.md)。

**返回**

 具有預測序列的動態陣列
  

**注意事項**

* 原始輸入序列的動態數位應包括要預測的數位*點*槽,這通常是通過使用[make 系列](make-seriesoperator.md)和指定包含要預測的時間範圍的結束時間來完成的。
    
* 應啟用季節性和/或趨勢,否則函數是冗餘的,並且僅返回填充零的序列。

**範例**

在下面的示例中,我們生成一個每周季節性和小幅上升趨勢的每小時穀物連續 4 周,`make-series`然後使用 並添加另一個空周到該系列。 `series_decompose_forecast`調用一周(24 最會自動檢測季節性和趨勢,並生成整個 5 周週期的預測。 

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

:::image type="content" source="images/samples/series-decompose-forecast.png" alt-text="列分解預測":::
