---
title: series_decompose() - Azure 數據資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的series_decompose()。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/26/2019
ms.openlocfilehash: 375d63dd050840cd884fca0198511e71ac46f170
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663599"
---
# <a name="series_decompose"></a>series_decompose()

對序列應用分解轉換。  

以包含序列(動態數值陣列)的運算式作為輸入,並將其分解為季節性、趨勢和剩餘分量。
 
**語法**

`series_decompose(`*Series*`[,`Seasonality_thresholdTest_points*Seasonality**Seasonality_threshold*一個串季`,`的季節性趨勢`,`*Trend*`,`*Test_points*`])`

**引數**

* *列*: 動態陣列儲存格,是數值陣列,通常是[產生序列](make-seriesoperator.md)或[make_list](makelist-aggfunction.md)運算子輸出
* *季節性*:控制季節性分析的整數,包含其中一個
    * -1:使用[series_periods_detect(](series-periods-detectfunction.md)預設)自動檢測季節性。
    * 期間:在條柱數中指定預期週期的正整數。例如,如果序列位於 1h bin 中,則每周週期為 168 個條柱。
    * 0:無季節性(跳過提取此元件)。    
* *趨勢*: 控制趨勢分析的字串,包含以下之一:
    * "平均":將趨勢分量定義為平均值(x)(預設)
    * "線擬":使用線性回歸提取趨勢分量。
    * "無":沒有趨勢,跳過提取此元件。    
* *Test_points*:0(預設)或正整數,指定要從學習(回歸)過程中排除的序列末尾的點數。 此參數應設置為用於預測目的。
* *Seasonality_threshold*:*當季節性*設定為自動偵測時,季節性分數的閾值為 。,預設分數閾`0.6`值為 。 有關詳細資訊,請參閱[series_periods_detect](series-periods-detectfunction.md)。

**返回**

 該函數傳回以下的列列:

* `baseline`:該系列的預測值(季節性和趨勢成分之和,見下文)
* `seasonal`:季節性成分系列:
    * 未偵測到期間或顯式設定為 0: 常數 0
    * 如果偵測到或設定為正整數:同一階段中序列點的中位
* `trend`:趨勢元件系列
* `residual`:殘餘分量系列(即 x - 基線)
  

**注意事項**

* 元件執行順序:
    1. 擷取季節性系列
    2. 從 x 中減去它,生成非季節序列
    3. 從非季節序列中提取趨勢分量
    4. 建立基線 = 季節性 + 趨勢
    5. 建立殘差 = x - 基線
    
* 應啟用季節性和/或趨勢,否則函數是冗餘的,只需返回基線 = 0 和殘差 = x

**有關系列分解的更多**

此方法通常應用於預期要體現週期性和/或趨勢行為的指標的時間序列(例如服務流量和其他使用指標),以便預測將來的指標值和/或檢測異常指標。 此回歸過程的隱含假設是,除了(已知先驗的)季節性和趨勢行為外,時間序列是隨機和隨機分佈的;因此,我們可以預測未來指標值從季節性和趨勢成分(忽略殘餘部分),而我們可以檢測異常值僅基於殘餘零件的異常值。 更多細節可以在這本偉大的書[的時間序列分解章節](https://www.otexts.org/fpp/6)中找到。

**範例**

**1. 每周季節性**

在下面的示例中,我們生成一個具有每周季節性且沒有趨勢的序列,然後向其中添加一些異常值。 `series_decompose`查找自動檢測季節性,並生成與季節性成分幾乎相同的基線。 我們添加的異常值可以在殘差元件中清晰地看到。

```kusto
let ts=range t from 1 to 24*7*5 step 1 
| extend Timestamp = datetime(2018-03-01 05:00) + 1h * t 
| extend y = 2*rand() + iff((t/24)%7>=5, 10.0, 15.0) - (((t%24)/10)*((t%24)/10)) // generate a series with weekly seasonality
| extend y=iff(t==150 or t==200 or t==780, y-8.0, y) // add some dip outliers
| extend y=iff(t==300 or t==400 or t==600, y+8.0, y) // add some spike outliers
| summarize Timestamp=make_list(Timestamp, 10000),y=make_list(y, 10000);
ts 
| extend series_decompose(y)
| render timechart  
```

:::image type="content" source="images/samples/series-decompose1.png" alt-text="列分解 1":::

**2. 每週季節性趨勢**

在此示例中,我們從前面的示例中向系列添加趨勢。 首先,我們使用預設`series_decompose`參數運行,其中`avg`趨勢 預設值僅採用平均值,不計算趨勢,我們可以看到生成的基線不包含趨勢,並且與前面的示例相比不太準確,在觀察殘差中的趨勢時最為明顯。

```kusto
let ts=range t from 1 to 24*7*5 step 1 
| extend Timestamp = datetime(2018-03-01 05:00) + 1h * t 
| extend y = 2*rand() + iff((t/24)%7>=5, 5.0, 15.0) - (((t%24)/10)*((t%24)/10)) + t/72.0 // generate a series with weekly seasonality and ongoing trend
| extend y=iff(t==150 or t==200 or t==780, y-8.0, y) // add some dip outliers
| extend y=iff(t==300 or t==400 or t==600, y+8.0, y) // add some spike outliers
| summarize Timestamp=make_list(Timestamp, 10000),y=make_list(y, 10000);
ts 
| extend series_decompose(y)
| render timechart  
```

:::image type="content" source="images/samples/series-decompose2.png" alt-text="列分解 2":::

接下來,我們運行相同的示例,但由於我們預期序列中的趨勢,我們在趨勢參數中指定`linefit`。 我們可以看到,正趨勢被檢測到,基線更接近輸入序列。 殘差接近於零,只有異常值脫穎而出。我們可以看到圖表中系列上的所有元件。

```kusto
let ts=range t from 1 to 24*7*5 step 1 
| extend Timestamp = datetime(2018-03-01 05:00) + 1h * t 
| extend y = 2*rand() + iff((t/24)%7>=5, 5.0, 15.0) - (((t%24)/10)*((t%24)/10)) + t/72.0 // generate a series with weekly seasonality and ongoing trend
| extend y=iff(t==150 or t==200 or t==780, y-8.0, y) // add some dip outliers
| extend y=iff(t==300 or t==400 or t==600, y+8.0, y) // add some spike outliers
| summarize Timestamp=make_list(Timestamp, 10000),y=make_list(y, 10000);
ts 
| extend series_decompose(y, -1, 'linefit')
| render timechart  
```

:::image type="content" source="images/samples/series-decompose3.png" alt-text="列分解 3":::
