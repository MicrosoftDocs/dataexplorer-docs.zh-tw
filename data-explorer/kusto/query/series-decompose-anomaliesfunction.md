---
title: series_decompose_anomalies （）-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 series_decompose_anomalies （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/28/2019
ms.openlocfilehash: 51ac499690323b1d2bafb4dc20ab7773f5c99c63
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618873"
---
# <a name="series_decompose_anomalies"></a>series_decompose_anomalies()

以數列分解為基礎的異常偵測（請參閱[series_decompose （）](series-decomposefunction.md)） 

接受包含數列（動態數值陣列）做為輸入的運算式，並使用分數將異常點解壓縮。

**語法**

`series_decompose_anomalies (`*數列* `[, ` *閾值*`,` *Trend* *AD_method* *Seasonality* `,` *Seasonality_threshold* *Test_points*季節性趨勢`, ` Test_points`, ` AD_method Seasonality_threshold`,``])`

**引數**

* *數列*：動態陣列資料格，這是數值的陣列，通常是[make 系列](make-seriesoperator.md)或[make_list](makelist-aggfunction.md)運算子的結果輸出
* *閾值*：偵測輕度或較強異常的異常臨界值（預設值為1.5 （k value））
* *季節性*：控制季節性分析的整數，其中包含任一項
    * -1：自動偵測季節性（使用[series_periods_detect](series-periods-detectfunction.md)） [預設] 
    * 0：無季節性（亦即略過解壓縮此元件）
    * period：正整數，以 bin 單位數指定預期的期間。 例如，如果數列位於1h 的 bin 中，則每週期間為168的 bin
* *趨勢*：控制趨勢分析的字串，其中包括    
    * "avg"：將趨勢元件定義為數列的平均值 [default]
    * "none"：沒有趨勢，略過解壓縮此元件 
    * "linefit"：使用線性回歸來摘錄趨勢元件
* *Test_points*： 0 [default] 或正整數，指定要從學習（回歸）程式中排除之數列結尾的點數目。 此參數應該設定為預測用途
* *AD_method*：在剩餘時間序列上控制異常偵測方法（請參閱[series_outliers](series-outliersfunction.md)）的字串，其中包含任一項    
    * "ctukey"：具有自訂第 10-90 個百分位數範圍[的 Tukey 的圍欄測試](https://en.wikipedia.org/wiki/Outlier#Tukey's_fences)[預設]
    * "tukey"： [tukey 的隔離測試](https://en.wikipedia.org/wiki/Outlier#Tukey's_fences)與標準第25個-75 百分位數範圍
* *Seasonality_threshold*：當*季節性*設定為自動偵測時，季節性分數的閾值，預設的分數臨界`0.6`值為（如需詳細資料，請參閱： [series_periods_detect](series-periods-detectfunction.md)）


**退貨**

 函式會傳回下列各自的數列：

* `ad_flag`：包含（+ 1，-1，0）的三元數列，分別標示/關閉/無異常
* `ad_score`：異常分數
* `baseline`：根據分解的數列預測值

**關於演算法的詳細資訊**

此函式會遵循下列步驟：
1. 使用個別參數呼叫[series_decompose （）](series-decomposefunction.md) ，以建立基準和殘差數列
2. 藉由在殘差數列上使用所選的異常偵測方法來套用[series_outliers （）](series-outliersfunction.md) ，以計算 ad_score 的數列
3. 藉由套用 ad_score 上的臨界值，分別標示/關閉/無異常，計算 ad_flag 數列
 
**範例**

**1. 偵測每週季節性中的異常**

在下列範例中，我們會產生一個包含每週季節性的數列，然後在其中新增一些極端值。 `series_decompose_anomalies`自動偵測季節性，並產生可捕捉重複模式的基準。 我們新增的極端值可以在 ad_score 元件中清楚地找出。

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

**2. 以趨勢偵測每週季節性中的異常**

在此範例中，我們會將趨勢新增到上一個範例中的數列。 首先，我們會`series_decompose_anomalies`使用預設參數執行，其中趨勢`avg`預設值只會佔用平均值，而不會計算趨勢，我們可以看到所產生的基準不包含趨勢，而且與前一個範例比較不精確，因此，由於較高的變異數，我們不會偵測到我們在資料中插入的某些極端值。

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

:::image type="content" source="images/series-decompose-anomaliesfunction/weekly-seasonality-outliers-with-trend.png" alt-text="具有趨勢的每週季節性極端值" border="false":::

接下來，我們會執行相同的範例，但是因為我們預期數列中的趨勢，所以`linefit`我們會在 trend 參數中指定。 我們可以看到基準比輸入數列更接近。 我們所插入的所有極端值，以及一些誤報（請參閱下一個微調閾值的範例）。

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

:::image type="content" source="images/series-decompose-anomaliesfunction/weekly-seasonality-linefit-trend.png" alt-text="具有 linefit 趨勢的每週季節性異常" border="false":::

**3. 調整異常偵測閾值**

在上述範例中，有幾個雜訊點被偵測為異常，在此範例中，我們會將異常偵測閾值從預設值1.5 增加至 2.5 interpercentile 範圍，以便只偵測到較強的異常。 我們可以看到，現在只會偵測到我們在資料中插入的極端值。

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

:::image type="content" source="images/series-decompose-anomaliesfunction/weekly-seasonality-higher-threshold.png" alt-text="具有較高異常閾值的每週序列異常" border="false":::

