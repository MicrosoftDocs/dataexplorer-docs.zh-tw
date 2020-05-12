---
title: sliding_window_counts 外掛程式-Azure 資料總管
description: 本文說明 Azure 資料總管中的 sliding_window_counts 外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2fbc870eafc45c8c63bea98a64f492d161af4c9b
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83226341"
---
# <a name="sliding_window_counts-plugin"></a>sliding_window_counts 外掛程式

使用[此處](samples.md#performing-aggregations-over-a-sliding-window)所述的技術，在回顧期間，計算滑動視窗中值的計數和相異計數。

例如，針對每一*天*，計算過去*一周*的計數和相異使用者計數。 

```kusto
T | evaluate sliding_window_counts(id, datetime_column, startofday(ago(30d)), startofday(now()), 7d, 1d, dim1, dim2, dim3)
```

**語法**

*T* `| evaluate` `sliding_window_counts(` *IdColumn* `,` *TimelineColumn* `,` *Start* `,` *End* `,` *LookbackWindow* `,` *Bin* `,` [*dim1* `,` *dim2* `,` ...]`)`

**引數**

* *T*：輸入表格式運算式。
* *IdColumn*：識別碼值代表使用者活動的資料行名稱。 
* *TimelineColumn*：代表時間軸的資料行名稱。
* *Start*：流量分析開始期間的值進行純量。
* *End*：以分析結束期間的值為純量。
* *LookbackWindow*：回顧期間的純量常數值（例如，針對 `dcount` 過去7d： LookbackWindow = 7d 中的使用者）
* *Bin*：分析步驟期間的純量常數值。 這個值可以是數值/日期時間/時間戳記值。 如果值是具有格式的字串，則 `week` / `month` / `year` 所有期間都會是[startofweek](startofweekfunction.md) / [startofmonth](startofmonthfunction.md) / [startofyear](startofyearfunction.md)。 
* *dim1*， *dim2*，...：（選擇性）分割活動度量計算的維度資料行清單。

**傳回**

針對每個時間軸（依 bin）和每個現有的維度組合，傳回在回顧期間內具有計數和相異計數值的資料表。

輸出資料表架構為：

|*TimelineColumn*|`dim1`|..|`dim_n`|`count`|`dcount`|
|---|---|---|---|---|---|
|類型：從*TimelineColumn*|..|..|..|long|long|


**範例**

計算 `dcounts` 過去一周內的使用者計數和時間（針對分析期間內的每一天）。 

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

|時間戳記|計數|`dcount`|
|---|---|---|
|2017-08-01 00：00：00.0000000|5|3|
|2017-08-02 00：00：00.0000000|8|5|
|2017-08-03 00：00：00.0000000|13|5|
|2017-08-04 00：00：00.0000000|9|5|
|2017-08-05 00：00：00.0000000|7|5|
|2017-08-06 00：00：00.0000000|2|2|
|2017-08-07 00：00：00.0000000|1|1|