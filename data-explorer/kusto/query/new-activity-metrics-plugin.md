---
title: new_activity_metrics 外掛程式-Azure 資料總管
description: 本文說明 Azure 資料總管中的 new_activity_metrics 外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: b376afda0874fdb70934ffc6861192ef9028e9aa
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347080"
---
# <a name="new_activity_metrics-plugin"></a>new_activity_metrics plugin

為的世代計算有用的活動計量（相異計數值、新值的相異計數、保留率和流失率） `New Users` 。 的每個世代 `New Users` （在時間範圍內第一次看到的所有使用者）都會與所有先前的世代進行比較。 比較會將*所有*先前的時間視窗納入考慮。 例如，在從 = T2 到 = T3 的記錄中，不同的使用者計數將是 T3 中所有不會在 T1 和 T2 中看到的使用者。 
```kusto
T | evaluate new_activity_metrics(id, datetime_column, startofday(ago(30d)), startofday(now()), 1d, dim1, dim2, dim3)
```

## <a name="syntax"></a>語法

*T* `| evaluate` `new_activity_metrics(` *IdColumn* `,` *TimelineColumn* `,` *開始* `,` *結束* `,` *視窗*[世代 `,` * *] [ `,` *dim1* `,` *dim2* `,` ...] [ `,` *回顧*]`)`

## <a name="arguments"></a>引數

* *T*：輸入表格式運算式。
* *IdColumn*：識別碼值代表使用者活動的資料行名稱。 
* *TimelineColumn*：代表時間軸的資料行名稱。
* *Start*：流量分析開始期間的值進行純量。
* *End*：以分析結束期間的值為純量。
* *Window*：以 [分析] 視窗期間的值為純量。 可以是數值/日期時間/時間戳記值，或為其中一個的字串 `week` / `month` / `year` ，在此情況下，所有週期都會據以[startofweek](startofweekfunction.md) / [startofmonth](startofmonthfunction.md) / [startofyear](startofyearfunction.md) 。 
* 世代 *：（* 選擇性）指出特定世代的純量常數。 如果未提供，則會計算並傳回對應至分析時間範圍的所有世代。
* *dim1*， *dim2*，...：（選擇性）分割活動度量計算的維度資料行清單。
* *回顧*：（選擇性）具有一組識別碼的表格式運算式屬於「回頭查看」期間

## <a name="returns"></a>傳回

傳回資料表，其中具有相異計數值、新值的相異計數、每個「從」和「到」時間軸週期的組合，以及每個現有維度組合的流失率。

輸出資料表架構為：

|from_TimelineColumn|to_TimelineColumn|dcount_new_values|dcount_retained_values|dcount_churn_values|retention_rate|churn_rate|dim1|..|dim_n|
|---|---|---|---|---|---|---|---|---|---|
|類型：從*TimelineColumn*|相同|long|long|double|double|double|..|..|..|

* `from_TimelineColumn`-新使用者的世代。 此記錄中的計量會參考在這段期間內第一次看到的所有使用者。 *第一次看到*的決策會將分析期間的所有先前期間列入考慮。 
* `to_TimelineColumn`-要與比較的期間。 
* `dcount_new_values`-在 `to_TimelineColumn` 和包含之前的*所有*期間內，不會看到的相異使用者數目 `from_TimelineColumn` 。 
* `dcount_retained_values`-所有新的使用者，第一次在中看到 `from_TimelineColumn` 出現在中的不同使用者數目 `to_TimelineCoumn` 。
* `dcount_churn_values`-所有新的使用者，第一次在中看到 `from_TimelineColumn` *不*在中的相異使用者數目 `to_TimelineCoumn` 。
* `retention_rate`-世代外的百分比 `dcount_retained_values` （使用者第一次見 `from_TimelineColumn` ）。
* `churn_rate`-世代外的百分比 `dcount_churn_values` （使用者第一次見 `from_TimelineColumn` ）。

**備註**

如需和的定義 `Retention Rate` ， `Churn Rate` 請參閱[activity_metrics 外掛程式](./activity-metrics-plugin.md)檔中的**附注**一節。


## <a name="examples"></a>範例

下列範例資料集會顯示哪些使用者看到哪些日子。 資料表是根據來源資料表產生的 `Users` ，如下所示： 

```kusto
Users | summarize tostring(make_set(user)) by bin(Timestamp, 1d) | order by Timestamp asc;
```

|Timestamp|set_user|
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
    * 針對此記錄考慮的使用者是在11/1 上看到的所有新使用者。 因為這是第一個週期，所以這些是該 bin 中的所有使用者– [0，2，3，4]
    * `dcount_new_values`–11/3 上未出現11/1 的使用者數目。 這包括單一使用者– `5` 。 
    * `dcount_retained_values`–在11/1 的所有新使用者中，有多少保留到11/3？ 有三個（ `[0,2,4]` ），而 `count_churn_values` 是一（使用者 = `3` ）。 
    * `retention_rate`= 0.75 –這三個已保留的使用者不在第一次出現在11/1 的四個新使用者中。 

* 記錄 `R=9` 、 `from_TimelineColumn`  =  `2019-11-02` 、 `to_TimelineColumn`  =  `2019-11-04` ：
    * 這筆記錄著重于在11/2 –使用者和上首次看到的新使用者 `1` `5` 。 
    * `dcount_new_values`–11/4 上未在所有期間內看到的使用者數目 `T0 .. from_Timestamp` 。 意思是，在11/4，但在11/1 或11/2 上看不到的使用者-沒有這類使用者。 
    * `dcount_retained_values`–在11/2 （）的所有新使用者 `[1,5]` 中，有多少保留到11/4？ 有一個這類使用者（ `[1]` ），而 count_churn_values 是一（使用者 `5` ）。 
    * `retention_rate`是0.5 –在11/2 的兩個新的使用者中，保留在11/4 的單一使用者。 


### <a name="weekly-retention-rate-and-churn-rate-single-week"></a>每週保留率和變換率（一周）

下一個查詢會計算世代 `New Users` （第一周抵達的使用者）的保留和流失率。

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


### <a name="weekly-retention-rate-and-churn-rate-complete-matrix"></a>每週保留率和變換率（完整矩陣）

下一個查詢會計算世代每週的保留和流失率 `New Users` 。 如果前一個範例計算一周的統計資料，以下會針對每個 from/to 組合產生 NxN 資料表。

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


### <a name="weekly-retention-rate-with-lookback-period"></a>回顧期間的每週保留率

下列查詢 `New Users` 會在納入考慮期間時計算世代的保留率 `lookback` ：具有一組識別碼的表格式查詢，這些記錄是用來定義世代 `New Users` （所有不會出現在此集合中的識別碼 `New Users` ）。 查詢會在分析期間檢查的保留行為 `New Users` 。

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
