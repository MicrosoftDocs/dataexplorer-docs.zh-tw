---
title: 'series_decompose_anomalies ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 series_decompose_anomalies。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 08/28/2019
ms.openlocfilehash: ded1f7ed499d0a8379fdf5b8e9949fa06351fc07
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250198"
---
# <a name="series_decompose_anomalies"></a>series_decompose_anomalies()

異常偵測是以序列分解為基礎。
如需詳細資訊，請參閱 [series_decompose ( # B1 ](series-decomposefunction.md)。

函式會採用包含數列 (動態數值) 陣列的運算式做為輸入，並以分數來解壓縮異常點。

## <a name="syntax"></a>語法

`series_decompose_anomalies (`*系列* `[, `*閾值* `,`*季節性* `,`*趨勢* `, `*Test_points* `, `*AD_method* `,`*Seasonality_threshold*`])`

## <a name="arguments"></a>引數

* *數列*：動態陣列資料格，這是數值的陣列，通常是 [make 系列](make-seriesoperator.md) 或 [make_list](makelist-aggfunction.md) 運算子的結果輸出
* *閾值*：異常閾值，預設值為 1.5 (k 值) 用於偵測輕微或更強的異常
* *季節性*：控制季節性分析的整數，其中包含下列其中一項：
    * -1：使用 [series_periods_detect](series-periods-detectfunction.md)) 的自動偵測季節性 ([default]
    * 0：無季節性 (也就是略過解壓縮此元件) 
    * 句號：正整數，以 bin 單位數目指定預期的期間。 例如，如果數列是在一個小時的儲箱中，則每週期間是168的 bin
* *趨勢*：控制趨勢分析的字串，其中包含下列其中一項：
    * "avg"：將趨勢元件定義為數列的平均值 [default]
    * 「無」：無趨勢，略過解壓縮此元件
    * "linefit"：使用線性回歸來解壓縮趨勢元件
* *Test_points*： 0 [default] 或正整數，指定要從 learning (回歸) 程式中排除之數列結尾的點數目。 此參數應針對預測用途進行設定
* *AD_method*：在剩餘時間序列上控制異常偵測方法的字串，其中包含下列其中一個：
    * "ctukey"： [Tukey 的範圍測試](https://en.wikipedia.org/wiki/Outlier#Tukey's_fences) 與自訂第 10-90 個百分位數 [default]
    * "tukey"： [tukey 的範圍測試](https://en.wikipedia.org/wiki/Outlier#Tukey's_fences) 與標準的第 25 75 個百分位數，如需有關剩餘時間序列的詳細資訊，請參閱 [series_outliers](series-outliersfunction.md)
* *Seasonality_threshold*：當 *季節性* 設為自動偵測時，季節性分數的臨界值。 預設的分數閾值為 `0.6` 。 如需詳細資訊，請參閱 [series_periods_detect](series-periods-detectfunction.md)

## <a name="returns"></a>傳回

 函數會傳回下列個別數列：

* `ad_flag`：包含 (+ 1，-1，0) 分別標示為向上/向下/無異常的三元系列
* `ad_score`：異常分數
* `baseline`：根據分解的數列預測值

## <a name="the-algorithm"></a>演算法

此函數會遵循下列步驟：
1. 使用各自的參數呼叫 [series_decompose ( # B1 ](series-decomposefunction.md) ，以建立基準和殘差的數列。
1. 藉由在殘差系列上套用具有所選異常偵測方法的 [series_outliers ( # B1 ](series-outliersfunction.md) 來計算 ad_score 系列。
1. 藉由在 ad_score 上套用臨界值來計算 ad_flag 系列，以分別標記為向上/向下/無異常。
 
## <a name="examples"></a>範例

### <a name="detect-anomalies-in-weekly-seasonality"></a>偵測每週季節性的異常狀況

在下列範例中，使用每週季節性產生數列，然後在其中新增一些極端值。 `series_decompose_anomalies` 自動偵測 dll 季節性，並產生可捕獲重複模式的基準。 您新增的極端值可以清楚地在 ad_score 元件中找出。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let ts=range t from 1 to 24*7*5 step 1 
| extend Timestamp = datetime(2018-03-01 05:00) + 1h * t 
| extend y = 2*rand() + iff((t/24)%7>=5, 10.0, 15.0) - (((t%24)/10)*((t%24)/10)) // generate a series with weekly seasonality
| extend y=iff(t==150 or t==200 or t==780, y-8.0, y) // add some dip outliers
| extend y=iff(t==300 or t==400 or t==600, y+8.0, y) // add some spike outliers
| summarize Timestamp=make_list(Timestamp, 10000),y=make_list(y, 10000);
ts 
| extend series_decompose_anomalies(y)
| render timechart  
```

:::image type="content" source="images/series-decompose-anomaliesfunction/weekly-seasonality-outliers.png" alt-text="顯示基準和極端值的每週季節性" border="false":::

### <a name="detect-anomalies-in-weekly-seasonality-with-trend"></a>使用趨勢偵測每週季節性中的異常狀況

在此範例中，請將趨勢新增至上述範例中的數列。 首先，以 `series_decompose_anomalies` 預設參數執行，其中 trend `avg` 預設值只會採用平均值，而不會計算趨勢。 相較于上一個範例，產生的基準不包含趨勢，且較不精確。 因此，因為變異數較高，所以不會偵測到您在資料中插入的某些極端值。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let ts=range t from 1 to 24*7*5 step 1 
| extend Timestamp = datetime(2018-03-01 05:00) + 1h * t 
| extend y = 2*rand() + iff((t/24)%7>=5, 5.0, 15.0) - (((t%24)/10)*((t%24)/10)) + t/72.0 // generate a series with weekly seasonality and ongoing trend
| extend y=iff(t==150 or t==200 or t==780, y-8.0, y) // add some dip outliers
| extend y=iff(t==300 or t==400 or t==600, y+8.0, y) // add some spike outliers
| summarize Timestamp=make_list(Timestamp, 10000),y=make_list(y, 10000);
ts 
| extend series_decompose_anomalies(y)
| extend series_decompose_anomalies_y_ad_flag = 
series_multiply(10, series_decompose_anomalies_y_ad_flag) // multiply by 10 for visualization purposes
| render timechart
```

:::image type="content" source="images/series-decompose-anomaliesfunction/weekly-seasonality-outliers-with-trend.png" alt-text="顯示基準和極端值的每週季節性" border="false":::

接下來，執行相同的範例，但因為您預期序列中會有趨勢，請 `linefit` 在 trend 參數中指定。 您可以看到基準較接近輸入數列。 偵測到所有插入的極端值，還有一些誤報。 請參閱下一個調整臨界值的範例。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let ts=range t from 1 to 24*7*5 step 1 
| extend Timestamp = datetime(2018-03-01 05:00) + 1h * t 
| extend y = 2*rand() + iff((t/24)%7>=5, 5.0, 15.0) - (((t%24)/10)*((t%24)/10)) + t/72.0 // generate a series with weekly seasonality and ongoing trend
| extend y=iff(t==150 or t==200 or t==780, y-8.0, y) // add some dip outliers
| extend y=iff(t==300 or t==400 or t==600, y+8.0, y) // add some spike outliers
| summarize Timestamp=make_list(Timestamp, 10000),y=make_list(y, 10000);
ts 
| extend series_decompose_anomalies(y, 1.5, -1, 'linefit')
| extend series_decompose_anomalies_y_ad_flag = 
series_multiply(10, series_decompose_anomalies_y_ad_flag) // multiply by 10 for visualization purposes
| render timechart  
```

:::image type="content" source="images/series-decompose-anomaliesfunction/weekly-seasonality-linefit-trend.png" alt-text="顯示基準和極端值的每週季節性" border="false":::

### <a name="tweak-the-anomaly-detection-threshold"></a>調整異常偵測閾值

在先前的範例中，偵測到一些雜音的點是異常。 現在將異常偵測閾值從預設的1.5 增加至2.5。 使用這個 interpercentile 範圍，僅偵測到較強的異常。 現在，只會偵測到您在資料中插入的極端值。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let ts=range t from 1 to 24*7*5 step 1 
| extend Timestamp = datetime(2018-03-01 05:00) + 1h * t 
| extend y = 2*rand() + iff((t/24)%7>=5, 5.0, 15.0) - (((t%24)/10)*((t%24)/10)) + t/72.0 // generate a series with weekly seasonality and onlgoing trend
| extend y=iff(t==150 or t==200 or t==780, y-8.0, y) // add some dip outliers
| extend y=iff(t==300 or t==400 or t==600, y+8.0, y) // add some spike outliers
| summarize Timestamp=make_list(Timestamp, 10000),y=make_list(y, 10000);
ts 
| extend series_decompose_anomalies(y, 2.5, -1, 'linefit')
| extend series_decompose_anomalies_y_ad_flag = 
series_multiply(10, series_decompose_anomalies_y_ad_flag) // multiply by 10 for visualization purposes
| render timechart  
```

:::image type="content" source="images/series-decompose-anomaliesfunction/weekly-seasonality-higher-threshold.png" alt-text="顯示基準和極端值的每週季節性" border="false":::
