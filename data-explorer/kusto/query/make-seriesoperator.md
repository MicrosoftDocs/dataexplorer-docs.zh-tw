---
title: make-series 運算子 - Azure 資料總管
description: 本文說明 Azure 資料總管中的 make-series 運算子。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/16/2020
ms.localizationpriority: high
ms.openlocfilehash: 6357afeb0a5673584e27b84a231e3c65f897b8fc
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "95512361"
---
# <a name="make-series-operator"></a>make-series 運算子

沿著指定的軸建立一系列指定的彙總值。

```kusto
T | make-series sum(amount) default=0, avg(price) default=0 on timestamp from datetime(2016-01-01) to datetime(2016-01-10) step 1d by fruit, supplier
```

## <a name="syntax"></a>語法

*T* `| make-series` [*MakeSeriesParamters*] [*Column* `=`] *Aggregation* [`default` `=` *DefaultValue*] [`,` ...] `on` *AxisColumn* [`from` *start*] [`to` *end*] `step` *step* [`by` [*Column* `=`] *GroupExpression* [`,` ...]]

## <a name="arguments"></a>引數

*  結果資料行的選擇性名稱。 預設值為衍生自運算式的名稱。
* *DefaultValue：* 將使用的預設值，而不是不存在的值。 如果沒有任何資料列含有 AxisColumn 和 GroupExpression 的具體值，則在結果中，將會指派 DefaultValue 給對應的陣列元素。 如果省略 *DefaultValue*，則會採用 0。 
* *Aggregation：* `count()` 或 `avg()` 等[彙總函式](make-seriesoperator.md#list-of-aggregation-functions)的呼叫，以資料行名稱作為引數。 請參閱[彙總函式清單](make-seriesoperator.md#list-of-aggregation-functions)。 只有傳回數值結果的彙總函式可以搭配 `make-series` 運算子使用。
* *AxisColumn：* 數列會據以排序的資料行。 可視為時間軸，但除了 `datetime` 以外，可接受任何數值類型。
* *start*：(選擇性) 要建立之每個數列的 *AxisColumn* 下限值。 *start*、*end* 和 *step* 用於在指定的範圍內建立 *AxisColumn* 值的陣列，並使用指定的 *step*。 所有 *Aggregation* 值都會分別針對此陣列進行排序。 此 *AxisColumn* 陣列也是輸出中的最後一個輸出資料行，其名稱與 *AxisColumn* 相同。 如果未指定 *start* 值，start 就是在每個數列中具有資料的第一個間隔 (step)。
* *end*：(選擇性) *AxisColumn* 的上限 (不包含在內) 值。 時間序列的最後一個索引會小於此值 (而且會是 *start* 加上小於 *end* 的 *step* 整數倍數)。 如果未提供 *end* 值，則會是最後一個間隔 (step) 的上限，其中包含每個數列的資料。
* *step*：*AxisColumn* 陣列的兩個連續元素之間的差異 (也就是，間隔大小)。
* *GroupExpression：* 可提供一組相異值的資料行運算式。 通常是已提供有限的一組值的資料行名稱。 
* *MakeSeriesParameters*：格式為 *Name* `=` *Value* 的零個或多個 (以空格分隔) 參數，此種參數可控制行為。 支援下列參數： 
  
  |名稱           |值                                        |描述                                                                                        |
  |---------------|-------------------------------------|------------------------------------------------------------------------------|
  |`kind`          |`nonempty`                               |當 make-series 運算子的輸入空白時，就會產生預設結果|                                

## <a name="returns"></a>傳回

輸入資料列會各自分組到具有相同 `by` 運算式和 `bin_at(`*AxisColumn*`, `*step*`, `*start*`)` 運算式值的群組。 然後指定的彙總函式會針對每個群組進行計算，以便為每個群組產生資料列。 結果會包含 `by` 資料行、*AxisColumn* 資料行，而且每個經過計算的彙總至少會有一個資料行。 (不支援多個資料行或非數值結果的彙總。)

此中繼結果具有的資料列數與相異的 `by` 和 `bin_at(`*AxisColumn*`, `*step*`, `*start*`)` 值組合一樣多。

最後，中繼結果中的資料列會各自分組到具有相同 `by` 運算式值的群組，而且所有彙總值都會排列成陣列(`dynamic` 類型的值)。 每個彙總都有一個資料行，其中包含具有相同名稱的陣列。 範圍函式輸出中的最後一個資料行，其中包含所有 *AxisColumn* 值。 其值會針對所有資料列重複。 

由於預設值會填滿遺漏間隔，因此所產生的樞紐分析表對所有數列具有相同數目的間隔 (也就是彙總值)  

**注意**

雖然您可以為彙總與群組運算式提供任意運算式，但更有效率的方法是使用簡單的資料行名稱。

**替代語法**

*T* `| make-series` [*Column* `=`] *Aggregation* [`default` `=` *DefaultValue*] [`,` ...] `on` *AxisColumn* `in` `range(`*start*`,` *stop*`,` *step*`)` [`by` [*Column* `=`] *GroupExpression* [`,` ...]]

從替代語法產生的數列與主要語法有兩個不同之處：
* *stop* 值包含在內。
* 索引軸分箱是以 bin() (而不是 bin_at()) 產生，這表示 *start* 可能不會包含在所產生的數列中。

建議使用 make-series 的主要語法，而不是替代語法。

**Distribution 和 Shuffle**

`make-series` 支援使用 hint.shufflekey 語法的 `summarize` [shufflekey 提示](shufflequery.md)。

## <a name="list-of-aggregation-functions"></a>彙總函式清單

|函式|描述|
|--------|-----------|
|[any()](any-aggfunction.md)|傳回群組的隨機非空白值|
|[avg()](avg-aggfunction.md)|傳回整個群組的平均值|
|[avgif()](avgif-aggfunction.md)|使用群組的述詞傳回平均值|
|[count()](count-aggfunction.md)|傳回群組的計數|
|[countif()](countif-aggfunction.md)|使用群組的述詞傳回計數|
|[dcount()](dcount-aggfunction.md)|傳回群組元素的近似相異計數|
|[dcountif()](dcountif-aggfunction.md)|使用群組的述詞傳回近似相異計數|
|[max()](max-aggfunction.md)|傳回整個群組的最大值|
|[maxif()](maxif-aggfunction.md)|使用群組的述詞傳回最大值|
|[min()](min-aggfunction.md)|傳回整個群組的最小值|
|[minif()](minif-aggfunction.md)|使用群組的述詞傳回最小值|
|[stdev()](stdev-aggfunction.md)|傳回整個群組的標準差|
|[sum()](sum-aggfunction.md)|傳回群組內元素的總和|
|[sumif()](sumif-aggfunction.md)|使用群組的述詞傳回元素的總和|
|[variance()](variance-aggfunction.md)|傳回整個群組的變異數|

## <a name="list-of-series-analysis-functions"></a>數列分析函式清單

|函式|描述|
|--------|-----------|
|[series_fir()](series-firfunction.md)|套用[有限脈衝響應](https://en.wikipedia.org/wiki/Finite_impulse_response)濾波器|
|[series_iir()](series-iirfunction.md)|套用[無限脈衝響應](https://en.wikipedia.org/wiki/Infinite_impulse_response)濾波器|
|[series_fit_line()](series-fit-linefunction.md)|尋找是輸入最佳近似的一條直線|
|[series_fit_line_dynamic()](series-fit-line-dynamicfunction.md)|尋找是輸入最佳近似的一條線，並傳回動態物件|
|[series_fit_2lines()](series-fit-2linesfunction.md)|尋找是輸入最佳近似的兩條線|
|[series_fit_2lines_dynamic()](series-fit-2lines-dynamicfunction.md)|尋找是輸入最佳近似的兩條線，並傳回動態物件|
|[series_outliers()](series-outliersfunction.md)|將數列中的異常點評分|
|[series_periods_detect()](series-periods-detectfunction.md)|尋找存在於時間序列中的最重要週期|
|[series_periods_validate()](series-periods-validatefunction.md)|檢查時間序列是否包含指定長度的定期模式|
|[series_stats_dynamic()](series-stats-dynamicfunction.md)|傳回具有一般統計資料 (最小值/最大值/變異數/標準差/平均值) 的多個資料行|
|[series_stats()](series-statsfunction.md)|產生具有一般統計資料 (最小值/最大值/變異數/標準差/平均值) 的動態值|
  
## <a name="list-of-series-interpolation-functions"></a>數列插補函式清單

|函式|描述|
|--------|-----------|
|[series_fill_backward()](series-fill-backwardfunction.md)|對數列中的遺漏值執行向後填滿插補|
|[series_fill_const()](series-fill-constfunction.md)|以指定的常數值取代數列中的遺漏值|
|[series_fill_forward()](series-fill-forwardfunction.md)|對數列中的遺漏值執行向前填滿插補|
|[series_fill_linear()](series-fill-linearfunction.md)|對數列中的遺漏值執行線性插補|

* 注意：插補函式預設會將 `null` 假定為遺漏值。 因此，如果您想要使用數列的插補函式，請在 `make-series` 中指定 `default=`*double*(`null`)。 

## <a name="example"></a>範例
  
 有一個資料表，其中顯示每個供應商每種水果的數量和平均價格陣列 (依指定範圍內的時間戳記排序)。 對於水果與供應商的每種相異組合，輸出中都會有一個資料列。 輸出資料行會顯示水果、供應商，以及計數、平均和整個時間軸 (從 2016-01-01 到 2016-01-10) 的陣列。 所有陣列都會依各自的時間戳記排序，而所有空白處都會填入預設值 (在此範例中為 0)。 其他所有輸入資料行則會遭到忽略。
  
```kusto
T | make-series PriceAvg=avg(Price) default=0
on Purchase from datetime(2016-09-10) to datetime(2016-09-13) step 1d by Supplier, Fruit
```

:::image type="content" source="images/make-seriesoperator/makeseries.png" alt-text="三個資料表。第一個列出原始資料，第二個只有相異的供應商-水果-日期組合，而第三個則包含 make-series 結果。":::  

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
|[ 4.0, 3.0, 5.0, 0.0, 10.5, 4.0, 3.0, 8.0, 6.5 ]|[ "2017-01-01T00:00:00.0000000Z", "2017-01-02T00:00:00.0000000Z", "2017-01-03T00:00:00.0000000Z", "2017-01-04T00:00:00.0000000Z", "2017-01-05T00:00:00.0000000Z", "2017-01-06T00:00:00.0000000Z", "2017-01-07T00:00:00.0000000Z", "2017-01-08T00:00:00.0000000Z", "2017-01-09T00:00:00.0000000Z" ]|  


如果 `make-series` 的輸入空白，`make-series` 的預設行為也會產生空白結果。

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


在 `make-series` 中使用 `kind=nonempty` 將會產生預設值的非空白結果：

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
|[<br>  1.0,<br>  1.0,<br>  1.0,<br>  1.0,<br>  1.0,<br>  1.0,<br>  1.0,<br>  1.0,<br>  1.0<br>]|[<br>  "2017-01-01T00:00:00.0000000Z",<br>  "2017-01-02T00:00:00.0000000Z",<br>  "2017-01-03T00:00:00.0000000Z",<br>  "2017-01-04T00:00:00.0000000Z",<br>  "2017-01-05T00:00:00.0000000Z",<br>  "2017-01-06T00:00:00.0000000Z",<br>  "2017-01-07T00:00:00.0000000Z",<br>  "2017-01-08T00:00:00.0000000Z",<br>  "2017-01-09T00:00:00.0000000Z"<br>]|
