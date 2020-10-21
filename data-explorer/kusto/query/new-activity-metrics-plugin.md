---
title: new_activity_metrics 外掛程式-Azure 資料總管
description: 本文說明 Azure 資料總管中 new_activity_metrics 外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: 447191158ce3de69c4429b5af4a33d24150af77c
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248845"
---
# <a name="new_activity_metrics-plugin"></a>new_activity_metrics plugin

計算的 (相異計數值、新值的相異計數、保留率及流失率) 的可用活動度量 `New Users` 。 每個世代 `New Users` (在時間) 範圍內第1次看到的所有使用者，都會與所有先前的世代比較。 比較會將 *所有* 先前的時間視窗納入考慮。 例如，在 a = T2 和 to = T3 的記錄中，使用者的相異計數將會是 T3 中未出現在 T1 和 T2 中的所有使用者。 
```kusto
T | evaluate new_activity_metrics(id, datetime_column, startofday(ago(30d)), startofday(now()), 1d, dim1, dim2, dim3)
```

## <a name="syntax"></a>語法

*T* `| evaluate` `new_activity_metrics(` *IdColumn* `,` *TimelineColumn* `,` *Start* `,` *End* `,` *Window* [世代 `,` * *] [ `,` *dim1* `,` *dim2* `,` ...] [ `,` *回顧*]`)`

## <a name="arguments"></a>引數

* *T*：輸入表格式運算式。
* *IdColumn*：具有代表使用者活動之識別碼值的資料行名稱。 
* *TimelineColumn*：代表時間軸的資料行名稱。
* *啟動*：具有分析開始期間值的純量值。
* *End*：具有分析結束期間值的純量值。
* *視窗*：具有分析視窗期間值的純量值。 可以是數值/日期時間/時間戳記值，也可以是的字串 `week` / `month` / `year` ，在此情況下，所有期間都會據以[startofweek](startofweekfunction.md) / [startofmonth](startofmonthfunction.md) / [startofyear](startofyearfunction.md) 。 
* 世代 *： (* 選擇性) 指出特定世代的純量常數。 如果未提供，則會計算並傳回對應至分析時間範圍的所有世代。
* *dim1*， *dim2*，...： () 選用的維度資料行清單來分割活動計量計算。
* *回顧*： (選擇性) 表格式運算式，其中包含一組屬於「回頭查看」期間的識別碼。

## <a name="returns"></a>傳回

傳回資料表，該資料表具有相異計數值、新值的相異計數、保留率，以及每個現有維度組合的每個組合的流失率。

輸出資料表架構為：

|from_TimelineColumn|to_TimelineColumn|dcount_new_values|dcount_retained_values|dcount_churn_values|retention_rate|churn_rate|dim1|..|dim_n|
|---|---|---|---|---|---|---|---|---|---|
|類型： *TimelineColumn*的|相同|long|long|double|double|double|..|..|..|

* `from_TimelineColumn` -新使用者的世代。 此記錄中的計量指的是在這段期間內第一次看到的所有使用者。 *第一次看到*的決策會考慮分析期間內所有先前的期間。 
* `to_TimelineColumn` -要比較的期間。 
* `dcount_new_values` -在 `to_TimelineColumn` 和包含的 *所有* 期間都未看到的相異使用者數目 `from_TimelineColumn` 。 
* `dcount_retained_values` -在所有新使用者中，第一次看到，在中看到的 `from_TimelineColumn` 相異使用者數目 `to_TimelineCoumn` 。
* `dcount_churn_values` -在所有新的使用者中，第一次出現在中，是在中看 `from_TimelineColumn` *不* 到的相異使用者數目 `to_TimelineCoumn` 。
* `retention_rate` - `dcount_retained_values` (使用者第一次在) 中看到的世代百分比 `from_TimelineColumn` 。
* `churn_rate` - `dcount_churn_values` (使用者第一次在) 中看到的世代百分比 `from_TimelineColumn` 。

**備註**

如需和的定義， `Retention Rate` `Churn Rate` 請參閱[activity_metrics 外掛程式](./activity-metrics-plugin.md)檔中的**附注**一節。


## <a name="examples"></a>範例

下列範例資料集會顯示哪些使用者看到哪些天。 資料表是根據來源資料表所產生，如下所示 `Users` ： 

```kusto
Users | summarize tostring(make_set(user)) by bin(Timestamp, 1d) | order by Timestamp asc;
```

|時間戳記|set_user|
|---|---|
|2019-11-01 00：00：00.0000000|[0，2，3，4]|
|2019-11-02 00：00：00.0000000|[0，1，3，4，5]|
|2019-11-03 00：00：00.0000000|[0，2，4，5]|
|2019-11-04 00：00：00.0000000|[0，1，2，3]|
|2019-11-05 00：00：00.0000000|[0，1，2，3，4]|

原始資料表的外掛程式輸出如下所示： 

```kusto
let StartDate = datetime(2019-11-01 00:00:00);
let EndDate = datetime(2019-11-07 00:00:00);
Users 
| evaluate new_activity_metrics(user, Timestamp, StartDate, EndDate-1tick, 1d) 
| where from_Timestamp < datetime(2019-11-03 00:00:00.0000000)
```

|R|from_Timestamp|to_Timestamp|dcount_new_values|dcount_retained_values|dcount_churn_values|retention_rate|churn_rate|
|---|---|---|---|---|---|---|---|
|1|2019-11-01 00：00：00.0000000|2019-11-01 00：00：00.0000000|4|4|0|1|0|
|2|2019-11-01 00：00：00.0000000|2019-11-02 00：00：00.0000000|2|3|1|0.75|0.25|
|3|2019-11-01 00：00：00.0000000|2019-11-03 00：00：00.0000000|1|3|1|0.75|0.25|
|4|2019-11-01 00：00：00.0000000|2019-11-04 00：00：00.0000000|1|3|1|0.75|0.25|
|5|2019-11-01 00：00：00.0000000|2019-11-05 00：00：00.0000000|1|4|0|1|0|
|6|2019-11-01 00：00：00.0000000|2019-11-06 00：00：00.0000000|0|0|4|0|1|
|7|2019-11-02 00：00：00.0000000|2019-11-02 00：00：00.0000000|2|2|0|1|0|
|8|2019-11-02 00：00：00.0000000|2019-11-03 00：00：00.0000000|0|1|1|0.5|0.5|
|9|2019-11-02 00：00：00.0000000|2019-11-04 00：00：00.0000000|0|1|1|0.5|0.5|
|10|2019-11-02 00：00：00.0000000|2019-11-05 00：00：00.0000000|0|1|1|0.5|0.5|
|11|2019-11-02 00：00：00.0000000|2019-11-06 00：00：00.0000000|0|0|2|0|1|

以下是來自輸出的一些記錄分析： 
* 記錄 `R=3` 、 `from_TimelineColumn`  =  `2019-11-01` 、 `to_TimelineColumn`  =  `2019-11-03` ：
    * 針對此記錄所考慮的使用者，都是11/1 上顯示的新使用者。 因為這是第一個期間，所以這些都是該 bin 中的所有使用者– [0，2，3，4]
    * `dcount_new_values` –11/3 上未看到11/1 的使用者數目。 這包括單一使用者– `5` 。 
    * `dcount_retained_values` –在11/1 的所有新使用者中，有多少已保留到11/3？ 有三種 (`[0,2,4]`) ，而 `count_churn_values` (使用者 = `3`) 。 
    * `retention_rate` = 0.75 –第三個使用者的三個使用者，第一次在11/1 中看到。 

* 記錄 `R=9` 、 `from_TimelineColumn`  =  `2019-11-02` 、 `to_TimelineColumn`  =  `2019-11-04` ：
    * 此記錄著重于在 11/2-使用者和上第一次看到的新使用者 `1` `5` 。 
    * `dcount_new_values` –11/4 上未看到所有期間的使用者數目 `T0 .. from_Timestamp` 。 也就是說，在11/4 但未看到11/1 或11/2 的使用者，就沒有這類使用者。 
    * `dcount_retained_values` – 11/2 () 上的所有新使用者 `[1,5]` ，在11/4 之前的保留有多少？ 其中有一個這類使用者 (`[1]`) ，而 count_churn_values 是一 (使用者 `5`) 。 
    * `retention_rate` 是0.5 –在11/4 上從11/2 的兩個新的使用者中保留的單一使用者。 


### <a name="weekly-retention-rate-and-churn-rate-single-week"></a>每週保留率和變換率 (一周) 

下一個查詢會計算每週的保留和流失率，以供 `New Users` 第一周) 抵達的 (使用者使用。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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
|2017-05-01 00：00：00.0000000|2017-05-01 00：00：00.0000000|1|0|
|2017-05-01 00：00：00.0000000|2017-05-08 00：00：00.0000000|0.544632768361582|0.455367231638418|
|2017-05-01 00：00：00.0000000|2017-05-15 00：00：00.0000000|0.031638418079096|0.968361581920904|
|2017-05-01 00：00：00.0000000|2017-05-22 00：00：00.0000000|0|1|
|2017-05-01 00：00：00.0000000|2017-05-29 00：00：00.0000000|0|1|


### <a name="weekly-retention-rate-and-churn-rate-complete-matrix"></a>每週保留率和變換率 (完整的矩陣) 

下一個查詢會針對世代的每週時段計算保留和流失率 `New Users` 。 如果先前的範例計算一周的統計資料，則下列各項會為每個 from/to 組合產生 NxN 資料表。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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
|2017-05-01 00：00：00.0000000|2017-05-01 00：00：00.0000000|1|0|
|2017-05-01 00：00：00.0000000|2017-05-08 00：00：00.0000000|0.190397350993377|0.809602649006622|
|2017-05-01 00：00：00.0000000|2017-05-15 00：00：00.0000000|0|1|
|2017-05-01 00：00：00.0000000|2017-05-22 00：00：00.0000000|0|1|
|2017-05-01 00：00：00.0000000|2017-05-29 00：00：00.0000000|0|1|
|2017-05-08 00：00：00.0000000|2017-05-08 00：00：00.0000000|1|0|
|2017-05-08 00：00：00.0000000|2017-05-15 00：00：00.0000000|0.405263157894737|0.594736842105263|
|2017-05-08 00：00：00.0000000|2017-05-22 00：00：00.0000000|0.227631578947368|0.772368421052632|
|2017-05-08 00：00：00.0000000|2017-05-29 00：00：00.0000000|0|1|
|2017-05-15 00：00：00.0000000|2017-05-15 00：00：00.0000000|1|0|
|2017-05-15 00：00：00.0000000|2017-05-22 00：00：00.0000000|0.785488958990536|0.214511041009464|
|2017-05-15 00：00：00.0000000|2017-05-29 00：00：00.0000000|0.237644584647739|0.762355415352261|
|2017-05-22 00：00：00.0000000|2017-05-22 00：00：00.0000000|1|0|
|2017-05-22 00：00：00.0000000|2017-05-29 00：00：00.0000000|0.621835443037975|0.378164556962025|
|2017-05-29 00：00：00.0000000|2017-05-29 00：00：00.0000000|1|0|


### <a name="weekly-retention-rate-with-lookback-period"></a>回顧期間的每週保留費率

下列查詢會在考慮期間計算世代的保留率 `New Users` ：採用 `lookback` 一組識別碼的表格式查詢，這些識別碼是用來定義世代 `New Users` (不會出現在此集合中的所有識別碼都會 `New Users`) 。 查詢會在分析期間檢查的保留行為 `New Users` 。

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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
|2017-05-01 00：00：00.0000000|2017-05-01 00：00：00.0000000|1|
|2017-05-01 00：00：00.0000000|2017-05-08 00：00：00.0000000|0.404081632653061|
|2017-05-01 00：00：00.0000000|2017-05-15 00：00：00.0000000|0.257142857142857|
|2017-05-01 00：00：00.0000000|2017-05-22 00：00：00.0000000|0.296326530612245|
|2017-05-01 00：00：00.0000000|2017-05-29 00：00：00.0000000|0.0587755102040816|
