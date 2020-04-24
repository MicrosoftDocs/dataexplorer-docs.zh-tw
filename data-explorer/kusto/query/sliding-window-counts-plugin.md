---
title: sliding_window_counts外掛程式 - Azure 資料資源管理員 |微軟文件
description: 本文介紹 Azure 數據資源管理器中的sliding_window_counts外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: feab3d0e8f548817be12f202eb2d494bd65aa133
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507492"
---
# <a name="sliding_window_counts-plugin"></a>sliding_window_counts外掛程式

使用[此處](samples.md#performing-aggregations-over-a-sliding-window)描述的技術計算回望期滑動視窗中的值計數和不同計數。

例如,對於*每一天*,*計算上周的用戶*計數和不同的計數。 

```kusto
T | evaluate sliding_window_counts(id, datetime_column, startofday(ago(30d)), startofday(now()), 7d, 1d, dim1, dim2, dim3)
```

**語法**

*T* `| evaluate` T `,` *Start* `,` *End* `,` `sliding_window_counts(` IdColumn*LookbackWindow*時間線列開始結束回視窗箱 = dim1 dim2 ...] *Bin* `,` *dim2* `,` *IdColumn* `,` *TimelineColumn* `,` `,` *dim1*`)`

**引數**

* *T*: 輸入表格表示式。
* *IdColumn*: 具有表示使用者活動的 ID 值的欄的名稱。 
* *時間線列*:表示時間線的列的名稱。
* *開始*:具有分析開始期值的 Scalar。
* *結束*:具有分析結束週期值的 Scalar。
* *回視視窗*: 回望週期的 Scalar 常數值 (例如,對於過去 7D 中的 dcount 使用者:回視視窗 = 7d)
* *:分析*步驟週期的扇號串值。 可以是數字/日期時間/時間`week`/`month`/`year`戳值,也可以是 中的字串,在這種情況下,所有期間都將相應地作為[開始的](startofmonthfunction.md)/[周](startofweekfunction.md)/開始[。](startofyearfunction.md) 
* *dim1* *,dim2*,...:(可選)列出對活動指標計算進行切片的維度列。

**傳回**

返回一個表,該表在回望期間、每個時間線期間(按 bin)和每個現有維度組合中具有 Id 的計數和不同的計數值。

輸出表架構為:

|*時間軸列*|昏暗1|..|dim_n|Count|計數|
|---|---|---|---|---|---|
|類型:截至*時間軸列*|..|..|..|long|long|


**範例**

計算過去一周用戶計數和計數,分析期間的每一天。 

```kusto
let start = datetime(2017 - 08 - 01);
let end = datetime(2017 - 08 - 07); 
let lookbackWindow = 3d;  
let bin = 1d;
let T = datatable(UserId:string, Timestamp:datetime)
[
'Bob',      datetime(2017 - 08 - 01), 
'David',    datetime(2017 - 08 - 01), 
'David',    datetime(2017 - 08 - 01), 
'John',     datetime(2017 - 08 - 01), 
'Bob',      datetime(2017 - 08 - 01), 
'Ananda',   datetime(2017 - 08 - 02),  
'Atul',     datetime(2017 - 08 - 02), 
'John',     datetime(2017 - 08 - 02), 
'Ananda',   datetime(2017 - 08 - 03), 
'Atul',     datetime(2017 - 08 - 03), 
'Atul',     datetime(2017 - 08 - 03), 
'John',     datetime(2017 - 08 - 03), 
'Bob',      datetime(2017 - 08 - 03), 
'Betsy',    datetime(2017 - 08 - 04), 
'Bob',      datetime(2017 - 08 - 05), 
];
T | evaluate sliding_window_counts(UserId, Timestamp, start, end, lookbackWindow, bin)


```

|時間戳記|Count|計數|
|---|---|---|
|2017-08-01 00:00:00.0000000|5|3|
|2017-08-02 00:00:00.0000000|8|5|
|2017-08-03 00:00:00.0000000|13|5|
|2017-08-04 00:00:00.0000000|9|5|
|2017-08-05 00:00:00.0000000|7|5|
|2017-08-06 00:00:00.0000000|2|2|
|2017-08-07 00:00:00.0000000|1|1|