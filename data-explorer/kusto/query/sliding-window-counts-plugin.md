---
title: sliding_window_counts 外掛程式-Azure 資料總管
description: 本文說明 Azure 資料總管中 sliding_window_counts 外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9234745a002b88acf23b2e177aa8bc11cda45857
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241804"
---
# <a name="sliding_window_counts-plugin"></a>sliding_window_counts 外掛程式

使用 [此處](samples.md#perform-aggregations-over-a-sliding-window)所述的技術，在回顧期間于滑動視窗中計算值的計數和相異計數。

比方說，*每天*都要計算計數和相異的使用者*計數。* 

```kusto
T | evaluate sliding_window_counts(id, datetime_column, startofday(ago(30d)), startofday(now()), 7d, 1d, dim1, dim2, dim3)
```

## <a name="syntax"></a>語法

*T* `| evaluate` `sliding_window_counts(` *IdColumn* `,` *TimelineColumn* `,` *Start* `,` *End* `,` *LookbackWindow* `,` *Bin* `,` [*dim1* `,` *dim2* `,` ...]`)`

## <a name="arguments"></a>引數

* *T*：輸入表格式運算式。
* *IdColumn*：具有代表使用者活動之識別碼值的資料行名稱。 
* *TimelineColumn*：代表時間軸的資料行名稱。
* *啟動*：具有分析開始期間值的純量值。
* *End*：具有分析結束期間值的純量值。
* *LookbackWindow*：回顧期間的純量常數值 (例如，針對 `dcount` 過去7d： LookbackWindow = 7d 中的使用者) 
* *Bin*：分析步驟期間的純量常數值。 這個值可以是數值/日期時間/時間戳記值。 如果值是具有格式的字串，則 `week` / `month` / `year` 所有句點都是[startofweek](startofweekfunction.md) / [startofmonth](startofmonthfunction.md) / [startofyear](startofyearfunction.md)。 
* *dim1*， *dim2*，...： () 選用的維度資料行清單來分割活動計量計算。

## <a name="returns"></a>傳回

傳回資料表，該資料表具有回顧期限內的識別碼和相異計數值，每個) 的時間軸期間和每個現有的維度組合 (。

輸出資料表架構為：

|*TimelineColumn*|`dim1`|..|`dim_n`|`count`|`dcount`|
|---|---|---|---|---|---|
|類型： *TimelineColumn*的|..|..|..|long|long|


## <a name="examples"></a>範例

`dcounts`針對分析期間內的每一天，計算過去一周的使用者計數和。 

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