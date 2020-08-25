---
title: 'series_decompose ( # A1-Azure 資料總管'
description: '本文描述 Azure 資料總管中 ( # A1 series_decompose。'
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/26/2019
ms.openlocfilehash: 9ff0df578f174bc6964e39e799b91068f89a28e4
ms.sourcegitcommit: 05489ce5257c0052aee214a31562578b0ff403e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/25/2020
ms.locfileid: "88793941"
---
# <a name="series_decompose"></a>series_decompose()

在數列上套用分解轉換。  

採用包含數列 (動態數值) 陣列的運算式作為輸入，並將其分解至季節性、趨勢和剩餘元件。
 
## <a name="syntax"></a>語法

`series_decompose(`*系列* `[,`*季節性* `,`*趨勢* `,`*Test_points* `,`*Seasonality_threshold*`])`

## <a name="arguments"></a>引數

* *數列*：動態陣列資料格，這是數值的陣列，通常是 [構成數列](make-seriesoperator.md) 或 [make_list](makelist-aggfunction.md) 運算子的結果輸出
* *季節性*：控制季節性分析的整數，其中包含下列其中一項：
    * -1：使用 [series_periods_detect](series-periods-detectfunction.md) (預設) 的自動偵測季節性。
    * 句號：指定 bin 數目的預期期間的正整數。例如，如果數列在 1-h bin 中，則每週期間是168的 bin。
    * 0：無季節性 (略過將此元件解壓縮) 。    
* *趨勢*：控制趨勢分析的字串，其中包含下列其中一個值：
    * 「平均」：將趨勢元件定義為平均 (x)  (預設) 
    * "linefit"：使用線性回歸來將趨勢元件解壓縮。
    * "none"：沒有趨勢，請略過解壓縮此元件。    
* *Test_points*： 0 (預設) 或正整數，指定要從學習 (回歸) 程式中排除之數列結尾的點數目。 此參數應針對預測用途進行設定。
* *Seasonality_threshold*：當 *季節性* 設為自動偵測時，季節性分數的閾值，預設的分數閾值為 `0.6` 。 如需詳細資訊，請參閱 [series_periods_detect](series-periods-detectfunction.md)。

**返回**

 函數會傳回下列個別數列：

* `baseline`：數列的預測值 (季節性和趨勢元件的總和，請參閱下面的) 。
* `seasonal`：季節性元件的數列：
    * 如果未偵測到期間，或明確設定為0：常數0。
    * 如果偵測到或設定為正整數：相同階段中數列點的中間值
* `trend`：趨勢元件的數列。
* `residual`：剩餘元件的數列 (也就是 x 基準) 。
  

**備註**

* 元件執行順序：
    1. 將季節性系列解壓縮
    2. 從 x 減去它，產生 deseasonal 系列
    3. 從 deseasonal 系列將 trend 元件解壓縮
    4. 建立基準 = 季節性 + 趨勢
    5. 建立剩餘 = x 基準
    
* 季節性和（或）應該啟用趨勢。 否則，此函式會是多餘的，而且只會傳回基準 = 0 和剩餘 = x。

**更多關於序列分解的資訊**

這個方法通常會套用到預期會出現在資訊清單定期和/或趨勢行為的時間序列。 您可以使用方法來預測未來的度量值和/或偵測異常值。 此回歸進程的隱含假設是除了季節性和趨勢行為之外，還會隨機和隨機散發時間序列。 預測季節性和趨勢元件的未來度量值，同時忽略剩餘部分。 根據僅限剩餘部分的極端值偵測來偵測異常值。 您可以在 [時間序列分解一章](https://otexts.com/fpp2/decomposition.html)中找到進一步的詳細資料。

## <a name="examples"></a>範例

**每週季節性**

在下列範例中，我們會產生包含每週季節性的數列，而不會產生趨勢，然後在其中新增一些極端值。 `series_decompose` 會尋找並自動偵測季節性，並產生與季節性元件幾乎完全相同的基準。 我們新增的極端值可在殘差元件中清楚看出。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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

在此範例中，我們會將趨勢新增至上述範例中的數列。 首先，我們會 `series_decompose` 以預設參數執行。 趨勢 `avg` 預設值只會採用平均值，而不會計算趨勢。 產生的基準不包含趨勢。 觀察殘差的趨勢時，很明顯地，此範例比前一個範例更不精確。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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

接下來，我們會重新執行相同的範例。 由於我們預期序列中會有趨勢，因此我們 `linefit` 在 trend 參數中指定。 我們可以看到偵測到正面的趨勢，且基準與輸入數列較接近。 殘差會接近零，而且只有極端值會脫離。我們可以在圖表中看到數列上的所有元件。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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
