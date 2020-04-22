---
title: 製造系列運算符 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的製作系列運算符。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/16/2020
ms.openlocfilehash: 3fa8f1693b56fc0820b9e0ba6b5f03a9363c9b98
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663741"
---
# <a name="make-series-operator"></a>make-series 運算子

沿指定軸創建一系列指定的聚合值。 

```kusto
T | make-series sum(amount) default=0, avg(price) default=0 on timestamp from datetime(2016-01-01) to datetime(2016-01-10) step 1d by fruit, supplier
```

**語法**

*T* `| make-series` =*製作欄位參數**=*`=`欄位 [`default` `=` *集合 】*`,` *預設值*= [ ...`on`*軸列*`from`*start*`to`[`step`開始 ]*結束*=*步驟*`,` =`by` *列*`=`•*群組表示式*= []

**引數**

* ** 結果資料行的選擇性名稱。 預設值為衍生自運算式的名稱。
* *預設值:* 將使用的預設值而不是缺少值。 如果沒有具有*AxisColumn*與*GroupExpression*特定值的行,則在結果中,陣列的相應元素將分配預設值 *。* 如果`default =`省略*默認值*,則假定為 0。 
* *彙總:* 對[聚合函數](make-seriesoperator.md#list-of-aggregation-functions)(`count()`如`avg()`或 )的調用,列名稱為參數。 請參閱 [彙總函式清單](make-seriesoperator.md#list-of-aggregation-functions)。 請注意,只有返回數值結果的聚合函數才能與運算符一`make-series`起使用。
* *軸柱:* 將訂購該系列的列。 它可以被視為時間線,但除了`datetime`任何數值類型外,也接受。
* *起始*:(可選) 將為每個系列生成*AxisColumn*的低綁定值。 *開始*、*結束*和*步驟*用於在給定範圍內使用指定的*步驟*生成*AxisColumn*值陣列。 所有*聚合*值分別排序到此陣組。 此*軸列*數位也是輸出中與*AxisColumn*同名的最後一個輸出列。 如果未指定*起始*值,則起始項是每個系列中具有數據的第一個條柱(步驟)。
* *結束*:(可選 *)AxisColumn*的高綁定(非包含)值,時間序列的最後一個索引小於此值(並且將*開始*加上小於*結束**的步驟的*整數倍數)。 如果未提供*結束*值,它將是最後一個條柱(步驟)的上限,每個條柱都有每個系列的數據。
* *步驟*:*軸列*陣列的兩個連續元素之間的差異(即條柱大小)。
* ** 可提供一組相異值的資料行運算式。 通常是已提供有限的一組值的資料行名稱。 
* *製作列參數*:零個或多個(空間分隔)參數,其形式為*控制行為的名稱*`=`*值*。 支援下列參數： 
  
  |名稱           |值                                        |描述                                                                                        |
  |---------------|-------------------------------------|------------------------------------------------------------------------------|
  |`kind`          |`nonempty`                               |當製造的系列運算子輸入為空時產生預設結果|                                

**傳回**

輸入列被排列成具有相同`by`表示式值的組`bin_at(`和*AxisColumn*`, `*步驟*`, `*開始*`)`運算式。 然後指定的彙總函式會針對每個群組進行計算，以便為每個群組產生資料列。 結果包含`by`列 *、AxisColumn*列以及每個計算聚合的至少一列。 (不支援多列或非數字結果的聚合。

此中間結果具有與*AxisColumn*`, `*步驟*`, `*起始*`)`值的不同`by``bin_at(`組合一樣多的 行。

最後,中間結果中的行排列成具有相同`by`表達式值的組中,所有聚合值都排列成陣列`dynamic`( 類型值)。 對於每個聚合,有一個列包含具有相同名稱的陣列。 具有所有*AxisColumn*值的範圍函數輸出中的最後一列。 它的值對所有行重複。 

請注意,由於預設值的填充缺少條柱,生成的數據透視表具有所有系列的相同數量的條柱(即聚合值)  

**注意**

儘管可以為聚合表達式和分組表達式提供任意表達式,但使用簡單列名稱會更有效。

**備用語法**

*T* `| make-series` =*欄*`=``default``=`位 =*集合模式*`,`(*預設值*】 [ ...`on`*軸*`in`柱`,``,``range(`開始停止步驟 [ 列 ]*組式*= ...]*step*`=``,`*stop*`by`*start*`)`*Column*

來自備用語法產生的序列在兩個方面與主要語法不同:
* *停止*值是包含的。
* 將索引軸與 bin() 生成,而不是bin_at()生成,這意味著不保證*啟動*不保證包含在生成的序列中。

建議使用 make 系列的主要語法,而不是備用語法。

**分發並播放**

`make-series`支援`summarize`使用語法提示的[隨機鍵提示](shufflequery.md)。

## <a name="list-of-aggregation-functions"></a>彙總函式清單

|函式|描述|
|--------|-----------|
|[any()](any-aggfunction.md)|傳回群組的隨機非空值|
|[avg()](avg-aggfunction.md)|重通整個組的平均值|
|[count()](count-aggfunction.md)|傳回群組的計數|
|[countif()](countif-aggfunction.md)|返回具有組謂詞的計數|
|[dcount()](dcount-aggfunction.md)|傳回元件元素的近似不同計數|
|[max()](max-aggfunction.md)|傳回整個群組的最大值|
|[min()](min-aggfunction.md)|傳回整個群組的最小值|
|[stdev()](stdev-aggfunction.md)|傳回整個組的標準偏差|
|[sum()](sum-aggfunction.md)|傳回有群組的元素的總和|
|[variance()](variance-aggfunction.md)|傳回整個群組的差異|

## <a name="list-of-series-analysis-functions"></a>列分析函式清單

|函式|描述|
|--------|-----------|
|[series_fir()](series-firfunction.md)|套[用有限脈衝回應](https://en.wikipedia.org/wiki/Finite_impulse_response)濾波器|
|[series_iir()](series-iirfunction.md)|套[用無限脈衝回應](https://en.wikipedia.org/wiki/Infinite_impulse_response)濾波器|
|[series_fit_line()](series-fit-linefunction.md)|尋找輸入的最佳近似值的直線|
|[series_fit_line_dynamic()](series-fit-line-dynamicfunction.md)|尋找輸入的最佳近似值的線,傳回動態物件|
|[series_fit_2lines()](series-fit-2linesfunction.md)|尋找兩條是輸入最佳近似線的行|
|[series_fit_2lines_dynamic()](series-fit-2lines-dynamicfunction.md)|查找兩條是輸入最佳近似值的行,傳回動態物件|
|[series_outliers()](series-outliersfunction.md)|在序列中得分異常點|
|[series_periods_detect()](series-periods-detectfunction.md)|尋找時間序列中存在的最重要期間|
|[series_periods_validate()](series-periods-validatefunction.md)|檢查時間序列是否包含指定長度的週期性模式|
|[series_stats_dynamic()](series-stats-dynamicfunction.md)|傳回有常見統計資訊的多個欄(最小/最大/方差/stdev/平均值)|
|[series_stats()](series-statsfunction.md)|使用通用統計資訊產生動態值(最小/最大/方差/stdev/平均)|
  
## <a name="list-of-series-interpolation-functions"></a>序列插數清單
|函式|描述|
|--------|-----------|
|[series_fill_backward()](series-fill-backwardfunction.md)|對一個中缺失值執行到後填充插值|
|[series_fill_const()](series-fill-constfunction.md)|將序列中缺少的值取代為指定的常量值|
|[series_fill_forward()](series-fill-forwardfunction.md)|執行序列中缺失值的正向填充插值|
|[series_fill_linear()](series-fill-linearfunction.md)|對列中缺失值執行線性插值|

* 注意:默認情況下,插值函數假定`null`為缺失值。 因此,如果要對序列使用`default=`插`null`值函數`make-series`, 建議在中指定*雙*( ) 。 

## <a name="example"></a>範例
  
 顯示每個供應商按指定範圍訂購的時間戳訂購的每個供應商的水果的數位和平均價格的陣列的表。 水果和供應商的每個不同組合的產出都有一行。 輸出列顯示水果、供應商和陣列:計數、平均值和整個時間線(從 2016-01-01 到 2016-01-10)。 所有陣列都按相應的時間戳排序,所有間隙都用預設值填充(本示例中為 0)。 其他所有輸入資料行則會遭到忽略。
  
```kusto
T | make-series PriceAvg=avg(Price) default=0
on Purchase from datetime(2016-09-10) to datetime(2016-09-13) step 1d by Supplier, Fruit
```

:::image type="content" source="images/aggregations/makeseries.png" alt-text="製作家族":::  
  
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
|[ 4.0, 3.0, 5.0, 0.0, 10.5, 4.0, 3.0, 8.0, 6.5 ]|[ "2017-01-01T00:00:00.0000000Z" " 2017-01-02T00:00:00.0000000Z", "2017-01-03T00:00:00.000000Z","2017-01-04T00:00.00000000Z","2017-01-05T00:00:00.00000000Z", "2017-01-06T00:00:00.000000Z" " 2017-01-07T00:00:00.0000000Z" 2017-01-08T00:00:00.000000Z, "2017-01-09T00:00.00000000Z" ||  


當輸入`make-series`為空時,`make-series`的 默認行為也會產生空結果。

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

|Count|
|---|
|0|


使用`kind=nonempty``make-series`預設值的非空結果:

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
|[<br>  1.0,<br>  1.0,<br>  1.0,<br>  1.0,<br>  1.0,<br>  1.0,<br>  1.0,<br>  1.0,<br>  1.0<br>]|[<br>  "2017-01-01T00:00.0000000Z"<br>  "2017-01-02T00:00.0000000Z"<br>  "2017-01-03T00:00.0000000Z"<br>  "2017-01-04T00:00.0000000Z"<br>  "2017-01-05T00:00.0000000Z"<br>  "2017-01-06T00:00.0000000Z"<br>  "2017-01-07T00:00.0000000Z"<br>  "2017-01-08T00:00.0000000Z"<br>  "2017-01-09T00:00.0000000Z"<br>]|