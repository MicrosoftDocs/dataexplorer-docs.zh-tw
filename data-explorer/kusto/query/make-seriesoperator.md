---
title: make 系列運算子-Azure 資料總管 |Microsoft Docs
description: 本文說明 Azure 資料總管中的 make 系列操作員。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/16/2020
ms.openlocfilehash: 66211cfcb33f97ba5f58d82e7ee4a8a8c7631e96
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618425"
---
# <a name="make-series-operator"></a>make-series 運算子

沿著指定的軸，建立一系列指定的匯總值。 

```kusto
T | make-series sum(amount) default=0, avg(price) default=0 on timestamp from datetime(2016-01-01) to datetime(2016-01-10) step 1d by fruit, supplier
```

**語法**

*T* `| make-series` [*MakeSeriesParamters*] [*Column* `=`]*匯總*[`default` `=` *DefaultValue*] [`,` ...]`on` *AxisColumn* [`from` *start*] [`to` *end*] `step` *step* [`by` [*Column* `=`] *GroupExpression* [`,` ...]]

**引數**

* ** 結果資料行的選擇性名稱。 預設值為衍生自運算式的名稱。
* *DefaultValue：* 將使用的預設值，而不是不存在的值。 如果沒有具有*AxisColumn*和*GroupExpression*之特定值的資料列，則會在結果中使用*DefaultValue*來指派陣列的對應元素。 如果`default =`省略*DefaultValue* ，則假設為0。 
* *匯總：* 以資料行名稱做為引數`count()`呼叫`avg()`[彙總函式](make-seriesoperator.md#list-of-aggregation-functions)（例如或）。 請參閱 [彙總函式清單](make-seriesoperator.md#list-of-aggregation-functions)。 請注意，只有傳回數值結果的彙總函式可以搭配`make-series`運算子使用。
* *AxisColumn：* 將排序數列的資料行。 它可以視為時程表，但除了接受任何`datetime`數數值型別之外。
* *start*：（選擇性）將會建立數列之每個*AxisColumn*的下限值。 *start*、 *end*和*step*是用來在給定範圍內建立*AxisColumn*值的陣列，並使用指定的*步驟*。 所有*匯總*值會分別針對此陣列進行排序。 這個*AxisColumn*陣列也是輸出中的最後一個輸出資料行，其名稱與*AxisColumn*相同。 如果未指定*start*值，start 就是在每個數列中具有資料的第一個 bin （step）。
* *end*：（選擇性） *AxisColumn*的上限（非內含）值，時間序列的最後一個索引會小於此值（而且會*開始*加上小於*end*的*步驟*整數倍數）。 如果未提供*結束*值，它會是最後一個 bin （步驟）的上限，其中包含每個數列的資料。
* *步驟*： *AxisColumn*陣列的兩個連續專案之間的差異（亦即，bin 大小）。
* ** 可提供一組相異值的資料行運算式。 通常是已提供有限的一組值的資料行名稱。 
* *MakeSeriesParameters*：*名稱* `=` *值*格式為零或多個（以空格分隔）的參數，可控制行為。 支援下列參數： 
  
  |名稱           |值                                        |描述                                                                                        |
  |---------------|-------------------------------------|------------------------------------------------------------------------------|
  |`kind`          |`nonempty`                               |當 make 系列運算子的輸入為空白時，會產生預設結果|                                

**傳回**

輸入資料列`by`會安排在具有相同運算式和`bin_at(` *AxisColumn*`, `*步驟*`, `*開始*`)`運算式值的群組中。 然後指定的彙總函式會針對每個群組進行計算，以便為每個群組產生資料列。 結果會包含資料`by`行、 *AxisColumn*資料行，以及每個計算匯總的至少一個資料行。 （不支援多個資料行或非數值結果的匯總）。

此中繼結果具有與`by` `bin_at(` *AxisColumn*`, `*步驟*`, `*開始*`)`值的不同組合的資料列數目。

最後，將中繼結果中的資料列排列成具有相同`by`運算式值的群組，並將所有匯總值排列成陣列（類型`dynamic`的值）。 針對每個匯總，有一個資料行包含具有相同名稱的陣列。 範圍函數輸出中具有所有*AxisColumn*值的最後一個資料行。 它的值會針對所有資料列重複。 

請注意，由於 [填滿遺漏的 bin] 預設值，產生的樞紐分析表與所有數列都有相同數目的 bin （也就是匯總的值）  

**注意**

雖然您可以為匯總和群組運算式提供任意運算式，但更有效率的方式是使用簡單的資料行名稱。

**替代語法**

*T* `| make-series` [*Column* `=`]*匯總*[`default` `=` *DefaultValue*] [`,` ...]`on` *AxisColumn* `,` *step* `,` *start* `by` *stop* `=` *GroupExpression* *Column* start stop step`)` [[Column] GroupExpression [...]]`,` `in` `range(`

從替代語法產生的數列與2個層面的主要語法不同：
* [*停止*] 值為 [內含]。
* 分類收納索引軸是以 bin （）產生，而不是 bin_at （），這表示*啟動*不一定會包含在產生的數列中。

建議使用 make 系列的主要語法，而不是替代語法。

**散發和隨機播放**

`make-series`支援`summarize`使用語法提示. shufflekey 的[shufflekey 提示](shufflequery.md)。

## <a name="list-of-aggregation-functions"></a>彙總函式的清單

|函式|說明|
|--------|-----------|
|[any （）](any-aggfunction.md)|傳回群組的隨機非空白值|
|[avg （）](avg-aggfunction.md)|Retuns 整個群組的平均值|
|[count （）](count-aggfunction.md)|傳回群組的計數|
|[countif()](countif-aggfunction.md)|傳回包含群組之述詞的計數|
|[dcount()](dcount-aggfunction.md)|傳回群組元素的近似相異計數|
|[最大值（）](max-aggfunction.md)|傳回整個群組的最大值|
|[min （）](min-aggfunction.md)|傳回整個群組的最小值|
|[stdev （）](stdev-aggfunction.md)|傳回整個群組的標準差|
|[sum （）](sum-aggfunction.md)|傳回遷移群組的元素總和|
|[variance()](variance-aggfunction.md)|傳回整個群組的變異數|

## <a name="list-of-series-analysis-functions"></a>數列分析函數清單

|函式|說明|
|--------|-----------|
|[series_fir()](series-firfunction.md)|套用[有限的脈衝回應](https://en.wikipedia.org/wiki/Finite_impulse_response)篩選|
|[series_iir()](series-iirfunction.md)|套用[無限脈衝回應](https://en.wikipedia.org/wiki/Infinite_impulse_response)篩選|
|[series_fit_line()](series-fit-linefunction.md)|尋找直線，這是輸入的最佳近似值|
|[series_fit_line_dynamic()](series-fit-line-dynamicfunction.md)|尋找最近似輸入的線條，傳回動態物件|
|[series_fit_2lines()](series-fit-2linesfunction.md)|尋找兩行，這是輸入的最佳近似值|
|[series_fit_2lines_dynamic()](series-fit-2lines-dynamicfunction.md)|尋找兩行，這是輸入的最佳近似值，傳回動態物件|
|[series_outliers()](series-outliersfunction.md)|將數列中的異常點評分|
|[series_periods_detect()](series-periods-detectfunction.md)|尋找存在於時間序列中最重要的期間|
|[series_periods_validate()](series-periods-validatefunction.md)|檢查時間序列是否包含指定長度的定期模式|
|[series_stats_dynamic()](series-stats-dynamicfunction.md)|傳回具有共同統計資料的多個資料行（最小/最大/變異數/stdev/平均）|
|[series_stats()](series-statsfunction.md)|產生具有一般統計資料的動態值（最小/最大/變異數/stdev/平均）|
  
## <a name="list-of-series-interpolation-functions"></a>數列插補函式的清單
|函式|描述|
|--------|-----------|
|[series_fill_backward()](series-fill-backwardfunction.md)|執行數列中遺漏值的後置填滿插補|
|[series_fill_const()](series-fill-constfunction.md)|以指定的常數值取代數列中的遺漏值|
|[series_fill_forward()](series-fill-forwardfunction.md)|對數列中的遺漏值執行向前填滿插補|
|[series_fill_linear()](series-fill-linearfunction.md)|執行數列中遺漏值的線性插補|

* 注意：插補函式預設`null`會假設為遺漏值。 因此，如果您想要`default=`使用數列`null`的插`make-series`補函式，建議您在中指定*double*（）。 

## <a name="example"></a>範例
  
 一份資料表，顯示每個供應商依時間戳記與指定範圍排序之每個水果的數位和平均價格陣列。 針對水果和供應商的每個相異組合，輸出中會有一個資料列。 [輸出] 資料行會顯示水果、供應商和下列各項的陣列：計數、平均和整段時間（2016-01-01 到2016-01-10）。 所有陣列都會依各自的時間戳記排序，而所有的間隙都會填入預設值（在此範例中為0）。 其他所有輸入資料行則會遭到忽略。
  
```kusto
T | make-series PriceAvg=avg(Price) default=0
on Purchase from datetime(2016-09-10) to datetime(2016-09-13) step 1d by Supplier, Fruit
```

:::image type="content" source="images/make-seriesoperator/makeseries.png" alt-text="Makeseries":::  
  
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
|[4.0，3.0，5.0，0.0，10.5，4.0，3.0，8.0，6.5]|["2017-01-01T00：00： 00.0000000 Z"，"2017-01-02T00：00： 00.0000000 Z"，"2017-01-03T00：00： 00.0000000 Z"，"2017-01-04T00：00： 00.0000000 Z"、"2017-01-05T00：00： 00.0000000 Z"、"2017-01-06T00：00： 00.0000000 Z"、"2017-01-07T00：00： 00.0000000 Z"、"2017-01-08T00：00： 00.0000000 Z"、"2017-01-09T00：00： 00.0000000 Z"]|  


當的輸入`make-series`是空的時，的預設行為`make-series`也會產生空的結果。

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


在`kind=nonempty`中`make-series`使用將會產生預設值的非空白結果：

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