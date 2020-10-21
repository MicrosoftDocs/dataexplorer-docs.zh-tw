---
title: activity_counts_metrics 外掛程式-Azure 資料總管
description: 本文說明 Azure 資料總管中 activity_counts_metrics 外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f44af0b7c1623bfd8393ddeefee57f7f8ca27d2b
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241728"
---
# <a name="activity_counts_metrics-plugin"></a>activity_counts_metrics plugin

針對每個時間範圍，計算與 *所有* 先前時間範圍比較/匯總的實用活動度量。 計量包括：總計數值、相異計數值、新值的相異計數，以及匯總的相異計數。 比較這個外掛程式與 [activity_metrics 外掛程式](activity-metrics-plugin.md)，其中每個時間範圍都只會與先前的時間範圍比較。

```kusto
T | evaluate activity_counts_metrics(id, datetime_column, startofday(ago(30d)), startofday(now()), 1d, dim1, dim2, dim3)
```

## <a name="syntax"></a>語法

*T* `| evaluate` `activity_counts_metrics(` *IdColumn* `,` *TimelineColumn* `,` *Start* `,` *End* `,` *Window* [世代 `,` * *] [ `,` *dim1* `,` *dim2* `,` ...] [ `,` *回顧*]`)`

## <a name="arguments"></a>引數

* *T*：輸入表格式運算式。
* *IdColumn*：具有代表使用者活動之識別碼值的資料行名稱。 
* *TimelineColumn*：代表時間軸的資料行名稱。
* *啟動*：具有分析開始期間值的純量值。
* *End*：具有分析結束期間值的純量值。
* *視窗*：具有分析視窗期間值的純量值。 可以是數值/日期時間/時間戳記值，也可以是的字串， `week` / `month` / `year` 在此情況下，所有期間都會是[startofweek](startofweekfunction.md) / [startofmonth](startofmonthfunction.md)或[startofyear](startofyearfunction.md)。 
* *dim1*， *dim2*，...： () 選用的維度資料行清單來分割活動計量計算。

## <a name="returns"></a>傳回

傳回資料表，其具有：總計數值、相異計數值、新值的相異計數，以及每個時間範圍的匯總相異計數。

輸出資料表架構為：

|`TimelineColumn`|`dim1`|...|`dim_n`|`count`|`dcount`|`new_dcount`|`aggregated_dcount`
|---|---|---|---|---|---|---|---|---|
|類型： *`TimelineColumn`*|..|..|..|long|long|long|long|long


* *`TimelineColumn`*：時間範圍開始時間。
* *`count`*：時間範圍內的記錄計數總計和 *dim (s) *
* *`dcount`*：時間範圍內的相異識別碼值和*dim (s*的計數) 
* *`new_dcount`*：時間範圍內的相異識別碼值和 *dim (s) * 相較于所有先前的時間範圍。 
* *`aggregated_dcount`*： *維度 (s * 的匯總相異識別碼值總計) 從第一次時間範圍到目前 (（含）) 。

## <a name="examples"></a>範例

### <a name="daily-activity-counts"></a>每日活動計數 

下一個查詢會計算所提供輸入資料表的每日活動計數

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let start=datetime(2017-08-01);
let end=datetime(2017-08-04);
let window=1d;
let T = datatable(UserId:string, Timestamp:datetime)
[
'A', datetime(2017-08-01),
'D', datetime(2017-08-01), 
'J', datetime(2017-08-01),
'B', datetime(2017-08-01),
'C', datetime(2017-08-02),  
'T', datetime(2017-08-02),
'J', datetime(2017-08-02),
'H', datetime(2017-08-03),
'T', datetime(2017-08-03),
'T', datetime(2017-08-03),
'J', datetime(2017-08-03),
'B', datetime(2017-08-03),
'S', datetime(2017-08-03),
'S', datetime(2017-08-04),
];
 T 
 | evaluate activity_counts_metrics(UserId, Timestamp, start, end, window)
```

|`Timestamp`|`count`|`dcount`|`new_dcount`|`aggregated_dcount`|
|---|---|---|---|---|
|2017-08-01 00：00：00.0000000|4|4|4|4|
|2017-08-02 00：00：00.0000000|3|3|2|6|
|2017-08-03 00：00：00.0000000|6|5|2|8|
|2017-08-04 00：00：00.0000000|1|1|0|8|


