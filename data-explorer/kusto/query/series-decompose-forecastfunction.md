---
title: 'series_decompose_forecast ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 series_decompose_forecast。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 09/26/2019
ms.openlocfilehash: 4cf02f01504f3111050f28430de9907fb4aad18b
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250110"
---
# <a name="series_decompose_forecast"></a>series_decompose_forecast()

根據數列分解進行預測。

採用包含數列 (動態數值) 陣列的運算式做為輸入，並預測最後的尾端點值。 如需詳細資訊，請參閱 [series_decompose](series-decomposefunction.md)。
 
## <a name="syntax"></a>語法

`series_decompose_forecast(`*系列* `,`*點* `[,`*季節性* `,`*趨勢* `,`*Seasonality_threshold*`])`

## <a name="arguments"></a>引數

* *數列*：數值的動態陣列資料格。 通常是 [make 系列](make-seriesoperator.md) 或 [make_list](makelist-aggfunction.md) 運算子產生的輸出。
* *點*：整數，指定數列結尾的點數目，以預測 (預測) 。 這些點會從 learning (回歸) 流程中排除。
* *季節性*：控制季節性分析的整數，其中包含下列其中一個：
    * -1：使用 [series_periods_detect](series-periods-detectfunction.md) (預設) 的自動偵測季節性。
    * 句號：正整數，以 bin 數目指定預期的期間。例如，如果數列在 1h bin 中，則每週期間是168的 bin。
    * 0：無季節性 (略過將此元件解壓縮) 。
* *趨勢*：控制趨勢分析的字串，其中包含下列其中一個：
    * `linefit`：使用線性回歸 (預設) 來將趨勢元件解壓縮。
    * `avg`：將 trend 元件定義為平均 (x) 。
    * `none`：沒有趨勢，請略過解壓縮此元件。
* *Seasonality_threshold*：當 *季節性* 設為自動偵測時，季節性分數的臨界值。 預設的分數閾值為 `0.6` 。 如需詳細資訊，請參閱 [series_periods_detect](series-periods-detectfunction.md)。

**返回**

 具有預測數列的動態陣列。

> [!NOTE]
> * 原始輸入數列的動態陣列應包含要預測的數個 *點位置* 。 預測通常是使用「 [構成系列](make-seriesoperator.md) 」來完成，並指定範圍內的結束時間（包含要預測的時間範圍）。
> * 應該啟用季節性或 trend，否則函數是多餘的，而且只會傳回填滿零的數列。

## <a name="example"></a>範例

在下列範例中，我們會以每小時的資料細微性產生一連串的四周，每週季節性和一個小的向上趨勢。 接著，我們會使用 `make-series` ，並將另一個空白周新增至數列。 `series_decompose_forecast` 在一周內呼叫 (24 * 7 點) ，它會自動偵測季節性和趨勢，並產生整個五週期間的預測。

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
 
