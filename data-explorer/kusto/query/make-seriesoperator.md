---
title: 製作系列操作員-Azure 資料總管
description: 本文說明 Azure 資料總管中的建立系列操作員。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/16/2020
ms.openlocfilehash: 56742b04386bfda9e2cdbaa40a85d2220f2373d5
ms.sourcegitcommit: 7fa9d0eb3556c55475c95da1f96801e8a0aa6b0f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2020
ms.locfileid: "91942347"
---
# <a name="make-series-operator"></a>make-series 運算子

沿著指定的軸建立一系列指定的匯總值。

```kusto
T | make-series sum(amount) default=0, avg(price) default=0 on timestamp from datetime(2016-01-01) to datetime(2016-01-10) step 1d by fruit, supplier
```

## <a name="syntax"></a>語法

*T* `| make-series` [*MakeSeriesParamters*] [*column* `=` ] *匯總* [ `default` `=` *DefaultValue*] [ `,` ...] `on` *AxisColumn* [ `from` *start*] [ `to` *end*] `step` *step* [ `by` [*Column* `=` ] *GroupExpression* [ `,` ...]]

## <a name="arguments"></a>引數

* ** 結果資料行的選擇性名稱。 預設值為衍生自運算式的名稱。
* *DefaultValue：* 將使用的預設值，而不是不存在的值。 如果沒有任何資料列具有 *AxisColumn* 和 *GroupExpression*的特定值，則在結果中，陣列的對應元素將會被指派 *DefaultValue*。 如果省略 *DefaultValue* ，則會假設為0。 
* *匯總：*[aggregation function](make-seriesoperator.md#list-of-aggregation-functions) `count()` 使用資料 `avg()` 行名稱做為引數的彙總函式（例如或）的呼叫。 請參閱 [彙總函式清單](make-seriesoperator.md#list-of-aggregation-functions)。 只有傳回數值結果的彙總函式可以與運算子搭配使用 `make-series` 。
* *AxisColumn：* 數列將會排序的資料行。 您可以將它視為時間軸，但除了 `datetime` 任何數數值型別之外。
* *啟動*： (選擇性) 要建立之每個數列的 *AxisColumn* 下限值。 *start*、 *end*和 *step* 可用來在指定範圍內建立 *AxisColumn* 值的陣列，並使用指定的 *步驟*。 所有 *匯總* 值都會分別針對這個陣列進行排序。 這個 *AxisColumn* 陣列也是輸出中的最後一個輸出資料行，該資料行的名稱與 *AxisColumn*相同。 如果未指定 *起始* 值，則開始是第一個 bin (步驟) ，其中每個數列都有資料。
* *end*： (選擇性) *AxisColumn*的高系結 (非內含) 值。 時間序列的最後一個索引小於此值 (*而且將會是小於* *end*) *之步驟*的整數倍數。 如果未提供 *結束* 值，它將會是最後一個 bin (步驟) 的上限，其中每個數列都有資料。
* *步驟*： *AxisColumn* 陣列的兩個連續元素之間的差異， (也就是) 的大小。
* *GroupExpression：* 在資料行上提供一組相異值的運算式。 通常是已提供有限的一組值的資料行名稱。 
* *MakeSeriesParameters*：零或多個 (空間分隔) 參數，以控制行為的 *名稱* `=` *值* 形式表示。 支援下列參數： 
  
  |名稱           |值                                        |描述                                                                                        |
  |---------------|-------------------------------------|------------------------------------------------------------------------------|
  |`kind`          |`nonempty`                               |當 make series 運算子的輸入為空白時，會產生預設結果|                                

## <a name="returns"></a>傳回

輸入資料列會排列成具有相同 `by` 運算式值和 `bin_at(` *AxisColumn* `, ` *步驟* `, ` *開始* `)` 運算式的群組。 然後指定的彙總函式會針對每個群組進行計算，以便為每個群組產生資料列。 結果中會包含資料 `by` 行、 *AxisColumn* 資料行，以及每個計算匯總的至少一個資料行。 不支援多個資料行或非數值結果的 (匯總。 ) 

這個中繼結果有許多資料列，因為 `by` 和 `bin_at(` *AxisColumn*的 `, ` *步驟* `, ` *開始* `)` 值有相異的組合。

最後，會將中繼結果中的資料列排列成具有相同運算式值的群組 `by` ，並將所有匯總值排列成陣列 (`dynamic`) 類型的值。 每個匯總都有一個資料行包含具有相同名稱的陣列。 範圍函數輸出中的最後一個資料行，包含所有 *AxisColumn* 值。 它的值會針對所有資料列重複。 

由於預設會填滿遺漏的 bin 值，因此產生的樞紐分析表會有相同數目的 bin (也就是所有數列的匯總值)   

**注意**

雖然您可以為匯總與群組運算式提供任意運算式，但更有效率的方式是使用簡單的資料行名稱。

**替代語法**

*T* `| make-series` [*Column* `=` ]*匯總*[ `default` `=` *DefaultValue*] [ `,` ...] `on` *AxisColumn* `in` `range(` *start* `,` *stop* `,` *step* `)` [ `by` [*Column* `=` ] *GroupExpression* [ `,` ...]]

從替代語法產生的數列與主要語法有兩個不同之處：
* *停止*值為內含。
* 分類收納索引軸是使用 bin ( # A1 來產生，而不是 bin_at ( # A3，這表示 *啟動* 可能不會包含在產生的數列中。

建議使用「構成系列」的主要語法，而不是替代語法。

**散發和隨機播放**

`make-series` 支援 `summarize` 使用語法提示. shufflekey 的 [shufflekey 提示](shufflequery.md) 。

## <a name="list-of-aggregation-functions"></a>彙總函式的清單

|函式|描述|
|--------|-----------|
|[任何 ( # B1 ](any-aggfunction.md)|傳回群組的隨機非空白值|
|[avg ( # B1 ](avg-aggfunction.md)|傳回整個群組的平均值|
|[avgif()](avgif-aggfunction.md)|傳回具有群組述詞的平均值|
|[計數 ( # B1 ](count-aggfunction.md)|傳回群組的計數|
|[countif()](countif-aggfunction.md)|傳回包含群組述詞的計數|
|[dcount()](dcount-aggfunction.md)|傳回群組元素的近似相異計數|
|[dcountif()](dcountif-aggfunction.md)|傳回具有群組述詞的近似相異計數|
|[max()](max-aggfunction.md)|傳回整個群組的最大值|
|[maxif()](maxif-aggfunction.md)|使用群組的述詞傳回最大值|
|[min()](min-aggfunction.md)|傳回整個群組的最小值|
|[minif()](minif-aggfunction.md)|使用群組的述詞傳回最小值|
|[stdev ( # B1 ](stdev-aggfunction.md)|傳回整個群組的標準差|
|[sum ( # B1 ](sum-aggfunction.md)|傳回群組內元素的總和|
|[sumif()](sumif-aggfunction.md)|傳回具有群組述詞之元素的總和|
|[差異 ( # B1 ](variance-aggfunction.md)|傳回整個群組的變異數|

## <a name="list-of-series-analysis-functions"></a>數列分析函數清單

|函式|描述|
|--------|-----------|
|[series_fir()](series-firfunction.md)|套用 [有限的脈衝回應](https://en.wikipedia.org/wiki/Finite_impulse_response) 篩選|
|[series_iir()](series-iirfunction.md)|套用 [無限脈衝回應](https://en.wikipedia.org/wiki/Infinite_impulse_response) 篩選|
|[series_fit_line()](series-fit-linefunction.md)|尋找最近似輸入的直線|
|[series_fit_line_dynamic()](series-fit-line-dynamicfunction.md)|尋找最近似輸入的行，傳回動態物件|
|[series_fit_2lines()](series-fit-2linesfunction.md)|尋找最近似輸入的兩行|
|[series_fit_2lines_dynamic()](series-fit-2lines-dynamicfunction.md)|尋找兩行，這是輸入的最佳近似值，傳回動態物件|
|[series_outliers()](series-outliersfunction.md)|將序列中的異常點評分|
|[series_periods_detect()](series-periods-detectfunction.md)|尋找存在於時間序列中最重要的期間|
|[series_periods_validate()](series-periods-validatefunction.md)|檢查時間序列是否包含指定長度的定期模式|
|[series_stats_dynamic()](series-stats-dynamicfunction.md)|傳回具有一般統計資料的多個資料行 (min/max/變異/stdev/average) |
|[series_stats()](series-statsfunction.md)|產生具有一般統計資料 (min/max/變異/stdev/average) 的動態值|
  
## <a name="list-of-series-interpolation-functions"></a>數列插補函數的清單

|函式|描述|
|--------|-----------|
|[series_fill_backward()](series-fill-backwardfunction.md)|在數列中執行遺漏值的後置填滿插補|
|[series_fill_const()](series-fill-constfunction.md)|以指定的常數值取代數列中的遺漏值|
|[series_fill_forward()](series-fill-forwardfunction.md)|在數列中執行遺漏值的向前填滿插補|
|[series_fill_linear()](series-fill-linearfunction.md)|在數列中執行遺漏值的線性插補|

* 注意：插補函數依預設會假設 `null` 為遺漏值。 因此 `default=` *double* `null` `make-series` ，如果您想要使用數列的插補函數，請在中指定 double () 。 

## <a name="example"></a>範例
  
 資料表，顯示每個供應商以指定範圍排序的每個供應商的數位和平均價格的陣列。 每個不同的水果和供應商組合的輸出中都有一個資料列。 輸出資料行會顯示：計數、平均和整個時間軸的水果、供應商和陣列 (從2016-01-01 到 2016-01-10) 。 所有陣列都會依各自的時間戳記排序，且在此範例) 中，所有的間距都會填入預設值 (0。 其他所有輸入資料行則會遭到忽略。
  
```kusto
T | make-series PriceAvg=avg(Price) default=0
on Purchase from datetime(2016-09-10) to datetime(2016-09-13) step 1d by Supplier, Fruit
```

:::image type="content" source="images/make-seriesoperator/makeseries.png" alt-text="三個數據表。第一個清單會列出未經處理的資料，第二個只會有不同的供應商水果和日組合，而第三個則包含構成系列結果。":::  

<!-- csl: https://help.kusto.windows.net:443/Samples --> 
```kusto
let data=datatable(timestamp:datetime, metric: real)
[
  datetime(2016-12-31T06:00), 50,
  datetime(2017-01-01), 4,
  datetime(2017-01-02), 3,
  datetime(2017-01-03), 4,
  datetime(2017-01-03T03:00), 6,
  datetime(2017-01-05), 8,
  datetime(2017-01-05T13:40), 13,
  datetime(2017-01-06), 4,
  datetime(2017-01-07), 3,
  datetime(2017-01-08), 8,
  datetime(2017-01-08T21:00), 8,
  datetime(2017-01-09), 2,
  datetime(2017-01-09T12:00), 11,
  datetime(2017-01-10T05:00), 5,
];
let interval = 1d;
let stime = datetime(2017-01-01);
let etime = datetime(2017-01-10);
data
| make-series avg(metric) on timestamp from stime to etime step interval 
```
  
|avg_metric|timestamp|
|---|---|
|[4.0、3.0、5.0、0.0、10.5、4.0、3.0、8.0、6.5]|["2017-01-01T00：00： 00.0000000 Z"，"2017-01-02T00：00： 00.0000000 Z"，"2017-01-03T00：00： 00.0000000 Z"，"2017-01-04T00：00： 00.0000000 Z"，"2017-01-05T00：00： 00.0000000 Z"，"2017-01-06T00：00： 00.0000000 Z"，"2017-01-07T00：00： 00.0000000 Z"，"2017-01-08T00：00： 00.0000000 Z"，"2017-01-09T00：00： 00.0000000 Z"]|  


當的輸入 `make-series` 是空的時，的預設行為 `make-series` 也會產生空的結果。

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let data=datatable(timestamp:datetime, metric: real)
[
  datetime(2016-12-31T06:00), 50,
  datetime(2017-01-01), 4,
  datetime(2017-01-02), 3,
  datetime(2017-01-03), 4,
  datetime(2017-01-03T03:00), 6,
  datetime(2017-01-05), 8,
  datetime(2017-01-05T13:40), 13,
  datetime(2017-01-06), 4,
  datetime(2017-01-07), 3,
  datetime(2017-01-08), 8,
  datetime(2017-01-08T21:00), 8,
  datetime(2017-01-09), 2,
  datetime(2017-01-09T12:00), 11,
  datetime(2017-01-10T05:00), 5,
];
let interval = 1d;
let stime = datetime(2017-01-01);
let etime = datetime(2017-01-10);
data
| limit 0
| make-series avg(metric) default=1.0 on timestamp from stime to etime step interval 
| count 
```

|計數|
|---|
|0|


`kind=nonempty`在中使用 `make-series` 將會產生預設值的非空白結果：

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let data=datatable(timestamp:datetime, metric: real)
[
  datetime(2016-12-31T06:00), 50,
  datetime(2017-01-01), 4,
  datetime(2017-01-02), 3,
  datetime(2017-01-03), 4,
  datetime(2017-01-03T03:00), 6,
  datetime(2017-01-05), 8,
  datetime(2017-01-05T13:40), 13,
  datetime(2017-01-06), 4,
  datetime(2017-01-07), 3,
  datetime(2017-01-08), 8,
  datetime(2017-01-08T21:00), 8,
  datetime(2017-01-09), 2,
  datetime(2017-01-09T12:00), 11,
  datetime(2017-01-10T05:00), 5,
];
let interval = 1d;
let stime = datetime(2017-01-01);
let etime = datetime(2017-01-10);
data
| limit 0
| make-series kind=nonempty avg(metric) default=1.0 on timestamp from stime to etime step interval 
```

|avg_metric|timestamp|
|---|---|
|[<br>  1.0、<br>  1.0、<br>  1.0、<br>  1.0、<br>  1.0、<br>  1.0、<br>  1.0、<br>  1.0、<br>  1.0<br>]|[<br>  "2017-01-01T00：00： 00.0000000 Z"，<br>  "2017-01-02T00：00： 00.0000000 Z"，<br>  "2017-01-03T00：00： 00.0000000 Z"，<br>  "2017-01-04T00：00： 00.0000000 Z"，<br>  "2017-01-05T00：00： 00.0000000 Z"，<br>  "2017-01-06T00：00： 00.0000000 Z"，<br>  "2017-01-07T00：00： 00.0000000 Z"，<br>  "2017-01-08T00：00： 00.0000000 Z"，<br>  "2017-01-09T00：00： 00.0000000 Z"<br>]|
