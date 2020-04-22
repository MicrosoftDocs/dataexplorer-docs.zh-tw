---
title: series_decompose_anomalies() - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的series_decompose_anomalies()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/28/2019
ms.openlocfilehash: 7d9ca0585b40a97d21690f908a50de5be493c412
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663617"
---
# <a name="series_decompose_anomalies"></a>series_decompose_anomalies()

基於序列分解的異常檢測(參見[series_decompose()](series-decomposefunction.md) 

以包含序列(動態數位陣列)的運算式作為輸入,並提取具有分數的異常點。

**語法**

`series_decompose_anomalies (`*系列*`[, `*閾值*`,`*季節性*`, `*Trend*`,``,`趨勢Test_pointsAD_methodSeasonality_threshold*Seasonality_threshold**AD_method**Test_points*`, ``])`

**引數**

* *列*: 動態陣列儲存格,是數值陣列,通常是[產生序列](make-seriesoperator.md)或[make_list](makelist-aggfunction.md)運算子輸出
* *閾值*:異常閾值,預設 1.5 (k 值),用於檢測輕度或更強的異常
* *季節性*:控制季節性分析的整數,包含其中一個
    * -1:自動檢測季節性(使用[series_periods_detect)[](series-periods-detectfunction.md)預設] 
    * 0:無季節性(即跳過提取此元件)
    * 期間:正整數,以條柱單位數指定預期週期。 例如,如果序列位於 1h bin 中,則每周週期為 168 個 bin
* *趨勢*: 控制趨勢分析的字串,包含任一    
    * "avg":將趨勢元件定義為系列的平均值 [默認值]
    * "無":無趨勢,跳過提取此元件 
    * 線擬":使用線性回歸提取趨勢分量
* *Test_points*:0 [預設] 或正整數,指定序列末尾要從學習(回歸)過程中排除的點數。 此參數應設定為用於預測目的
* *AD_method*: 控制剩餘時間序列上的例外偵測方法(參見[series_outliers)](series-outliersfunction.md)的字串,包含任一    
    * "ctukey":[圖基的柵欄測試](https://en.wikipedia.org/wiki/Outlier#Tukey's_fences)與自定義的第10-90個百分位數範圍[預設]
    * "圖基":[圖基的圍欄測試](https://en.wikipedia.org/wiki/Outlier#Tukey's_fences)標準25-75百分位數範圍
* *Seasonality_threshold*:*當季節性*設定為自動檢測時,季節性分數的閾值為"時`0.6`",默認 分數閾值為(有關詳細資訊,請參閱[:series_periods_detect)](series-periods-detectfunction.md)


**返回**

 該函數傳回以下的列列:

* `ad_flag`:分別包含 (+1, -1, 0) 標記向上/向下/無異常的三元系列
* `ad_score`:異常分數
* `baseline`:根據分解序列的預測值

**有關演演演算法的更多**

此函數遵循以下步驟:
1. 使用參數呼叫[series_decompose()](series-decomposefunction.md)以建立基線和殘差系列
2. 在殘差序列上應用[series_outliers()](series-outliersfunction.md)計算ad_score序列
3. 通過在ad_score上應用閾值來分別標記向上/向下/無異常,計算ad_flag序列
 
**範例**

**1. 檢測每周季節性異常**

在下面的示例中,我們生成一個具有每周季節性的序列,然後向其中添加一些異常值。 `series_decompose_anomalies`自動檢測季節性並生成捕獲重複模式的基線。 我們添加的異常值可以在ad_score元件中清晰地發現。

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

:::image type="content" source="images/samples/series-decompose-anomalies1.png" alt-text="列分解異常 1":::

**2. 檢測每周季節性趨勢中的異常情況**

在此示例中,我們從前面的示例中向系列添加趨勢。 首先,我們使用預設`series_decompose_anomalies`參數運行,其中`avg`趨勢 預設值僅採用平均值,並且不計算趨勢,我們可以看到生成的基線不包含趨勢,與前面的示例相比不太準確,因此,由於差異較高,在數據中插入的一些異常值未被檢測到。

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
:::image type="content" source="images/samples/series-decompose-anomalies2.png" alt-text="列分解異常 2":::

接下來,我們運行相同的示例,但由於我們預期序列中的趨勢,我們在趨勢參數中指定`linefit`。 我們可以看到基線更接近輸入系列。 檢測到我們插入的所有異常值以及一些誤報(請參閱下一個調整閾值的範例)。

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

:::image type="content" source="images/samples/series-decompose-anomalies3.png" alt-text="系列分離例外 3":::

**3. 調整異常檢測閾值**

在前面的示例中,一些雜訊點被檢測為異常,在此示例中,我們將異常檢測閾值從預設的 1.5 到 2.5 的百分位數範圍增加到 2.5,以便僅檢測到更強的異常。 我們可以看到,現在只檢測到我們插入的數據中的異常值。

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

:::image type="content" source="images/samples/series-decompose-anomalies4.png" alt-text="系列分離異常 4":::
