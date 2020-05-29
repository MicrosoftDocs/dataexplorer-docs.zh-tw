---
title: active_users_count 外掛程式-Azure 資料總管
description: 本文說明 Azure 資料總管中的 active_users_count 外掛程式。
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b40ca669df7671b1451166f6bfc1c7c680713166
ms.sourcegitcommit: 1f50c6688a2b8d8a3976c0cd0ef40cde2ef76749
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/29/2020
ms.locfileid: "84202954"
---
# <a name="active_users_count-plugin"></a>active_users_count 外掛程式

計算值的相異計數，其中每個值在回顧期間至少出現在最小週期數。

僅適用于計算「風扇」的相異計數，而不包含「非風扇」的外觀。 只有當使用者在回顧期間處於作用中狀態時，才會將其視為「風扇」。 回顧期間只會用來判斷使用者是否被視為（「 `active` 風扇」）。 匯總本身不會包含回顧視窗中的使用者。 相較之下， [sliding_window_counts](sliding-window-counts-plugin.md)匯總會在回顧期間的滑動視窗上執行。

```kusto
T | evaluate active_users_count(id, datetime_column, startofday(ago(30d)), startofday(now()), 7d, 1d, 2, 7d, dim1, dim2, dim3)
```

**語法**

*T* `| evaluate` `active_users_count(` *IdColumn* `,` *TimelineColumn* `,` *Start* `,` *End* `,` *LookbackWindow* `,` *Period* `,` *ActivePeriodsCount* `,` *Bin* `,` [*dim1* `,` *dim2* `,` ...]`)`

**引數**

* *T*：輸入表格式運算式。
* *IdColumn*：識別碼值代表使用者活動的資料行名稱。 
* *TimelineColumn*：代表時間軸的資料行名稱。
* *Start*：（選擇性）流量分析開始期間的值來進行純量。
* *End*：（選擇性）以 [分析結束期間] 的值為純量。
* *LookbackWindow*：定義檢查使用者外觀之期間的滑動時間範圍。 回顧期間從（[目前的外觀]-[回顧視窗]）開始，並于（[目前的外觀]）結束。 
* *Period*：純量常數 timespan 會計算為單一外觀（如果使用者出現在此時間範圍的至少不同 ActivePeriodsCount 中，則會被視為作用中。
* *ActivePeriodsCount*：用來決定使用者是否作用中的相異作用期間數目最少。 [作用中使用者] 是出現在 [使用中的期間] （等於或大於） [作用中] 的使用者人數。
* *Bin*：分析步驟期間的純量常數值。 可以是數值/日期時間/時間戳記值，或為的字串 `week` / `month` / `year` 。 所有期間都會是對應的[startofweek](startofweekfunction.md) / [startofmonth](startofmonthfunction.md) / [startofyear](startofyearfunction.md)函數。
* *dim1*， *dim2*，...：（選擇性）分割活動度量計算的維度資料行清單。

**傳回**

傳回資料表，其中包含下列期間出現在 ActivePeriodCounts 中之識別碼的相異計數值：回顧期間、每個時間軸期間，以及每個現有的維度組合。

輸出資料表架構為：

|*TimelineColumn*|dim1|..|dim_n|dcount_values|
|---|---|---|---|---|
|類型：從*TimelineColumn*|..|..|..|long|


**範例**

計算在過去八天的一段時間內，至少出現三個不同天數的每週相異使用者數目。 分析期間：2018年7月。

```kusto
let Start = datetime(2018-07-01);
let End = datetime(2018-07-31);
let LookbackWindow = 8d;
let Period = 1d;
let ActivePeriods = 3;
let Bin = 7d; 
let T =  datatable(User:string, Timestamp:datetime)
[
    "B",      datetime(2018-06-29),
    "B",      datetime(2018-06-30),
    "A",      datetime(2018-07-02),
    "B",      datetime(2018-07-04),
    "B",      datetime(2018-07-08),
    "A",      datetime(2018-07-10),
    "A",      datetime(2018-07-14),
    "A",      datetime(2018-07-17),
    "A",      datetime(2018-07-20),
    "B",      datetime(2018-07-24)
]; 
T | evaluate active_users_count(User, Timestamp, Start, End, LookbackWindow, Period, ActivePeriods, Bin)



```

|時間戳記|`dcount`|
|---|---|
|2018-07-01 00：00：00.0000000|1|
|2018-07-15 00：00：00.0000000|1|

如果使用者滿足下列兩個準則，則會將其視為作用中： 
* 使用者在至少三個不同的日子（Period = 1d，ActivePeriods = 3）中出現。
* 使用者已在8d 的回顧視窗中看到，並包含其目前的外觀。

在下圖中，此準則使用的唯一外觀為下列實例：在7/20 上的使用者 A 和7/4 上的使用者 B （請參閱上方的外掛程式結果）。 7/4 上的回顧視窗會包含使用者 B 的外觀，但不會針對6/29-30 的開始結束時間範圍。 

:::image type="content" source="images/queries/active-users-count.png" alt-text="作用中使用者計數範例":::
