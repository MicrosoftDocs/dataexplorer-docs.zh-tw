---
title: new_activity_metrics外掛程式 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的new_activity_metrics外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: 0aad1c91fec6855030544596a08818b80bbf3d18
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512201"
---
# <a name="new_activity_metrics-plugin"></a>new_activity_metrics plugin

計算`New Users`對 佇列的有用活動指標(不同的計數值、新值的不同計數、保留率和流失率)。 每個佇列`New Users`(在時間視窗中第一次看到的所有使用者)與所有以前的佇列進行比較。 比較會考慮*所有*以前的時間視窗。 例如,在 from_T2和to_T3的記錄中,不同的用戶計數將是 T3 中未在 T1 和 T2 中看不到的所有使用者。 
```kusto
T | evaluate new_activity_metrics(id, datetime_column, startofday(ago(30d)), startofday(now()), 1d, dim1, dim2, dim3)
```

**語法**

*T* `| evaluate` T `,` *End* `,` *Cohort* `,` `new_activity_metrics(` IdColumn*dim1*時間線列結束視窗 [ 佇列 】 = dim1 dim2 ...] `,` `,` *Start* `,` *IdColumn* `,` *Window* *TimelineColumn* `,` *dim2*[`,` *回頭看*]`)`

**引數**

* *T*: 輸入表格表示式。
* *IdColumn*: 具有表示使用者活動的 ID 值的欄的名稱。 
* *時間線列*:表示時間線的列的名稱。
* *開始*:具有分析開始期值的 Scalar。
* *結束*:具有分析結束週期值的 Scalar。
* *視窗*:具有分析視窗期間值的 Scalar。 可以是數字/日期時間/時間`week`/`month`/`year`戳值,也可以是 中的字串,在這種情況下,所有期間都將相應地作為[開始的](startofmonthfunction.md)/[周](startofweekfunction.md)/開始[。](startofyearfunction.md) 
* *佇列*:(可選)一個標量常數,指示特定的佇列。 如果未提供,則計算並返回與分析時間窗口對應的所有佇列。
* *dim1* *,dim2*,...:(可選)列出對活動指標計算進行切片的維度列。
* *回顧*:(可選)一個表格表達式,包含一組屬於「回頭看」期間的 ID

**傳回**

返回具有不同計數值、新值的不同計數、保留率和每個"從"和"到"時間線期間以及每個現有維度組合的改動率的表。

輸出表架構為:

|from_TimelineColumn|to_TimelineColumn|dcount_new_values|dcount_retained_values|dcount_churn_values|retention_rate|churn_rate|昏暗1|..|dim_n|
|---|---|---|---|---|---|---|---|---|---|
|類型:截至*時間軸列*|相同|long|long|double|double|double|..|..|..|

* `from_TimelineColumn`- 新使用者組。 此記錄中的指標是指在此期間首次見到的所有使用者。 *首次看到*的決定考慮到了分析期間的所有前一階段。 
* `to_TimelineColumn`- 被比較的期間。 
* `dcount_new_values`- 在與`to_TimelineColumn`之前*的所有期間未*見到的不同使用者數,`from_TimelineColumn`包括 。 
* `dcount_retained_values`- 在 中首先看到的所有`from_TimelineColumn`新使用者 中`to_TimelineCoumn`,在 中 看到的不同用戶數。
* `dcount_churn_values`- 在 中首次看到的所有`from_TimelineColumn`新使用者 中,未在*中*`to_TimelineCoumn`看到的不同用戶數。
* `retention_rate`- 佇列`dcount_retained_values`中 (使用者首次`from_TimelineColumn`看到中的 百分比)。
* `churn_rate`- 佇列`dcount_churn_values`中 (使用者首次`from_TimelineColumn`看到中的 百分比)。

**注意事項**

有關`Retention Rate``Churn Rate`和 - - 請參閱[activity_metrics外掛程式](./activity-metrics-plugin.md)文件中**的備註**部分。


**範例**

以下範例數據集顯示哪些使用者在哪些日期看到。 該表基於源`Users`表生成,如下所示: 

```kusto
Users | summarize tostring(make_set(user)) by bin(Timestamp, 1d) | order by Timestamp asc;
```

|時間戳記|set_user|
|---|---|
|2019-11-01 00:00:00.0000000|[0,2,3,4]|
|2019-11-02 00:00:00.0000000|[0,1,3,4,5]|
|2019-11-03 00:00:00.0000000|[0,2,4,5]|
|2019-11-04 00:00:00.0000000|[0,1,2,3]|
|2019-11-05 00:00:00.0000000|[0,1,2,3,4]|

原始表外掛程式的輸出如下: 

```kusto
let StartDate = datetime(2019-11-01 00:00:00);
let EndDate = datetime(2019-11-07 00:00:00);
Users 
| evaluate new_activity_metrics(user, Timestamp, StartDate, EndDate-1tick, 1d) 
| where from_Timestamp < datetime(2019-11-03 00:00:00.0000000)
```

|R|from_Timestamp|to_Timestamp|dcount_new_values|dcount_retained_values|dcount_churn_values|retention_rate|churn_rate|
|---|---|---|---|---|---|---|---|
|1|2019-11-01 00:00:00.0000000|2019-11-01 00:00:00.0000000|4|4|0|1|0|
|2|2019-11-01 00:00:00.0000000|2019-11-02 00:00:00.0000000|2|3|1|0.75|0.25|
|3|2019-11-01 00:00:00.0000000|2019-11-03 00:00:00.0000000|1|3|1|0.75|0.25|
|4|2019-11-01 00:00:00.0000000|2019-11-04 00:00:00.0000000|1|3|1|0.75|0.25|
|5|2019-11-01 00:00:00.0000000|2019-11-05 00:00:00.0000000|1|4|0|1|0|
|6|2019-11-01 00:00:00.0000000|2019-11-06 00:00:00.0000000|0|0|4|0|1|
|7|2019-11-02 00:00:00.0000000|2019-11-02 00:00:00.0000000|2|2|0|1|0|
|8|2019-11-02 00:00:00.0000000|2019-11-03 00:00:00.0000000|0|1|1|0.5|0.5|
|9|2019-11-02 00:00:00.0000000|2019-11-04 00:00:00.0000000|0|1|1|0.5|0.5|
|10|2019-11-02 00:00:00.0000000|2019-11-05 00:00:00.0000000|0|1|1|0.5|0.5|
|11|2019-11-02 00:00:00.0000000|2019-11-06 00:00:00.0000000|0|0|2|0|1|

以下是對輸出中的一些記錄的分析: 
* `R=3``from_TimelineColumn`記錄 =  = , `2019-11-03` . `to_TimelineColumn` `2019-11-01`
    * 考慮用於此記錄的使用者都是 11/1 上看到的所有新使用者。 由於這是第一個期間,所有這些使用者都是該 bin 中的所有使用者 [0,2,3,4]
    * `dcount_new_values`• 11/3 上未在 11/1 上看到的用戶數。 這包括單個使用者`5`* 。 
    * `dcount_retained_values`• 在 11/1 的所有新使用者中,有多少人保留到 11/3? 有三個`[0,2,4]` `count_churn_values` ( ),`3`而是一 個 (user= )。 
    * `retention_rate`• 0.75 – 在11月1日首次出現的四個新使用者中,三個保留的使用者。 

* `R=9``from_TimelineColumn`記錄 =  = , `2019-11-04` . `to_TimelineColumn` `2019-11-02`
    * 此記錄側重於首次出現在 11%、2`1``5`使用者和的新使用者。 
    * `dcount_new_values`• 11/4 上未在`T0 .. from_Timestamp`所有期間 都看到的用戶數量。 也就是說,在 11/4 上看到但未在 11/1 或 11/2 上看到的使用者 - 沒有此類使用者。 
    * `dcount_retained_values`• 在 11 FREE`[1,5]`程式 2 () 的所有新使用者中,有多少使用者被保留到 11/4? 有一個這樣的使用者`[1]`( ), 而`5`count_churn_values是一 個 (使用者)。 
    * `retention_rate`是 0.5 = 在 11/2 的兩個新使用者中保留在 11 FREE 用戶端 4 個使用者。 


### <a name="weekly-retention-rate-and-churn-rate-single-week"></a>每週保留率和流失率(單週)

下一個查詢計算`New Users`佇列(第一周到達的使用者)的每周視窗的保留率和流失率。

```kusto
// Generate random data of user activities
let _start = datetime(2017-05-01);
let _end = datetime(2017-05-31);
range Day from _start to _end  step 1d
| extend d = tolong((Day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+200*r-1), 1) 
| mv-expand id=_users to typeof(long) limit 1000000
// Take only the first week cohort (last parameter)
| evaluate new_activity_metrics(['id'], Day, _start, _end, 7d, _start)
| project from_Day, to_Day, retention_rate, churn_rate
```

|from_Day|to_Day|retention_rate|churn_rate|
|---|---|---|---|
|2017-05-01 00:00:00.0000000|2017-05-01 00:00:00.0000000|1|0|
|2017-05-01 00:00:00.0000000|2017-05-08 00:00:00.0000000|0.544632768361582|0.455367231638418|
|2017-05-01 00:00:00.0000000|2017-05-15 00:00:00.0000000|0.031638418079096|0.968361581920904|
|2017-05-01 00:00:00.0000000|2017-05-22 00:00:00.0000000|0|1|
|2017-05-01 00:00:00.0000000|2017-05-29 00:00:00.0000000|0|1|


### <a name="weekly-retention-rate-and-churn-rate-complete-matrix"></a>每週保留率與流失率(完整矩陣)

下一個查詢計算佇列的每周視窗的`New Users`保留率和流失率。 如果前面的範例計算了一周的統計資訊 - 下面將為每個從/到組合生成 NxN 表。

```kusto
// Generate random data of user activities
let _start = datetime(2017-05-01);
let _end = datetime(2017-05-31);
range Day from _start to _end  step 1d
| extend d = tolong((Day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+200*r-1), 1) 
| mv-expand id=_users to typeof(long) limit 1000000
// Last parameter is omitted - 
| evaluate new_activity_metrics(['id'], Day, _start, _end, 7d)
| project from_Day, to_Day, retention_rate, churn_rate
```

|from_Day|to_Day|retention_rate|churn_rate|
|---|---|---|---|
|2017-05-01 00:00:00.0000000|2017-05-01 00:00:00.0000000|1|0|
|2017-05-01 00:00:00.0000000|2017-05-08 00:00:00.0000000|0.190397350993377|0.809602649006622|
|2017-05-01 00:00:00.0000000|2017-05-15 00:00:00.0000000|0|1|
|2017-05-01 00:00:00.0000000|2017-05-22 00:00:00.0000000|0|1|
|2017-05-01 00:00:00.0000000|2017-05-29 00:00:00.0000000|0|1|
|2017-05-08 00:00:00.0000000|2017-05-08 00:00:00.0000000|1|0|
|2017-05-08 00:00:00.0000000|2017-05-15 00:00:00.0000000|0.405263157894737|0.594736842105263|
|2017-05-08 00:00:00.0000000|2017-05-22 00:00:00.0000000|0.227631578947368|0.772368421052632|
|2017-05-08 00:00:00.0000000|2017-05-29 00:00:00.0000000|0|1|
|2017-05-15 00:00:00.0000000|2017-05-15 00:00:00.0000000|1|0|
|2017-05-15 00:00:00.0000000|2017-05-22 00:00:00.0000000|0.785488958990536|0.214511041009464|
|2017-05-15 00:00:00.0000000|2017-05-29 00:00:00.0000000|0.237644584647739|0.762355415352261|
|2017-05-22 00:00:00.0000000|2017-05-22 00:00:00.0000000|1|0|
|2017-05-22 00:00:00.0000000|2017-05-29 00:00:00.0000000|0.621835443037975|0.378164556962025|
|2017-05-29 00:00:00.0000000|2017-05-29 00:00:00.0000000|1|0|


### <a name="weekly-retention-rate-with-lookback-period"></a>帶回望期的每週保留率

以下`New Users`查詢在`lookback`考慮 期間時計算佇列的保留率:一個包含一組 ID 的表格`New Users`查詢,用於定義 佇列(此集中`New Users`未顯示的所有 ID 都是 )。 查詢檢查`New Users`分析 期間的保留行為。

```kusto
// Generate random data of user activities
let _lookback = datetime(2017-02-01);
let _start = datetime(2017-05-01);
let _end = datetime(2017-05-31);
let _data = range Day from _lookback to _end  step 1d
| extend d = tolong((Day - _lookback)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+200*r-1), 1) 
| mv-expand id=_users to typeof(long) limit 1000000;
//
let lookback_data = _data | where Day < _start | project Day, id;
_data
| evaluate new_activity_metrics(id, Day, _start, _end, 7d, _start, lookback_data)
| project from_Day, to_Day, retention_rate
```

|from_Day|to_Day|retention_rate|
|---|---|---|
|2017-05-01 00:00:00.0000000|2017-05-01 00:00:00.0000000|1|
|2017-05-01 00:00:00.0000000|2017-05-08 00:00:00.0000000|0.404081632653061|
|2017-05-01 00:00:00.0000000|2017-05-15 00:00:00.0000000|0.257142857142857|
|2017-05-01 00:00:00.0000000|2017-05-22 00:00:00.0000000|0.296326530612245|
|2017-05-01 00:00:00.0000000|2017-05-29 00:00:00.0000000|0.0587755102040816|
