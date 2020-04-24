---
title: activity_counts_metrics外掛程式 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的activity_counts_metrics外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e55cd940850440499d227082f57769e499e6a551
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519307"
---
# <a name="activity_counts_metrics-plugin"></a>activity_counts_metrics plugin

計算每個時間視窗與*所有*時間視窗進行比較/聚合的有用活動指標(總計數值、不同計數值、新值的不同計數、聚合的不同計數)(與每次視窗與其上一個時間視窗進行比較[activity_metrics外掛程式](activity-metrics-plugin.md)不同)。

```kusto
T | evaluate activity_counts_metrics(id, datetime_column, startofday(ago(30d)), startofday(now()), 1d, dim1, dim2, dim3)
```

**語法**

*T* `| evaluate` T `,` *End* `,` *Cohort* `,` `activity_counts_metrics(` IdColumn*dim1*時間線列結束視窗 [ 佇列 】 = dim1 dim2 ...] `,` `,` *Start* `,` *IdColumn* `,` *Window* *TimelineColumn* `,` *dim2*[`,` *回頭看*]`)`

**引數**

* *T*: 輸入表格表示式。
* *IdColumn*: 具有表示使用者活動的 ID 值的欄的名稱。 
* *時間線列*:表示時間線的列的名稱。
* *開始*:具有分析開始期值的 Scalar。
* *結束*:具有分析結束週期值的 Scalar。
* *視窗*:具有分析視窗期間值的 Scalar。 可以是數字/日期時間/時間`week`/`month`/`year`戳值,也可以是 中的字串,在這種情況下,所有期間都將相應地作為[開始的](startofmonthfunction.md)/[周](startofweekfunction.md)/開始[。](startofyearfunction.md) 
* *dim1* *,dim2*,...:(可選)列出對活動指標計算進行切片的維度列。

**傳回**

返回具有總計數值、不同計數值、新值的不同計數、每個時間視窗的聚合不同計數的表。

輸出表架構為:

|時間軸列|昏暗1|...|dim_n|count|dcount|new_dcount|aggregated_dcount
|---|---|---|---|---|---|---|---|---|
|類型:截至*時間軸列*|..|..|..|long|long|long|long|long


* *時間軸列*:時間視窗開始時間。
* *計數*: 總紀錄計數在時間視窗與*暗數*
* *dcount*: 不同的代碼值在時間視窗中計數,*並且暗點*
* *new_dcount*: 與以前所有時間視窗相比,時間視窗和*暗點*中的不同 ID 值。 
* *aggregated_dcount*: 從第一個時間視窗到當前(包括)的總匯總不同 ID 值*dim(s)。*

**範例**

### <a name="daily-activity-counts"></a>每日活動計數 

下一個查詢計算提供的輸入表的每日活動計數

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

|時間戳記|count|dcount|new_dcount|aggregated_dcount|
|---|---|---|---|---|
|2017-08-01 00:00:00.0000000|4|4|4|4|
|2017-08-02 00:00:00.0000000|3|3|2|6|
|2017-08-03 00:00:00.0000000|6|5|2|8|
|2017-08-04 00:00:00.0000000|1|1|0|8|


