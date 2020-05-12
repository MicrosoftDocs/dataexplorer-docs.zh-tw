---
title: activity_counts_metrics 外掛程式-Azure 資料總管
description: 本文說明 Azure 資料總管中的 activity_counts_metrics 外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 167ba8818709f52ccc344452e275405c42b1796e
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227667"
---
# <a name="activity_counts_metrics-plugin"></a>activity_counts_metrics plugin

計算每個時間範圍中，比較/匯總至*所有*先前時間視窗的有用活動計量。 計量包括：總計數值、相異計數值、新值的相異計數，以及匯總的相異計數。 比較此外掛程式與[activity_metrics 外掛程式](activity-metrics-plugin.md)，其中每個時間範圍只會與先前的時間範圍進行比較。

```kusto
T | evaluate activity_counts_metrics(id, datetime_column, startofday(ago(30d)), startofday(now()), 1d, dim1, dim2, dim3)
```

**語法**

*T* `| evaluate` `activity_counts_metrics(` *IdColumn* `,` *TimelineColumn* `,` *開始* `,` *結束* `,` *視窗*[世代 `,` * *] [ `,` *dim1* `,` *dim2* `,` ...] [ `,` *回顧*]`)`

**引數**

* *T*：輸入表格式運算式。
* *IdColumn*：識別碼值代表使用者活動的資料行名稱。 
* *TimelineColumn*：代表時間軸的資料行名稱。
* *Start*：流量分析開始期間的值進行純量。
* *End*：以分析結束期間的值為純量。
* *Window*：以 [分析] 視窗期間的值為純量。 可以是數值/日期時間/時間戳記值，或為其中一個的字串 `week` / `month` / `year` ，在此情況下，所有週期都會[startofweek](startofweekfunction.md) / [startofmonth](startofmonthfunction.md)或[startofyear](startofyearfunction.md)。 
* *dim1*， *dim2*，...：（選擇性）分割活動度量計算的維度資料行清單。

**傳回**

傳回資料表，其中包含：總計計數值、相異計數值、新值的相異計數，以及每個時間範圍的匯總相異計數。

輸出資料表架構為：

|`TimelineColumn`|`dim1`|...|`dim_n`|`count`|`dcount`|`new_dcount`|`aggregated_dcount`
|---|---|---|---|---|---|---|---|---|
|類型：從*`TimelineColumn`*|..|..|..|long|long|long|long|long


* *`TimelineColumn`*：時間範圍開始時間。
* *`count`*：時間範圍和*維度*中的記錄計數總計
* *`dcount`*：相異識別碼值在時間範圍和*dim*中計數
* *`new_dcount`*：時間範圍和*維度*中的相異識別碼值，相較于先前的所有時間視窗。 
* *`aggregated_dcount`*：從第一個時間範圍到目前（含）的每個*維度*匯總相異識別碼值總計。

**範例**

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


