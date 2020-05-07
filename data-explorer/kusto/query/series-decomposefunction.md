---
title: series_decompose （）-Azure 資料總管
description: 本文說明 Azure 資料總管中的 series_decompose （）。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/26/2019
ms.openlocfilehash: 5394eefad37195833c0c5ebb94325bb540d1f520
ms.sourcegitcommit: 9fe6ee7db15a5cc92150d3eac0ee175f538953d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2020
ms.locfileid: "82907223"
---
# <a name="series_decompose"></a>series_decompose()

在數列上套用分解轉換。  

接受包含數列（動態數值陣列）做為輸入的運算式，並將其會 decompose 至季節性、趨勢和剩餘元件。
 
**語法**

`series_decompose(`*數列* `[,` *季節性*`,` *Seasonality_threshold* *Test_points* *Trend*趨勢 Test_points Seasonality_threshold`,` `,``])`

**引數**

* *數列*：動態陣列資料格，這是數值的陣列，通常是[make 系列](make-seriesoperator.md)或[make_list](makelist-aggfunction.md)運算子的結果輸出
* *季節性*：控制季節性分析的整數，其中包含任一項
    * -1：使用[series_periods_detect](series-periods-detectfunction.md) （預設值）自動偵測季節性。
    * period：正整數，指定以區間數表示的預期時間。例如，如果數列是在 1-h 的 bin 中，則每週的期間為168的 bin。
    * 0：無季節性（略過解壓縮此元件）。    
* *趨勢*：控制趨勢分析的字串，其中包含下列其中一個值：
    * "avg"：將趨勢元件定義為平均（x）（預設值）
    * "linefit"：使用線性回歸來將趨勢元件解壓縮。
    * "none"：沒有趨勢，略過解壓縮此元件。    
* *Test_points*：0（預設值）或正整數，指定要從學習（回歸）進程中排除之數列結尾的點數目。 此參數應針對預測用途進行設定。
* *Seasonality_threshold*：當*季節性*設定為自動偵測時，季節性分數的閾值，預設的分數臨界`0.6`值為。 如需詳細資訊，請參閱[series_periods_detect](series-periods-detectfunction.md)。

**退貨**

 函式會傳回下列各自的數列：

* `baseline`：數列的預測值（季節性和趨勢元件的總和，請參閱下文）。
* `seasonal`：季節性元件的數列：
    * 如果未偵測到期間，或未明確設定為0：常數0。
    * 如果偵測到或設定為正整數：相同階段中數列點的中間值
* `trend`：趨勢元件的數列。
* `residual`：剩餘元件的數列（也就是 x 基準）。
  

**注意事項**

* 元件執行順序：
    1. 摘錄季節性數列
    2. 從 x 減去，產生 deseasonal 數列
    3. 從 deseasonal 系列摘錄趨勢元件
    4. 建立基準 = 季節性 + 趨勢
    5. 建立剩餘 = x 基準
    
* 季節性和、或趨勢都應該啟用。 否則，函數是多餘的，而且只會傳回基準 = 0 和剩餘 = x。

**深入瞭解數列分解**

這個方法通常會套用到預期會產生資訊清單和/或趨勢行為的時間序列。 您可以使用方法來預測未來的度量值和/或偵測異常值。 此回歸程式的隱含假設是除了季節性和趨勢行為之外，時間序列會隨機並隨機散發。 從季節性和趨勢元件預測未來的度量值，同時忽略剩餘的部分。 僅根據最差的部分，偵測異常值。 如需進一步的詳細資料，請參閱[時間序列分解一章](https://www.otexts.org/fpp/6)。

**範例**

**每週季節性**

在下列範例中，我們會產生一個包含每週季節性的數列，而沒有趨勢，然後在其中新增一些極端值。 `series_decompose`尋找並自動偵測季節性，並產生與季節性元件幾乎完全相同的基準。 我們所新增的極端值可以在殘差元件中清楚看出。

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

:::image type="content" source="images/samples/series-decompose1.png" alt-text="數列分解1":::

**具有趨勢的每週季節性**

在此範例中，我們會將趨勢新增到上一個範例中的數列。 首先，我們會`series_decompose`使用預設參數來執行。 [趨勢`avg` ] 預設值只會採用平均值，而不會計算趨勢。 產生的基準不包含趨勢。 觀察殘差的趨勢時，此範例會明顯比前一個範例更不精確。

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

:::image type="content" source="images/samples/series-decompose2.png" alt-text="數列分解2":::

接下來，我們會重新執行相同的範例。 因為我們預期數列中的趨勢，所以我們在 trend `linefit`參數中指定。 我們可以看到偵測到正向趨勢，而且基準比輸入數列更接近。 殘差會接近零，只有極端值才會醒目。我們可以在圖表中看到數列上的所有元件。

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

:::image type="content" source="images/samples/series-decompose3.png" alt-text="數列分解3":::
